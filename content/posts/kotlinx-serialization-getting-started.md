+++
date = '2020-05-03T02:23:11+05:30'
draft = false
title = 'Kotlinx Serialization - Getting Started'
author = "Gurpreet"
tags = ["kotlin", "migrated-from-medium", "kotlinx-serialization"]
+++

<br>

![Header](/assets/posts/kotlinx-serialization/banner-kotlin-mobile.webp)

<br>

[Kotlin serialization](https://github.com/Kotlin/kotlinx.serialization) is kotlin-first, compile-time, type-safe, reflection-free and completely multi-platform ready [serialization](https://en.wikipedia.org/wiki/Serialization) mechanism to convert kotlin objects into data formats like JSON or Protobuf and vice-versa.
It is comprised of a compiler plugin which is used to generate code for serializable classes and a runtime library, containing core classes and primitives for serialization & IO, among some other things.

![current-library-runtime-structure](/assets/posts/kotlinx-serialization/kotlinx-serialization-current-structure.webp)
<div style="text-align:center;">Kotlinx serialization runtime project structure</div>
<br/>

While JSON format is supported out of the box by the library, there’s first-class support for other formats like Protobuf & CBOR, with the option to extend the functionality by writing your own library to support any other format.

The effort was [first announced](https://discuss.kotlinlang.org/t/kotlin-serialization/2063) in October 2016, where [@elizarov](https://x.com/elizarov) posted about the team’s efforts on “working on a generic Kotlin Serialization facility for some future release of Kotlin”. You can know more about the design, goals and core concepts of kotlin serialization by going through [this KEEP](https://github.com/Kotlin/KEEP/blob/serialization/proposals/extensions/serialization.md).

### Why?

Code sharing between platforms isn’t easy. Each platform you want to support has different ways of handling different things — IO, transformations, threading, serialization to name a few. There already are various libraries out there which support kotlin serialization, even generate kotlin code if you want to, Square’s Moshi being probably being the most famous one.

However, all of these work only on JVM and don’t support other platforms. That’s why a new mechanism is required — one that supports all the platforms.

### Setup

Please refer to the official guide to setup. It’s simple, all you’ll need to do is :

1. Apply the serialization plugin, and
2. Add dependency on the runtime library in your build.gradle file.

    *You’ll need to be on Kotlin version 1.3.70 or higher to use runtime library version 0.20.0, which is the latest release at time of writing this article.*

-----

## Learning by examples

(You can see all these examples as a series of unit tests here in [this gist](https://gist.github.com/GurpreetSK95/d227315561c470f12f488f03ec0007a9).)

Let’s go through some of the basic cases you’re likely to encounter if you plan on using kotlin serialization in one of your projects.

### 1. Parse JSON object to Kotlin object

Suppose you want to convert a JSON object to a kotlin object. For this, you’ll need to specify two things:

1. The object to which you’re trying to parse the JSON to, and
2. The behaviour of the JSON format you’re trying to parse

Let’s say we want to parse JSON containing user’s information — just his name for now. The JSON looks like `{“name”: “Gurpreet”}`.

In Kotlin, this can be represented as a data class User with a name property.

    data class User(val name: String)

However, this class can’t be used just yet for serialization purposes as we haven’t told the compiler to generate serialization code. This is where @Serializable annotation comes to use. This annotation instructs the compiler to generate code required for performing serialization for our class.

```kotlin
@Serializable
data class User(val name: String)
```

That it! It’s as simple as that. Now the compiler can understand and serialize User objects.

Next, we need to define JSON format behaviour we’re trying to pass. We do this by specifying a Json instance. From the docs, this class is

```
The main entry point to work with JSON serialization.
It is typically used by constructing an application-specific instance, with configured json-specific behaviour.
```

We’ll also need to defined what type of JSON we’re expecting. We define this using JsonConfiguration. This class helps us configure the behaviour for Json object. We’ll discuss more about this class a later, but for now, it’s good to know that it provides two ready to use configurations for us to use — Stable and Default. We’ll use the Stable configuration, which adheres to the [JSON specification restriction](https://www.ietf.org/rfc/rfc4627.txt) and is provided by the runtime library.

Now that we’ve gathered information about what we need, we can take a look at code to serialize the JSON to an object. It’s as simple as calling parse on the Json instance we have created. You have to pass the serializer and the JSON you want to parse.

```kotlin
Json(JsonConfiguration.Stable)
    .parse(User.serializer(), """{"name": "Gurpreet"}""")// Output:
// User("Gurpreet")
```

The above code reads *“Parse this input JSON to a User object using the serializer provided. The JSON provided to the parse is ‘stable’ i.e., adheres to the standards”*.

#### 2. Convert Kotlin object to JSON

To deserialize an object (i.e. to convert it to JSON), stringify method can be used. Unlike parse, this method takes the kotlin object to deserialize as an argument.

The following code snippet shows how we can convert User object to JSON.

```kotlin
Json(JsonConfiguration.Stable)
    .stringify(User.serializer(), User("Gurpreet"))// Output:
// {"name":"Gurpreet"}
```

### 3. Parsing an object containing a list of objects

Let’s say you have to parse a list of contacts instead of a single object and the JSON looks something like

```kotlin
val jsonToParse = """{
  "result": [
    {
      "name": "Contact 1"
    },
    {
      "name": "Contact 2"
    },
    {
      "name": "Contact 3"
    }
  ]
}"""
```

Looking at the structure, we can infer that we need an object which contains a list of User objects. We define it as follows:

```kotlin
@Serializable
data class User(val name: String)@Serializable
data class Contacts(val result: List<User>)
```

Notice that we’ve marked both the classes as @Serializable. If we don’t mark User as @Serializable, the compiler will report an error which says Serializer has not been found for type 'User’, essentially meaning that it doesn’t know how to serialize the type.

It’s pretty straight-forward from here. We need to serialize the JSON to Contactsobject, similar to how we parsed a User object before

```kotlin
Json(JsonConfiguration.Stable)
    .parse(Contacts.serializer(), jsonToParse)
// Output:
// Contacts(listOf(
//    User("Contact 1"),
//    User("Contact 2"),
//    User("Contact 3")
// ))
```

Note that you can obtain the JSON back by using stringify in this case too.

### 4. Parsing a list of objects

If you noticed, all the types we have talked about till now are user defined types. We learnt about how to serialize and deserialize them. But what about the cases where we want to operate on a collection of objects, for example, a list of Users? We can’t apply @Serializable on a class we don’t own 🤷‍♂.

For this, the team at Jetbrains has got us covered 🎊. Serializers to parse widely used collections — Lists, Maps and Sets — are provided by the runtime library out of the box . To parse a list of contacts as shown below, we can use ListSerializer. All we need to do is tell what type of objects the list comprises of, which is User in our case.

```kotlin
val listOfContacts = """[
  {
    "name": "Contact 1"
  },
  {
    "name": "Contact 2"
  },
  {
    "name": "Contact 3"
  }
]"""
```

To serialize the JSON, we do

```kotlin
Json(JsonConfiguration.Stable)
    .parse(
        ListSerializer(User.serializer()),
        listOfContacts
    )
// Output:
// listOf(
//   User("Contact 1"),
//   User("Contact 2"),
//   User("Contact 3")
// )
```

Similarly, we can deserialize the list to JSON by

```kotlin
Json(JsonConfiguration.Stable)
    .stringify(
        ListSerializer(User.serializer()),
        listOf(
            User("Contact 1"),
            User("Contact 2"),
            User("Contact 3")
        )
    )
// Output:
// [{"name": "Contact 1"},{"name":"Contact 2"},{"name":"Contact 3"}]
```

You can also serialize Maps by using MapSerializerand Sets by using SetSerializer in a similar way.

-----

Hope this was helpful and you learnt something new. You can read [the continuation article](/posts/kotlinx-serialization-optional-and-transient-properties) to this which talks about handling optional and transient properties.

This article is first of a series of articles I’m writing on Kotlin serialization while also learning more about the same. Stay tuned, stay inside and keep reading!

Hope it helps!

*Originally posted on Medium [link](https://medium.com/@gurpreetsk/getting-started-with-kotlin-serialization-3315c59bafb2)*
