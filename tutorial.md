---
layout: single
permalink: /tutorial/
classes: wide
sidebar:
  nav: "tutorial"
---

# Creating `MockNeat` objects

`MockNeat.class` is the "entry-point" of the library. Think of this as a *fat* factory class, responsible with the instantiation of all of the existing data generators.

By default the library creates 3 reusable default `MockNeat` "factories", each wrapping a different type of `Random` generators ([ThreadLocalRandom](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadLocalRandom.html), [SecureRandom](http://docs.oracle.com/javase/8/docs/api/java/security/SecureRandom.html) or [the good Old Random](https://docs.oracle.com/javase/8/docs/api/java/util/Random.html)):

```java
MockNeat mock1 = MockNeat.threadLocal(); // recommended
MockNeat mock2 = MockNeat.secure();
MockNeat mock3 = MockNeat.old();
```

Calling `threadLocal()`, `secure()` or `old()` will always return the same instance.

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

Since `0.2.6` "shortcut static methods" were introduced pointing to `MockNeat.threadLocal()`. This is syntactic sugar, and as long as you stick with the `MockNeat.threadLocal()` implementation this should be the preferred way of doing things:

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

Where:

```java
MockNeat mock = MockNeat.threadLocal();
int x = mock.ints().get();
```

Is equivalent with:

```java
int x = ints().get();
```

In this tutorial most of the examples will use the shortcut static methods "mirroring" the ones in `MockNeat.threadLocal()`.

----

*Note:*

For very specific use-cases new `MockNeat` instances can be created by invoking directly the public constructor. For example if we want to create a secure `MockNeat` object with `123l` as the seed:

```java
Long seed = 123l;
// Note: RandomType.SECURE is the only type that supports seeding.
MockNeat mock = new MockNeat(RandomType.SECURE, seed);
```

It's highly recommended to avoid creating multiple `MockNeat` instances. It's better to stick with one instance per-project.

# MockNeat as an enhanced `Random`

Internally `MockNeat` wraps a java `Random` implementation and offers a powerful API for generating arbitrary values for primitive and `String` types.

After creating any of the data generators (also called a `MockUnit<Type>`s) you always have to call `get()` in order to obtain the actual value. **mockneat** works in a lazy way, no value will be generated until explicitly calling `get()`.

*Note*: Older examples you can find on the Internet might be using `val()` instead of `get()`. `get()` is just an alias which was introduced later, starting with version `0.2.5`. The reason for this addition is simple, `val` is a restricted keyword in JVM languages (like Scala).

## Booleans

`Boolean` (or `boolean`) values can be generated using the `bools()` (or `mockNeat.bools()`) method. As an additional feature `true` values can be generated with a given probability like in the example bellow.

```java
import static net.andreinc.mockneat.unit.types.Bools.bools;
...

// Generating boolean values
boolean bool = bools().get();
boolean almostTrue = bools().probability(99.00)
                            .get();
```

## Chars

`char` (or `Character`) values can be generated using the `chars()` (or `mockNeat.chars()`) method.

```java
import static net.andreinc.mockneat.types.enums.CharsType.*;
import static net.andreinc.mockneat.unit.types.Chars.chars;
...

// Example for generating a single char value
char c1 = chars().get();

// Example for generating a digit char
// (Eg.: '0', '1', etc.)
char digit1 = chars().digits().get();
char digit2 = chars().type(DIGITS).get();

// Example for generating a lower letter
// (Eg.: 'a', 'z', etc.)
char lowerLetter1 = chars().lowerLetters().get();
char lowerLetter2 = chars().type(LOWER_LETTERS).get();

// Example for generating an upper letter
// (Eg.: 'A', 'J', etc.)
char upperLetter1 = chars().upperLetters().get();
char upperLetter2 = chars().type(UPPER_LETTERS).get();

// Example for generating a letter which is either
// lowercase or uppercase
char letter1 = chars().letters().get();
char letter2 = chars().type(LETTERS).get();

// Example for generating an arbitrary HEX character
// Hex characters are considered to be:
// [ "0", "1", "2", "3", "4", "5", "6", "7", "8", "9",
// "a", "b", "c", "d", "e", "f" ]
Character hex1 = chars().hex().get();
Character hex2 = chars().type(HEX).get();

// Example for generating an arbitrary alphanumeric character
char an1 = chars().alphaNumeric().get();
char an2 = chars().type(ALPHA_NUMERIC).get();
```

All of the methods `digits()`, `letters()`, `hex()` etc. are shortcuts for the `type(CharsType)` method.

In case you want to combine different types but in the same time exclude others, you can use the `types(CharTypes...)` method.

For example the following code will be generating either a lower letter or digit (but not an upper letter):

```java
char lowerLetterOrDigit = chars().types(LOWER_LETTERS, DIGITS).get();
```

## Ints

`int` (or `Integer`) values can be generated using the `ints()` (or `mockNeat.ints()`) method.
