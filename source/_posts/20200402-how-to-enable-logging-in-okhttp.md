---
title: How to enable logging in OkHttp ?
date: 2020-04-02 17:17:17
tags:
  - okhttp
categories: Android
---
Ever had some bad issues, while doing an API call in your android application? Or may be got some error which was making your API call unsuccessful.

#### What did you try?

First resort would have been to try API calls through some client that what was the error. Or may be something else you would have tried.

What if I told you that you could have chosen something else as well and that would have not required you to test APIs via some client.

You can use Interceptors in your android code to gets the logs their itself while getting an error. This sounds a better solution right?

Let's understand more about logging in Android. So, in this, we will discuss about [okhttp logging interceptor](https://github.com/square/okhttp/tree/master/okhttp-logging-interceptor) and how we can leverage it log our API calls.

#### What is OkHttp?

OkHttp is an interceptor which helps you to log your API calls. So, here an interceptor is more like a manager for an API call which helps you to monitor or perform certain action on your API calls.

Let us start by including it in our project, we will add the following in the build.gradle file:

```java
implementation "com.squareup.okhttp3:logging-interceptor:4.0.1"
```

and to start logging your API calls, we need to first make an API call,

```java
val client = OkHttpClient.Builder()
var request = Request.Builder()
        .url(/** YOUR API URL **/)
        .build()
```

Here, we create object for request and declared a variable client of OkHttpClient. Now, to perform API call, we will use the following to do it,

```java
client.newCall(request).enqueue(object :Callback{
    override fun onFailure(request: Request?, e: IOException?) {
        //API Call fails
    }

    override fun onResponse(response: Response?) {
        //Do something with response
    }

})
```

Now, we have performed the first API call. But here we would not see any logs coming up because we have not added any specific interceptor to log the calls. So, how can we log the response the call.

To, start logging we need to add interceptors in the above OkHttpClient.

And as we mentioned , interceptors are used to monitor the API call and it will print the logs which would get generated, in the Logcat of the console.

To add a Interceptor,

```java
val logging = HttpLoggingInterceptor()
logging.level = (HttpLoggingInterceptor.Level.BASIC)
```

we create and variable called logging of HttpLoggingInterceptor and set the level of logging to ***Basic.\*** Basic is the initial level in which you can just log reqest and the response of the API. We can also have ***NONE, HEADERS** and **BODY.***

> ***NONE : Logs Nothing.\***

> ***HEADERS : Logs Request and Response along with Header.\***

> ***BODY : Logs Request and Response along with header and if body present in the API call.\***

And, to add this interceptor to the client we use,

```java
val client = OkHttpClient.Builder()
client.addInterceptor(logging)
```

and, now when we call the API again we would start seeing the logs getting logged in the Logcat like the following,

```java
--> POST /greeting http/1.1 (3-byte body)

<-- 200 OK (22ms, 6-byte body)
```

#### **NOTE :**

\1. To add a custom TAG for your or logs to get generated, just add the following,

```java
val logging = HttpLoggingInterceptor(object : Logger() {
    fun log(message: String) {
        Log.d("YOUR TAG", message)
    }
})
```

and you will see the logs getting generated with the TAG you added.

\2. You can also create a custom Interceptor by extending the Interceptor class.

```java
class CustomInterceptor : Interceptor {
    override fun intercept(chain: Interceptor.Chain): Response {
        var request = chain.request()
        request = request.newBuilder()
                .build()
        return chain.proceed(request)
    }
}
```

and add it to the client using,

```java
val client = OkHttpClient.Builder()
client.addInterceptor(CustomInterceptor())
```

\3. To hide sensative information from the Logcat of Android we can use,

```java
logging.redactHeader("Authorization")
logging.redactHeader("Cookie")
```

Here, in above snippet *redactHeader* hides the sensitive information of Authorization and Cookie key.

These gets generated only in **HEADERS** and **BODY** level.

This is how we can log API calls being made in your Android application.