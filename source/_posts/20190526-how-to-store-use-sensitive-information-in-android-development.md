---
title: 【转】How To Store Use Sensitive Information In Android Development
date: 2019-05-26 17:17:17
tags:
  - android
categories: Android
---
## Step 1 : Consolidate sensitive information in file(s) and ignore from the repo

The first naive thought is just to write all the sensitive information in a file and don’t commit it. List the file in .**gitignore** so that nobody accidentally commits it.

For example, we can store all API keys/secrets in **app/keys.properties:**

```
TMDB_API_KEY=XXXXYYYYZZZZ
AZURE_API_KEY=AAAABBBBCCCC
AWS_API_KEY=LLLLMMMMNNNNN
...
```

**.gitignore** would look like this.

```
...
# Files with sensitive info
app/keys.properties
```

Now, how can we use the `TMDB_API_KEY` from the main Java/Kotlin code? We can let gradle to generate some code for you.

In your **app/build.gradle**, you can write something like this.

```
android {
    ...
    buildTypes {
        release {
            ...
            buildConfigField("String", "TMDB_API_KEY", "\"" + getTmdbApiKey() + "\"")
        }
        debug {
            buildConfigField("String", "TMDB_API_KEY", "\"" + getTmdbApiKey() + "\"")
        }
    }
}
def getTmdbApiKey() {
    def keysFile = file("keys.properties")
    def keysProperties = new Properties()
    keysProperties.load(new FileInputStream(keysFile))
    def tmdbApiKey = keysProperties['TMDB_API_KEY']
    return tmdbApiKey
}
```

Basically, you read the sensitive file and get the key value pairs, feed the corresponding API key value you want into the magic function `**buildConfigField**`. This function is going to create a static field with provided parameters after gradle sync. The auto generated`**BuildConfig.java**` will look like this.

```
public final class BuildConfig {
  ...
  public static final String TMDB_API_KEY = "XXXXYYYYZZZZ";
}
```

Now you can access this global variable from anywhere in the app. If you are using Retrofit to generate API client code, you can do something like this.

```
interface TMDBService {
    @GET("3/movie/now_playing")
    fun nowplaying(@Query("page") page: Int,
                   @Query("api_key") apiKey: String = BuildConfig.TMDB_API_KEY
    ): Flowable<MoviesPage>
}
```

Now, this is awesome. Your sensitive information is consolidated in a single place (keys.properties in this example) and is not going to be easily pushed to any repository by accident. You can pass the file to a new team mate as he/she joins. Your team can probably manage the file in 1Password or a dedicated private repository.

> NOTE: Obviously, it is cumbersome and has drawbacks to pass keys.properties to the new team members outside of repository. I will explain the solution in the Step 3.

## Step 2: Make CI/CD work with environment variable

With the previous step, we successfully skipped sensitive information to be included in the repository. However, that will cause a problem to the CI/CD system. Remember the `build.gradle` we wrote above.

```
android {
    ...
    buildTypes {
        release {
            ...
            buildConfigField("String", "TMDB_API_KEY", "\"" + getTmdbApiKey() + "\"")
        }
        debug {
            buildConfigField("String", "TMDB_API_KEY", "\"" + getTmdbApiKey() + "\"")
        }
    }
}
def getTmdbApiKey() {
    def keysFile = file("keys.properties")
    def keysProperties = new Properties()
    keysProperties.load(new FileInputStream(keysFile))
    def tmdbApiKey = keysProperties['TMDB_API_KEY']
    return tmdbApiKey
}
```

The gradle sync on CI/CD server would fail because it cannot find `keys.properties`, because it was omitted from the repository.

You might have guessed : here comes good old environment variables. Every CI/CD system has an entry for environment variables. This screen is from Circle CI for example.

![](images/2019052601.png)

Environment Variables Screen for Circle CI

You can just “Add Variable” for`TMDB_API_KEY` in this case.

And of course, we have to modify our gradle script to look at environment variables in addition to the file(s).

