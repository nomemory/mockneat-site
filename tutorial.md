---
layout: page
title: tutorial
permalink: /tutorial/
---

[Creating (or not) `MockNeat` objects](#creating-or-not-mockneat-objects)

# Creating (or not) `MockNeat` objects

The `MockNeat` class is the "entry-point" of the library.

By default the library creates 3 reusable `MockNeat` instances, each wrapping a different type of `Random` generators ([ThreadLocalRandom](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadLocalRandom.html), [SecureRandom](http://docs.oracle.com/javase/8/docs/api/java/security/SecureRandom.html) or [Ol' Random](https://docs.oracle.com/javase/8/docs/api/java/util/Random.html)):

```java
MockNeat mock1 = MockNeat.threadLocal(); // recommended
MockNeat mock2 = MockNeat.secure();
MockNeat mock3 = MockNeat.old();
```

In most of the use-cases it's recommended to (re)use the `MockNeat.threadLocal()` object. After the instance is referenced, the library can be used like this:

```java
import net.andreinc.mockneat.MockNeat;

...

MockNeat mock = MockNeat.threadLocal();

// Gets a random int, long and string
int x = mock.ints().get();
long y = mock.longs().get();
String s = mock.strings().get();
```

Since `0.2.6` all/only the methods from `MockNeat.threadLocal()` instance can be called directly by using static imports:

```java
import static net.andreinc.mockneat.unit.text.Strings.strings;
import static net.andreinc.mockneat.unit.types.Ints.ints;
import static net.andreinc.mockneat.unit.types.Longs.longs;

...

// Gets a random int, long and string
int x = ints().get();
long y = longs().get();
String s = strings().get();
```

Basically:

```java
MockNeat mock = MockNeat.threadLocal();
int x = mock.ints().get();
```

Is equivalent with:

```java
int x = ints().get();
```

----

*Note:*

For very specific use-cases new `MockNeat` instances can be created by invoking directly the public constructor. For example if we want to create a secure `MockNeat` object with `123l` as the seed:

```java
Long seed = 123l;
MockNeat mock = new MockNeat(RandomType.SECURE, seed);
```

`RandomType.SECURE` is the only type that supports seeding.
