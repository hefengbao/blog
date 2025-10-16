---
title: 【转】使用 Moshi 解析 json
date: 2019-05-19 17:17:17
tags:
  - moshi
categories: Android
---
After using Gson for parsing JSON API responses in android I started using the [Moshi](https://github.com/square/moshi) JSON library created by Square as it targets the android platform, supports using Kotlin language features, is lightweight (with codegen) and has a simpler API.

As a consumer of a restful API with JSON responses it is preferable to handle any issues at the boundary of the data layer in your model rather than have them propagate into the app and have to track them down. Kotlin’s native language features are supported with Moshi to ensure the type safety of a Kotlin class used with adapters to parse JSON responses.

This article demonstrates how this is achieved from setting up Moshi, using it to parse JSON into a simple model object and implementing your own adapter.

### Set-up using codegen

Prior to version 1.6 Moshi was implemented using reflection with the kotlin-reflect artifact which at 2.5MB is quite heavyweight for an android app. Since version 1.6 you can also use codegen with the moshi-kotlin-codegen artifact. This is preferred for runtime performance and use of Kotlin language features in the generated adapters. (The only limitation compared with the reflection artifact is that it doesn’t support private and protected fields). In this article I’ll use codegen which requires this gradle configuration:

```groovy
apply plugin: 'kotlin-kapt'
...
dependencies {
    implementation 'com.squareup.moshi:moshi:1.8.0'
    kapt 'com.squareup.moshi:moshi-kotlin-codegen:1.8.0'
}
```

### Parsing JSON into a simple model object

Consider this JSON and Kotlin data class for a movie:

```kotlin
{
  "vote_count": 2026,
  "id": 19404,
  "title": "Example Movie",
  "image_path": "/example-movie-image.jpg",
  "overview": "Overview of example movie"
}
@JsonClass(generateAdapter = true)
data class Movie (
    @Json(name = "vote_count") val voteCount: Int = -1,
    val id: Int,
    val title: String,
    @Json(name = "image_path") val imagePath: String,
    val overview: String
)
```

There are a couple of things to highlight here. We’ve annotated the class with`@JsonClass(generateAdapter = true)` which will generate a JsonAdapter to handle serializing/deserializing to and from JSON of the specified type.

The`@Json(name = “value”)` annotation defines the JSON key name for serialisation and the property to set the value on with deserialization.

> This annotation works similarly to `*@SerializedName(“some_json_key”)*` in Gson and `*@JsonProperty("some_json_key")*` in Jackson.

To hook it all up and parse the json to the data class you need to create a Moshi object, create the adapter instance and then pass the JSON to the adapter:

```Kotlin
val moshi: Moshi = Moshi.Builder().build()
val adapter: JsonAdapter<Movie> = moshi.adapter(Movie::class.java)
val movie = adapter.fromJson(moviesJson))
```

#### Moshi handling of null and absent JSON fields

If the JSON response changes and sets a null field in the JSON then the adapter will fail respecting the non null reference of a `val` property in the data class and throw a clear exception.

```bash
{
  "vote_count": 2026,
  "id": 19404,
  "title": "Example Movie",
  "image_path": "/example-movie-image.jpg",
  "overview": null
}
com.squareup.moshi.JsonDataException: Non-null value 'overview' was null at $[0].overview
```

If the JSON response has an absent field then again the reason for the thrown exception is clear:

```bash
{
  "vote_count": 2026,
  "id": 19404,
  "title": "Example Movie",
  "image_path": "/example-movie-image.jpg"
}
com.squareup.moshi.JsonDataException: Required property ‘overview’ missing at $[1]
```

Default properties work just as expected setting the voteCount to -1 if it is absent in the consumed JSON. If the property is nullable, however, and null is set in the the JSON then the null value takes precedence. So `@Json(name = "vote_count") val voteCount: Int? = -1` will set `voteCount` to null if "`vote_count": null` is in the JSON.

### Creating your own JSON adapter

There will be times when you don’t want a JSON key to map directly to a Kotlin property and you can create your own custom adapter to change the parsing.

Take a look at the updated JSON and corresponding model where genres the movie belongs to have been introduced referenced by genre id:

```kotlin
{
  "vote_count": 2026,
  "id": 19404,
  "title": "Example Movie",
  "genre_ids": [
    35,
    18,
    10749
  ],
  "overview": "Overview of example movie"
}
@JsonClass(generateAdapter = true)
data class Movie (
    @Json(name = "vote_count") val voteCount: Int = -1,
    val id: Int,
    val title: String,
    @Json(name = "genre_ids") val genres: List<Genre>,
    val overview: String
)
data class Genre(val id: Int, val name: String)
```

As you haven’t specified how to map the ids to create a genre the parsing fails with `com.squareup.moshi.JsonDataException: Expected BEGIN_OBJECT but was NUMBER at path $[0].genre_ids[0]` From the model you can see a list of Genre is expected in the JSON but a list of NUMBER is found

To do this mapping you need to create your own adapter and register it when you create the moshi instance:

```kotlin
//Movie genres from the The movie database https://www.themoviedb.org/
class GenreAdapter  {

        @ToJson
        fun toJson(genres: List<Genre>): List<Int> {
            return genres.map { genre -> genre.id}
        }

        @FromJson
        fun fromJson(genreId: Int): Genre {

            when (genreId) {
                28 -> return Genre(28, "Action")
                12 -> return Genre(12, "Adventure")
                16 -> return Genre(16, "Animation")
                35 -> return Genre(35, "Comedy")
                80 -> return Genre(80, "Crime")
                99 -> return Genre(99, "Documentary")
                18 -> return Genre(18, "Drama")
                10751 -> return Genre(10751, "Family")
                14 -> return Genre(14, "Fantasy")
                36 -> return Genre(36, "History")
                27 -> return Genre(27, "Horror")
                10402 -> return Genre(10402, "Music")
                10749 -> return Genre(10749, "Romance")
                9648 -> return Genre(9648, "Mystery")
                878 -> return Genre(878, "Science Fiction")
                10770 -> return Genre(10770, "TV Movie")
                53 -> return Genre(53, "Mystery")
                10752 -> return Genre(10752, "War")
                37 -> return Genre(37, "Western")
                else -> throw JsonDataException("unknown genre id: $genreId")
            }
        }
    }
val moshi: Moshi = Moshi.Builder().add(GenreAdapter()).build()
val adapter: JsonAdapter<Movie> = moshi.adapter(Movie::class.java)
val movie = adapter.fromJson(moviesJson))
```

The mapping is now handled and you can create Genres from the ids in the JSON. You could do this mapping after consuming the JSON by specifying `@Json(name = “genre_ids”) val genres: List<Int>`, but it’s better to use Moshi to do this when you ingest the content as you will discover any issues sooner.

#### Further Reading on Moshi Codegen

[Zac Sweers](https://medium.com/@ZacSweers) [Exploring Moshi’s Kotlin Codegen](https://medium.com/@ZacSweers/exploring-moshis-kotlin-code-gen-dec09d72de5e)

[Christophe Beyls](https://medium.com/@BladeCoder) [Advanced JSON parsing techniques using Moshi and Kotlin](https://medium.com/@BladeCoder/advanced-json-parsing-techniques-using-moshi-and-kotlin-daf56a7b963d)

### Wrapping Up

Gists for implementing the examples above in codegen and reflection are available here:

[Moshi Kotlin Reflection Example with Custom Adapter](https://gist.github.com/alexforrester/e1b1c50bbddb5036fdb02d52d445cc84)

[Moshi Kotlin Codegen Example with Custom Adapter](https://gist.github.com/alexforrester/5c96ace4227916fb456ff49a16ef025d)

Sample projects showing full code of the examples with data from [The Movie Database](https://www.themoviedb.org/) using both moshi codegen and moshi reflection with architecture components are available in the repo below.

[**alexforrester/android-moshi**
*dog: Uses Android Architecture Components to illustrate moshi parsing of json into kotlin with different…*github.com](https://github.com/alexforrester/android-moshi)

I hope this article helps you get up-to-speed with Moshi if you are considering using it for your android project. Comments are welcome. Happy parsing!



来源：

https://proandroiddev.com/getting-started-using-moshi-for-json-parsing-with-kotlin-5a460bf3935a
