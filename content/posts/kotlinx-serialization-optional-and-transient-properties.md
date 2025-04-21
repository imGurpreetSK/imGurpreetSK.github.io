+++
date = '2020-05-03T02:23:11+05:30'
draft = false
title = 'Kotlinx Serialization - optional and transient properties'
author = "Gurpreet"
tags = ["kotlin", "migrated-from-medium", "kotlinx-serialization"]
+++

When interacting with a Rest API, it’s common to have some optional properties in the response. These properties might or might not be present at runtime. Let’s consider we updated out User data object to contain a required, non-nullable and unique id and age, which is an optional, nullable property.

```kotlin
@Serializable
data class User(
    val id: String,
    val name: String,
    val age: Int? = null
)
```

(You can see all these examples as a series of unit tests here in [this gist](https://gist.github.com/GurpreetSK95/f2752ac9984cb6fc122765762fc8df31).)

#### Handling optional values

Kotlin’s features — especially its nullable types and default parameter values prove to be great when dealing with JSON. You don’t need to do anything special to handle optional values when working with Kotlin serialization.

  All properties with default values are considered optional.

In our case, age is an optional property with null as its default value. the property will be serialized if it’s found in JSON, else the serialized object will have age as null, unlike for required objects where a MissingFieldException is thrown.

#### Handling Transient values

Transient properties are those which you never want to take part in the entire serialization process. An example can be adding an isAdult property to our User object. The value is calculated on client side and will never be returned by the Rest API, hence it never needs to take part in the entire process.

Such properties can be marked using @Transient annotation. From the docs:

  Marking a property Transient makes it invisible for whole serialization framework.

This also means that transient properties must have default values, as they can’t be initialized otherwise.

-----

That’s all for this one folks! Hope this was helpful and you learnt something new.

This article is second of a series of articles I’m writing on Kotlin serialization while also learning more about the same. Stay tuned, stay inside and keep reading!

Hope it helps!

*Originally posted on Medium [link](https://medium.com/@gurpreetsk/kotlin-serialization-optional-and-transient-properties-b3da3247e112)*