```
def getTmdbApiKey() {
    def tmdbApiKey = System.getenv("TMDB_API_KEY")
    if (tmdbApiKey == null || tmdbApiKey.length() == 0) {
        def keysFile = file("keys.properties")
        def keysProperties = new Properties()        keysProperties.load(new FileInputStream(keysFile))
        tmdbApiKey = keysProperties['TMDB_API_KEY']
        if (tmdbApiKey == null) {
            logger.error("You need to either place appropriate keys.properties or set proper environment variables for API key")
        }
    }
    return tmdbApiKey
}
```

Now, in the modified function above, we first look at the environment variable **TMDB_API_KEY**. If the environment variable is not defined, we will read from the file as before. As long as CI/CD has that environment variable properly set, it can successfully generate a build.

## Step 3 : Encrypt the sensitive keys and push to the repository

By consolidating sensitive key information in file(s) and configuring gradle scripts properly, you can set up your Android project to hide sensitive information from the repository.

With this approach, however, we have to have another storage to just store the sensitive information. If we use some password manager (e.g., 1Password), then you cannot manage versions of the sensitive file. You can work this around by setting up a dedicated private repository, but it’s a bit cumbersome that you have to pull/copy the file from a separate repository.

Here comes [git-secret](https://git-secret.io/). It uses GPG under the hood and allows easy interface for the repository manager to encrypt secret files so that only certain developers are allowed to decrypt those.

#### Initial Setup

The repository manager needs to put the repository under control of `git-secret` and specify file(s) to encrypt. This only needs to happen once. Following commands should be executed from the top level directory of the repository.

```
% git secret init                    // You only need to do this once
% git secret add app/keys.properties // You only need to do this once per file
```

### Developer passes the public key to the repository manager

The repository manager needs to obtain GPG public key from individual developers. Individual developer can follow [this link](https://help.github.com/en/articles/generating-a-new-gpg-key) to create and export GPG public key. One important thing for the developer is that he/she **SHOULD NEVER FORGET THE PASSPHRASE HE/SHE SET HERE.** It’s quite cumbersome to recover from that situation, and I bet you want to avoid facing unhappy devops or a tech lead.

#### Repository manager encrypts secret file(s) using the public keys.

On receiving the public key, the repository manager runs the following command.

```
% gpg --import IMPORTED_PUBLIC_KEY.txt
```

Now, repository managers machine can encrypt any file using developers’ GPG public keys. The repo manager can then type following command to grant access for the developer to the respository.

```
% git secret tell xxx@yyy.com
```

The Email is the one associated to the imported public key. You can probably check the email via `**pgp --list-keys**` after doing the import.

Now, you can issue the magic command:

```
% git secret hide
```

This will create an encrypted file **app/keys.properties.secret** out of **app/keys.properties**, using the public keys registered to the machine. The repository manager can then push the encrypted file to the repository.

### Any developer can decrypt secret file(s) with a simple command.

```
% git secret reveal
```

The above command lets you regenerate **app/keys.properties** out of **app/keys.properties.secret** as long as the steps above have been successfully completed. 💥💥💥

The initial setup is a little bit involved process, but it becomes very simple after that. Every time a new developer comes in, he/she needs to send the public key to the repository manager, where he/she add the user and re-encrypt the file(s). The new member can then just pull the repository and enter `**git secret reveal**`. It is much better than searching the file in some other storage, possibly without knowing whether that is the latest version or not.

## Summary

I have introduced the 3 steps to share sensitive information without pushing it to the repository. The 3rd step is probably optional at this point. It requires some effort of setting up, and also has some shortcomings like follows:

- You always need to run the `**git secret hide**` command in a machine that has everybody’s public keys.
- When the secret file(s) is updated and pushed to the repo, developers should not forget to run `**git secret reveal**`. Otherwise, you will keep running the app based on old info.

However, the above points are limitations of current toolset and hopefully will get better soon. Overall, the trend is heading to the direction to commit encrypted version of secret files in the same repository. Another tool that fills the same purpose is [git-crypt.](https://github.com/AGWA/git-crypt)

First 2 steps, at this moment, is probably something we should follow in every Android project. Once CI systems offer reliable support for `git-secret` or `git-crypt`, we may just implement the decryption process on the CI and skip Step2 (getting info through environment variable)



来源：

<https://medium.com/@yfujiki/how-to-store-use-sensitive-information-in-android-development-bc352892ece7>