+++
date = '2019-03-10T02:23:11+05:30'
draft = false
title = 'Kotlin Sequences'
author = "Gurpreet"
tags = ["android", "migrated-from-medium"]
+++

Transforming, filtering, manipulating and operating on data is what every developer does almost every day at their jobs. If you’re using Kotlin for writing your applications, you might have used map or filter operator to transform a collection from one form to another.

Along with the kotlin.collections package, Kotlin also provides a way to lazily represent and evaluate a collection of data. This is accomplished using Sequences.

### *Sequences are a collection of possibly infinite data, ordered in a certain way.*

Sequences are a part of the language standard library and allow lazy evaluation of large amount of data (in millions), as opposed to collections, which compute and evaluate operations on a data set eagerly.

Looking at the source, it’s pretty clear that the Sequence interface is similar to Iterable in definition:

```kotlin
public interface Sequence<out T> {
  operator fun iterator(): Iterator<T>
}

public interface Iterable<out T> {
  operator fun iterator(): Iterator<T>
}
```

Sequences maintain the same API as collections. The difference comes in the way they are implemented. Let’s take an example to understand.

Let’s suppose we have a list of customers, and each customer has a recorded unique id, first name, last name and age.

```kotlin
private const val UNDEFINED = -1

data class Customer(
    val firstName: String,
    val lastName: String? = null,
    val age: Int = UNDEFINED
)
```

Next, let’s define some data to demonstrate:

```kotlin
private fun getCustomers(): List<Customer> {
  return listOf(
      Customer("Ragunath", "Jawahar", 31),
      Customer("Dinesh", "Raja", 28),
      Customer("Donn", "Felker", 42),
      Customer("RMK", ""),
      Customer("Omar")
  )
}
```

Now that we’re done with the basic setup, let’s define the problem statement. Let’s say we need to get the first names of all the customers whose age is not UNDEFINED and whose last name isn’t null. We can do this using the filter and map operators provided by the Collections API:

```kotlin
fun main(args: Array<String>) {
  val result = getCustomers()
      .filter { it.lastName != null }
      .filter { it.age != UNDEFINED }
      .find   { it.age == 31 }

  println(result)
}
```

Here, we first filter the customers whose last name is null, then filter customers whose age is UNDEFINED. Finally, we map the remaining customers’ first name and collect them in a list. If you run the above example, the output would be as expected:

```kotlin
Customer(firstName=Ragunath, lastName=Jawahar, age=31)
```

But what were the operations leading to this output? If we put an onEach operator after each operation and print the value input to the function, the output would be something on the lines of:

```
filter lastName: Customer(firstName=Ragunath, lastName=Jawahar, age=31)
filter lastName: Customer(firstName=Dinesh, lastName=Raja, age=28)
filter lastName: Customer(firstName=Donn, lastName=Felker, age=42)
filter lastName: Customer(firstName=RMK, lastName=, age=0)filter age: Customer(firstName=Ragunath, lastName=Jawahar, age=31)
filter age: Customer(firstName=Dinesh, lastName=Raja, age=28)
filter age: Customer(firstName=Donn, lastName=Felker, age=42)Customer(firstName=Ragunath, lastName=Jawahar, age=31)
```

The result above shows Eager evaluation. The filter operation is applied on the entire collection (input), and an intermediate collection is formed. This intermediate collection is then passed down the chain to the next filter, which repeats the process and passes another collection to the find function, which returns the result.

This approach has two drawbacks:

1. Creation of varying number of intermediate collections, depending on the number of operations to be performed, and
2. Processing of all elements in the collection, even if it is not required.

This hardly makes any difference in this tiny example, or even for any relatively larger use case for that matter (thanks to judicious use of inlined functions in stdlib and good garbage collection support by JVM),

But think about a case where the input data is in order of Millions, or virtually infinite? Wouldn’t it be nice if we could compute on data one after another, and bail as soon as the required condition is met?

This is exactly what sequences do! Here’s what the code above would look like when using sequences:

```kotlin
fun main(args: Array<String>) {
  val result = getCustomers()
      .asSequence() // Creating a sequence
      .filter { it.lastName != null }
      .filter { it.age != UNDEFINED }
      .find   { it.age == 31 } // Terminal operation

  println(result)
}
```

We create a sequence by wrapping the existing collection, and returning its elements when being iterated. If we apply the onEach function here and check the output, it’ll look like

```
filter lastName: Customer(firstName=Ragunath, lastName=Jawahar, age=31)filter age: Customer(firstName=Ragunath, lastName=Jawahar, age=31)Customer(firstName=Ragunath, lastName=Jawahar, age=31)
```

Once a terminal operation is encountered, computations are carried out one after another, and as soon as a result is found, execution is stopped. This is why sequences are called lazy.

A visual comparison of how collections and sequences work can be:

![kotlin-sequences](/assets/posts/kotlin-sequences-1.webp)

-----

You might have noticed the term ‘Terminal’ operation. If we remove it, no computation will be carried out. This is because operations on a sequence are divided into two categories: Intermediate and Terminal.

Intermediate operations return a sequence which knows how to transform elements of the original sequence. These operations are always lazy, and are postponed until a terminal operation is performed. The terminal operation performs all the postponed operations and returns a some value as a result.

```kotlin
getCustomers()
      .asSequence() // Creating a sequence
      .filter { it.lastName != null } // Intermediate operation
      .filter { it.age != UNDEFINED } // Intermediate operation
      .find   { it.age == 31 } // Terminal operation
```

-----

“Okay, this looks interesting. Let’s use sequences EVERYWHERE!”.

Nope. Please don’t.

As a rule of thumb, a sequence should be used at places where a chain of operations are being performed on a very large (or possibly infinite) collection of data, and/or there is a need to break out of the computation as soon as an element matching the predicate is found. As mentioned earlier, eager operations on regular collections are highly optimised and efficient in kotlin and should fare you well for most cases.

Another thing to remember is that the elements of a sequence cannot be accessed as simply as elements of a collection. For example, the following snippet will produce a compile-time error:

```kotlin
fun main() {
    val sequence = (1..10)
     .asSequence()
     .map { it * 3 }    println(sequence[1]) // Error: Unresolved Reference. None of the                                              following candidate is applicable because of receiver type mismatch.
}
```

This is because the accessed element might not have been processed at all! (remember lazy evaluation for sequences?)

Hope this helps!

*Originally posted on Medium [link](https://medium.com/android-news/kotlin-sequences-ac6dc7c883d3)*
