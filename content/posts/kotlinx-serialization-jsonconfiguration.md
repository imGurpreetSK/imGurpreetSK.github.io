+++
date = '2020-05-13T02:23:11+05:30'
draft = false
title = 'Kotlinx Serialization - JsonConfiguration'
author = "Gurpreet"
tags = ["kotlin", "migrated-from-medium", "kotlinx-serialization"]
+++

<br>

![Header](/assets/posts/kotlinx-serialization/banner-kotlin-mobile.webp)

<br>

In the previous posts we learned about the basics of kotlin serialization. We instantiated a Json object, passing it a configuration and used it to perform serialization and deserialization operations. This post is about that configuration, called JsonConfiguration — why is it required and what role does it play in the process.

The JsonConfiguration class is provided by the serialization runtime library and allows us to customise JSON behaviour as per our requirements. It is a simple data class with various properties and two out-of-the-box configurations:

1. Stable — adheres to the [JSON specification restrictions (RFC-4627)](https://datatracker.ietf.org/doc/html/rfc4627) and is guaranteed to preserve its semantics between library releases.
2. Default — This configuration is what the library recommends for JSON format. It is not guaranteed to preserve its semantics and might change as the runtime library evolves until it hits a version 1.0.0.

Being a data class, you can also copy either of these configurations and make changes as per your requirements, or create a configuration from scratch if that suits your use-case better.

Customisable properties

Following are the various available options for JSON-specific behaviour customisation (along with certain examples):

#### 1. encodeDefaults

Denotes whether the serialized JSON contains properties with default values or not.

#### 2. ignoreUnknownKeys

If false, any unknown properties in JSON will cause the deserialization to fail. This flag is true by default in stable configuration, however it’s a wise choice to keep it false to maintain backward compatibility while interacting with frequently changing objects or external APIs.

#### 3. isLenient

If set to true, removes JSON specification restrictions and allows parsing of malformed JSON objects. In following example, notice how the input JSON is unquoted, yet the parsing succeeds.
However, the parsing will still fail if any string values has spaces or delimiters.

#### 4. serializeSpecialFloatingPointValues

As the name suggests, allows serialization of special floating point values in the JSON. These values are Infinity, -Infinity and NaN. As a note, you should give a thought why these values are required to be parse at first place.

#### 5. unquotedPrint

If true, the output JSON does not have quoted strings. This option is not recommended to be used except for rare debugging purposes as the parsed JSON is invalid.
If any of the values in deserialized output has a string with spaces or delimiters, it will always be quoted.

#### 6. prettyPrint

If set to true, the output JSON will be pretty-printed.

#### 7. indents

Specifies the indent to use while parsing and pretty-printing JSON. Pretty printing has to be enabled while providing a custom indent, else the serialization will fail with an IllegalArgumentException.
The next few properties we’ll talk about allow transformation of JSON from one form to another to support certain features which might be legacy or unsupported by default.

#### 8. allowStructuredMapKeys

In JSON, only primitives can be used as keys. The serialization will fail with the following error if we have some non-primitive type representing a key in the object to be parsed.

```
    Value of type 'kotlin.collections.LinkedHashMap' can't be used in JSON as a key in the map.
    It should have either primitive or enum kind, but its kind is 'MAP.'
```

If you need to serialize a JSON with say a map as a key, you can enable allowStructuredMapKeys flag. This will transform the serialized JSON from a map of key-value pair to a flat list of keys and values.

#### 9. useArrayPolymorphism

This flag works only if you’re performing polymorphic serialization ([this article](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/polymorphism.md#multiplatform-polymorphic-serialization) and the [class docs](https://github.com/Kotlin/kotlinx.serialization/blob/master/runtime/commonMain/src/kotlinx/serialization/Polymorphic.kt#L23) do a really good job of explaining what polymorphic serialization is). This flag causes the serialized JSON to be produced in the legacy array format instead of the object format.

If array polymorphism is not used, the following JSON is obtained:

```
[{"type":"Dog","lovesTreats":true},{"type":"Cat","lovesSleeping":true}]
```

  The ‘type’ in above JSON is called the class discriminator, and can be customised too!

#### 10. classDiscriminator

Use this property to give a custom name to the JSON property describing the class. In the above example, if we pass classDiscriminator=”class” to the JsonConfiguration, the following JSON would be obtained

```
[{"class":"Dog","lovesTreats":true},{"class":"Cat","lovesSleeping":true}]
```

-----

Hope this was helpful and you learnt something new. If you haven’t gone through the previous posts, you should take a look at those: 

1. [Getting started with Kotlin Serialization](/posts/kotlinx-serialization-getting-started/)
2. [Kotlin serialization — Optional and Transient properties](/posts/kotlinx-serialization-optional-and-transient-properties/)

This article is third in a series of articles I’m writing on Kotlin serialization while also learning more about the same. Stay tuned and keep learning!

Hope it helps!

*Originally posted on Medium [link](https://medium.com/@gurpreetsk/kotlin-serialization-optional-and-transient-properties-b3da3247e112)*
