---
layout: single
permalink: /tutorial/
classes: wide
title: Tutorial
sidebar:
  nav: "tutorial"
---

This tutorial is intended to describe the ways of working with **mockneat**. It's definitely not a comprehensive description of the various APIs and their usage. For this please check the [official docs](../docs).

*Disclaimer*: www.mockneat.com is a [Jekyll site](https://jekyllrb.com) and it's [open source](https://github.com/nomemory/mockneat-site), so if you see any code errors or you have any suggestions in the approach, explanations, etc. please open a PR. Also English is not my native-language, so "grammer" <sup>sigh</sup> mistakes are inherent and anxious to be corrected.

# Table of contents:

* [The MockNeat class](/tutorial#creating-mockneat-objects)
* [MockNeat as an enhanced Random for:](/tutorial#mockneat-as-an-enhanced-random)
    - [Booleans](/tutorial#booleans)
    - [Chars](/tutorial#chars)
    - [Ints](/tutorial#ints)
    - [Doubles](/tutorial#doubles)
    - [Longs and floats](/tutorial#longs-and-floats)
    - [Strings](/tutorial#strings)
    - [Dates](/tutorial#dates)
* [Everything is a `MockUnit<T>`](/tutorial#everything-is-a-mockunitt)
    - [Closing Methods](/tutorial#closing-methods)
    - [Transformer Methods](/tutorial#transformer-methods)
* [The `MockUnitString`](/tutorial#the-mockunitstring)
* [More than an enhanced Random](/tutorial#more-than-an-enhanced-random)
    - [Address Generators](/tutorial#address-generators)
    - [Financial Generators](/tutorial#financial-generators)
    - [Internet Data Generators](/tutorial#internet-data-generators)
    - [Sequence Generators](/tutorial#sequence-generators)
    - [Text Generators](/tutorial#text-generators)
    - [User Data Generators](/tutorial#user-data-generators)
* [Composing Objects](/tutorial#composing-objects)
  - [`filler()`](/tutorial#filler)
  - [`constructor()`](/tutorial#constructor)
  - [`factory()`](/tutorial#factory)
  - [`reflect()`](/tutorial#reflect)
* [Generating CSV Files](/tutorial#generating-csv-files)

# The `MockNeat` class

The `MockNeat.class` is the "entry-point" of the library. Think of this as a *fat* factory class, responsible with the instantiation of all of the existing [data generators](../docs#datagenerators). It's the most important and, in most of the cases, the most "invisible" component from the library.

By default three reusable `MockNeat` "factory objects" are created, each wrapping a different type of java `Random` generators ([ThreadLocalRandom](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadLocalRandom.html), [SecureRandom](http://docs.oracle.com/javase/8/docs/api/java/security/SecureRandom.html) or [the good Old Random](https://docs.oracle.com/javase/8/docs/api/java/util/Random.html)):

```java
MockNeat mock1 = MockNeat.threadLocal(); // recommended !
MockNeat mock2 = MockNeat.secure();
MockNeat mock3 = MockNeat.old();
```

Calling `threadLocal()`, `secure()` or `old()` will always return the same instances of `MockNeat`, so in a way the class is a "three"-ngleton.

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

Since version **0.2.6** "shortcut static methods" were introduced pointing to `MockNeat.threadLocal()`.

This is syntactic sugar, and as long as you stick with the `MockNeat.threadLocal()` implementation this should be the preferred way of doing things:

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

In this tutorial most of the examples will use the shortcut static methods "mirroring" the ones in `MockNeat.threadLocal()`, so there will be little interaction with `MockNeat.class`.

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

Internally the `MockNeat.class` wraps a java `Random` implementation and offers a powerful API for generating arbitrary values for primitive ([`boolean`](#booleans), [`chars`](#chars), [`ints`](#ints), [`doubles`](#doubles), etc.), `String`, `LocalDate`, `Day` and `Month` types.

After creating any of the [data generators](../docs#datagenerators) (also called a `MockUnit<Type>`s) you always have to call [`get()`](../docs#get) in order to obtain the actual value. **mockneat** works in a lazy way, no value will be generated until explicitly calling the "closer" `get()`.

*Note*: Older examples you can find on the Internet might be using [`val()`](../docs#val) instead of `get()`. `get()` is just an alias which was introduced later, starting with version `0.2.5`. The reason for this addition is simple, `val` is a restricted keyword in some JVM languages (eg.: Scala).

## Booleans

`Boolean` (or `boolean`) values can be generated using the [`bools()`](../docs#bools) (or `mockNeat.bools()`) method.

As an additional feature `true` values can be generated with a given probability like in the example bellow.

```java
import static net.andreinc.mockneat.unit.types.Bools.bools;
...

// Generating boolean values
boolean bool = bools().get();
boolean almostTrue = bools().probability(99.00)
                            .get();
```

## Chars

`char` (or `Character`) values can be generated using the [`chars()`](../docs#chars) (or `mockNeat.chars()`) method.

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

`int` (or `Integer`) values can be generated using the [`ints()`](../docs#ints) (or `mockNeat.ints()`) method.

```java
import static net.andreinc.mockneat.unit.types.Ints.ints;
...

// Generates a arbitrary integer in the interval
// [Integer.MIN_VALUE, Integer.MAX_VALUE]
int x = ints().get();

// Generates an arbitrary integer in the interval
// [0, 10)
int y = ints().range(0, 10).get();

// Generates an arbitrary integer in the interval
// [0, 100)
int z = ints().bound(100).get();
```

The `ints()` data generator has an additional helper method that allows the developer to "extract" a random value from an array `int[]`:

```java
// Generates a integer value from the given array
int[] array = new int[] {1,10,100,1000};
int fromArray = ints().from(array).get();
```

For example we can use the `from(int[])` method to arbitrary generate a `10x10` matrix of integers containing only `0` and `1`:

```java
// Create a "matrix" of zeroes and ones
int[][] zeroesAndOnes = new int[10][10];

// Populate the matrix with values
for(int i = 0; i < 10; i++)
    for(int j = 0; j < 10; j++)
        zeroesAndOnes[i][j] = ints()
                                .from(new int[]{0, 1})
                                .get();


// Printing the matrix
for (int i = 0; i < 10; i++) {
    for(int j = 0; j < 10; j++) {
        System.out.print(zeroesAndOnes[i][j] + " ");
    }
    System.out.println();
}

// (Possible) Output:
/**
1 0 1 1 1 1 1 1 0 1
0 0 1 0 1 1 1 0 0 0
1 0 0 1 0 1 1 1 1 1
1 1 1 1 1 1 0 1 0 0
1 1 1 1 0 1 0 1 1 1
0 0 0 0 1 0 0 1 1 0
0 1 0 1 0 1 1 1 0 0
0 0 1 1 1 1 1 1 1 0
1 0 1 0 0 1 1 0 1 0
0 0 0 1 0 0 0 0 1 1
*/
```

## Doubles

`double` (or `Double`) values can be generated using the [`doubles()`](../docs#doubles) (or `mockNeat.doubles()`) method.

```java
import static net.andreinc.mockneat.unit.types.Doubles.doubles;
...

// Generates an arbitrary double in the interval [0.0, 1.0)
double d1 = doubles().get();

// Generates an arbitrary double in the interval [0,0, 10.0)
double d2 = doubles().range(0, 10.0).get();
```

Just like the `ints()` data generator, the `doubles()` generator has a helper method to select values randomly from an an already existing array (`double[] array`).

```java
// Generates an arbitrary double that is either 1.1, 1.2 or 1.3
double d3 = doubles().from(new double[]{1.1, 1.2, 1.3}).get();
```

## Longs and floats

The API for [`longs()`](../docs#longs) is identical with the one for [`ints()`](#ints).

The API for [`floats()`](../docs#floats) is identical with one for [`doubles()`](#doubles).

For brevity we won't add additional code examples, please check the [docs](../docs).

## Strings

**mockneat** has a few `String` generators, but for the moment we will describe the behaviour of the most common one,  [`strings()`](../docs#strings) (or `mockUnit.strings()`). This generates text without any inherent meaning <sup>(let's call it "giberish")</sup>.

Even so, it's possible to specify the type of the characters used (eg.: `NUMBERS`, `ALPHA_NUMERIC`, `LETTERS`, `HEX` or `SPECIAL_CHARACTERS`) and the size of the resulting String. The size can be fixed (`int`) or arbitrary (defined with `ints()`).

If no type is specified, the default characters are letters (both upper case and lower case) and numbers, but no special characters will be used.

If no size is specified, the default is `64` <sup>(don't ask me why)</sup>.

Examples:

```java
// Generates a String of size 64 (default value)
// containing letters (upper case and lowercase) and numbers
String theFirstString = strings().get();
System.out.println("theFirstString= " + theFirstString);

// Generates a String of size 10 (custom value)
// containing only NUMBERS
String someNumbers = strings()
                        .size(10)
                        .type(NUMBERS)
                        .get();
System.out.println("someNumbers= " + someNumbers);

// Generates a String with a random size
String randomSized = strings()
                        .size(ints().range(1, 100))
                        .get();

// Output:
/**
theFirstString= r6dficsvQlkj7j75bDrRN8GktU0PejNXRGfDmK8F5HlM69lNZBZ6Tz3MX9S9mRiU
someNumbers= 9098810282
*/
```

## Dates

An easy to use mechanism was implemented to generate `LocalDate` values, using the [`localDate()`](../docs#localdates) (or `mockUnit.localDates()`) method.

By default dates are generated from `[1970, now()]`:

```java
LocalDate localDate = localDates().get();
//Output:
/**
1984-05-10
*/
```

But it's also possible to generate dates only in the current year:

```java
 LocalDate thisYear = localDates().thisYear().get();
 //Output:
 /**
 2018-12-28
 */
 // The tutorial was written in 2018 sic!
```

Or in the current month:
```java
LocalDate thisMonth = localDates()
                        .thisMonth()
                        .get();
```

Future or past:
```java
LocalDate maxDateInTheFuture = LocalDate.of(2050, 12, 12);
LocalDate inTheFuture = localDates()
                          .future(maxDateInTheFuture)
                          .get();

// OR

LocalDate minDateInThePast = LocalDate.of(1987, 1, 30);
LocalDate inThePast = localDates(minDateInThePast)
                          .past(min)
                          .get();
```

If the application uses the `java.util.Date` type instead of the newer `java.time.LocalDate` there's a convenient way to do it by adding into the mix `toUtilDate()`:

```java
Date thisMonth = localDates()
                    .thisMonth()
                    .toUtilDate() // THIS!
                    .get();
```                                

After reading the next chapter, the "magic" behind `toUtilDate()` will more obvious.

# "Everything" is a `MockUnit<T>`

The rule of thumbs is that every [data generator](../docs#datagenerators) from `MockNeat` is a `MockUnit<T>`.

Calling `chars()` without the closing method (`get()` or `val()`) returns a `MockUnit<Character>`.
```java
MockUnit<Character> asAMockUnit = chars();
```
...and so on

For certain data types more specialised interfaces exists. For example [`ints()`](../docs#ints) returns an `Ints` data generator, which is an implementation of `MockUnitInt`, which is actually an extension for `MockUnit<Integer>` that adds additional benefits (read *more methods*).

`MockUnit`s are powerful mechanisms that allow developers to generate data incrementally, by defining broader behaviours that can be reused/transformed in different scenarios.

For example we define an `ints()` generator (`MockUnitInt`) that generates numbers between `[0, 100)`.

```java
MockUnitInt generator = ints().range(0, 100);
```

Now the same generator can be re-used in different scenarios by adding new behaviours or additional constraints.

We can create arrays of `int` or `Integer`.
```java
Integer[] ints1 = generator.array(10).get();
int[] ints2 = generator.arrayPrimitive(10).get();
```

Or an infinite stream of arbitrary integers in that range:

```java
Stream<Integer> infiniteStream = generator.stream().get();
```

Or a `List<String>` where each String is actually a integer from the given range `[0, 100)`:
```java
MockUnitInt generator = ints().range(0, 100);
// MockUnitString stringGenerator = generator.map(i -> i.toString());
MockUnitString stringGenerator = generator.mapToString();
MockUnit<List<String>> listGenerator = stringGenerator.list(() -> new LinkedList<>(), 10);

List<String> list1 = listGenerator.get();
List<String> list2 = listGenerator.get();

System.out.println(list1);
System.out.println(list2);

// Output:
/**
[43, 8, 11, 83, 97, 67, 62, 97, 46, 55]
[62, 4, 23, 97, 79, 4, 81, 62, 37, 12]
*/
```

`mapToString()`, `map()` and `list(...)` are what we call *"transformers"* (`default` generic methods defined in the `MockUnit<T>` interface).

More precisely `mapToString()` is transforming the `MockUnitInt` generator into a new generator `MockUnitString`, which will generate `String` values obtained from integers in the range <sup>yes, exactly</sup>`[0, 100)`.

`list(Supplier<List<String>>, int size)` is another transformer that will transform the previous `MockUnitString` into a new `MockUnit<List<String>>`, where each element from the resulting `List` is a `String` representing an `Integer` in the range <sup>yes, exactly</sup>`[0, 100)`.

For brevity it's not recommended to keep all the intermediary references so you can chain all the methods (in the most fluent mode possible):

```java
List<String> list3 = ints()
                      .range(0, 100)
                      .mapToString()
                      .list(() -> new LinkedList<>(), 10)
                      .get();

System.out.println(list3);
//Output
/**
[57, 80, 26, 99, 31, 40, 98, 4, 77, 58]
*/
```

Data generators are lazy by nature. That's why we can split the methods associated with them into two main categories:

* **Closers**: methods that are asking the `MockUnit<T>` to generate data on the spot. After calling a closer method, the `MockUnit<?>` "chain ends". (Eg.: `get()`).
* **Transformers**: methods that are incrementally changing the behaviour of the `MockUnit<T>` without actually computing any data. (Eg.: `map(...)`, `list(...)`, etc.);

For a comprehensive list of transformer or closing methods please check the [docs](../docs) for:
* [`MockUnit<T>`](../docs#mockunit)

## Closing Methods

### [`get()`](../docs#get)

The most obvious closing method is [`get()`](../docs#get). All of the examples have used it so there's need to (re)explain what it does.

### [`consume()`](../docs#consume)

Another important method is [`consume()`](../docs#consume). Compared to `get()` this computes the arbitrary value, but it doesn't return it, instead it consumes it through a `Consumer<T> consumer` (or `BiConsumer<Integer, T>`). For example if we just want to print a `List<Integer>` a on `System.out` we can write something like:

```java
ints().list(10).consume((list) -> System.out.println(list));

// OR

ints().list(10).consume(System.out::println); // Using the :: notation
```

The `(Bi)Consumer<T> consumer` can be called multiple times, and with each call the `MockUnit` will return a different value. For example we can write a program that rolls a dice 10 times and prints the results on `System.out` like this:

```java
BiConsumer<Integer, Integer> dicePrinter =
                (i, dice) -> System.out.printf("Roll = %d, Dice = %d\n", i+1, dice);

ints()
  .range(1, 7)
  .consume(10, dicePrinter);

// output
/**
Roll = 1, Dice = 1
Roll = 2, Dice = 6
Roll = 3, Dice = 2
Roll = 4, Dice = 3
Roll = 5, Dice = 3
Roll = 6, Dice = 3
Roll = 7, Dice = 4
Roll = 8, Dice = 6
Roll = 9, Dice = 2
Roll = 10, Dice = 4
*/
```

## Transformer Methods

### [`map(Function<T1, T2>)`](../docs#map)

This is one of the most useful transformation method: it takes a `MockUnit<T1>` and transforms it into a `MockUnit<T2>`, through a `Function<T1, T2>`.

For example if we run the [FizzBuzz](https://en.wikipedia.org/wiki/Fizz_buzz) solution over a 100 `Integers` and print the results we can do something like:

```java
// Solution for FizzBuzz exercise.
Function<Integer, String> fizzBuzz = (i) -> {
    if (i % (5*3) == 0) return i + "=>FizzBuzz";
    else if (i % 3 == 0) return i + "=>Fizz";
    else if (i % 5 == 0) return i + "=>Buzz";
    return String.valueOf(i);
};

ints()
  .range(1, 100)
  .map(fizzBuzz) // MockUnitInt becomes MockUnit<String>
  .list(100) // MockUnitString becomes MockUnit<List<String>>
  .consume(System.out::println);

//output:
/**
[27=>Fizz, 37, 55=>Buzz, 19, 7, 65=>Buzz, 77, 5=>Buzz, 17, 20=>Buzz, 64, 85=>Buzz, 2, 79, 81=>Fizz, 92, 87=>Fizz,
...]
*/
```

After calling `map(fizzBuzz)` the initial `MockUnitInt` is transformed into a `MockUnit<String>`, this can be ok for most use-cases, but to enable the usage of more advanced `String` processing methods it's probably recommended to "upgrade" directly to `MockUnitString` (a smarter extension for `MockUnit<String>`):

```java
ints()
  .range(1, 100)
  .mapToString(fizzBuzz) // MockUnit becomes MockUnitString (not MockUnit<String>)
  .format(UPPER_CASE) // Uppercase the Strings (available only on MockUnitString)
  .replace("F", "X") // Replaces "F" with "X" (available only on MockUnitString)
  .list(100) // MockUnitString becomes MockUnit<List<String>>
  .consume(System.out::println);
```

Equivalent methods exist for all the other enhanced `MockUnits`:

* [`mapToDouble(...)`](../docs#maptodouble) to transform an existing `MockUnit<T>` into a `MockUnitDouble`;
* [`mapToInt(...)`](../docs#maptoint) to transform an existing `MockUnit<T>` into a `MockUnitInt`;
* [`mapToLong(...)`](../docs#maptolong) to transform an existing `MockUnit<T>` into a `MockUnitLong`;
* [`mapToLocalDate(...)`](../docs#maptolocaldate) to transform an existing `MockUnit<T>` into a `MockUnitLocalDate`;

The `map(Function<T1, T2>)` doesn't impose any restriction over the types. `T1` can be similar with `T2`. In this case the `MockUnit` will not be translated into something else (from the type perspective).

For example if we want to generate an arbitrary multiple of 10, we can simply apply `map()` and return the same type:

```java
int multipleOf10 = ints().map(i -> i * 10).get();
```

### [`array()`](../docs#array), [`collection()`](../docs#collection), [`list()`](../docs#list) and [`set()`](../docs#set)

Those methods are used to transform an existing `MockUnit<T>` into a `MockUnit<T[]`, `MockUnit<Collection<T>>`, `MockUnit<List<T>>` and `MockUnit<Set<T>>`.

Multiple values will be generated and collected into arrays, collections, lists and sets.

Generating a uni-dimensional array of `Integer` values from the interval `[0,5)` can be written in a very concise way:

```java
Integer[] ints = ints()
                    .range(0, 5)
                    .array(10)
                    .get();
```


`MockUnits` can be chained to obtain powerful constructs. For example generating a `List<List<Integer>>` can be obtain just by calling [`list()`](../docs#list) twice:

```java
List<List<Integer>> listOfLists = ints()
                                    .list(2) // MockUnitInt becomes MockUnit<List<Integer>>
                                    .list(2) // MockUnit<List<Integer>> becomes MockUnit<List<List<Integer>>>
                                    .get(); // Evaluate the value

System.out.println(listOfLists);

//Output
/**
[[1082381729, 1097865582], [1952501993, 221886267]]
*/
```

By default the internal implementation used for `List<T>` is `ArrayList<T>`. But what if we want to change the implementation of the "wrapping" `List<T>` from `ArrayList<T>` to `LinkedList<T>`.

Additionally what if we want the list to have an arbitrary length itself ?

```java
MockUnitInt sizeGen = ints().range(0, 5); // size will be randomly generated

List<List<Integer>> listOfLists = ints()
                                    .list(sizeGen)
                                    .list(() -> new LinkedList<>(), sizeGen)
                                    .get();

//Output
/**
[[-1086447222, 364023211], [-1712221397, -1973762881], [-1502802628, 1863612508]]
*/
```

[`set()`](../docs#set) works in a similar fashion as `list()`, the API is the same except one big difference: size is not guaranteed. This is a normal consequence of the fact that a `Set<T>` can only contain unique elements.

```java
Set<Integer> set = ints()
                         .from(new int[]{1,2,3})
                         .set(1000) // There only 3 possible distinct values,
                                    // mockneat will try 1000 times to add new data
                                    // but in the end the Set<Integer> will contain
                                    // only 3 elements.
                                    // This is normal behaviour.
                         .get();

System.out.println("set.size() =" + set.size());
//Output
/**
set.sizie() =3
*/
```

### [mapKeys()](../docs#mapkeys) and [mapVals()](../docs#mapvals)

The [mapKeys()](../docs#mapkeys) method is used to create `Map<K, V>`s and fill them up with arbitrary data.

Basically it transforms an existing `MockUnit<T>` into a a new `MockUnit<R, T>` where the `<R>` keys are generated from:

* A `Supplier<R>` (this can be obtained from a `MockUnit` with `mockUnit.supplier()`);
* An `Iterable<R>` (eg.: a `List<R>`);
* A generic array `R[]`;
* Primitive arrays of type: `int[]`, `double[]`, `long[]`. In this case `<R>` is not generic so corresponding `MockUnit<Integer, T>`, `MockUnit<Double, T>` or `MockUnit<Long, T>` will be created.

```java
Map<Integer, List<Double>> map =
                doubles()
                    // MockUnitDouble
                    .gaussians()
                    // Is transformed into a MockUnit<List<Double>>
                    .list(2)
                    // Is transformed into a MockUnit<Map<Integer, List<Double>>
                    .mapKeys(3, ints().bound(10000).supplier())
                    // The resulting object is computed
                    .get();

System.out.println(map);
// Output:
/**
{5680=[0.3378495069224774, -0.333557489486435], 5386=[0.9789656246200228, 0.3684941698377526], 5822=[1.2328661504292975, 0.5981217534882592]}
*/
```

*Note:* Just like in the `Set<T>`'s case the keys in the Map are unique so the actual size of the `Map<K,V>` is not guaranteed to be the input value.

[`mapVals()`](../docs#mapvals) works in similar way, but instead of mapping keys we are mapping values. The transformation works the other way around, `MockUnit<T>` becomes `MockUnit<T,R>` where `<R>` keys are generated from the same sources as for `mapKeys()`.

`mapKeys()` and `mapVals()` can achieve the same results. For example we can create the exact `Map<Integer, List<Double>>` as in the previous example:
```java
Map<Integer, List<Double>> sameMapAsAbove =
                ints()
                    // MockUnitInt
                    .bound(10000)
                    // Is transformed into a MockUnit<Map<Integer, List<Double>>
                    .mapVals(3, doubles().gaussians().list(2).supplier())
                    // The resulting object is computed
                    .get();

System.out.println(sameMapAsAbove);
// Output:
/**
{8195=[-0.8483899838848824, 0.42267892251968014], 7051=[-0.9665327769342633, 0.4030082004963111], 2991=[-1.0825503179214298, -0.9325765134899544]}
*/
```

# The [MockUnitString](../docs#mockunitstring)

[`MockUnitString`](../docs#mockunitstring) is an extension for `MockUnit<String>`. Out of all the specialised MockUnits it contains the most (useful) methods for customising output (both closers and transformers).

## accumulate()

This is a method that concatenates multiple values into one "big `String`". You can specify how many `Strings` to be concatenated and what separator to use (Eg.: ",", "\n", etc.):

Example for creating a "HTML List" of random characters:

```java
StringBuilder buff = new StringBuilder();

buff.append("<ul>\n")
    .append(
        chars()
            .upperLetters()
            .mapToString((c) -> String.format("\t<li>%s</li>", c.toString()))
            .accumulate(5, "\n")
            .get()
     )
     .append("\n</ul>");

System.out.println(buff.toString());

//Output:
/**
<ul>
	<li>Q</li>
	<li>F</li>
	<li>F</li>
	<li>H</li>
	<li>S</li>
</ul
*/
```        

## format()

`format(type)` is used to format the String values generated by a `MockUnitString`.

The possible formatting types are kept in the `StringFormatType.enum` and the possible values are:

* `UPPER_CASE`
* `LOWER_CASE`
* `CAPITALIZED`

Example:

```java
import static java.lang.System.out;
import static net.andreinc.mockneat.types.enums.StringFormatType.UPPER_CASE;

//....

strings()
  .size(10)
  .format(UPPER_CASE)
  .accumulate(10, "\n")
  .consume(out::println);

// Output:
/**
L6K8FUGNXL
PLUJTTTAOG
AN09ZKVEQB
EHZPAEJFPR
FAMYLHXUOT
GXDSOJWSJB
KZR8JYNZEE
2DU7WTHOJA
IIVRYJIMIW
Q9GOFLQERB
*/
```

## `append()` and `prepend()`

`append(String)` and `prepend(String)` are modifying the arbitrary generated `String` from the `MockUnitString` by appending and prepending additional text.

For example we pretty print a random matrix of integers like this:

```java
int size = 10;

// Hack-ish way of repeating a char 'n=10' times
String horizontalLine =
  String.format("\n " + new String(new char[size]).replace("\0", "- ") + " \n");

ints()
  .range(0, 10)
  .mapToString()
  .accumulate(size, " ") // We create the "row"
  .prepend("|") // Every row will be prefixed with "|"
  .append("|") // Every row will be suffixed with "|"
  .accumulate(size, "\n") // We accumulate 10 strings
  .append(horizontalLine)
  .prepend(horizontalLine)
  .consume(out::println);

// Output:
/**
 - - - - - - - - - -  
|3 0 5 0 4 0 7 4 5 8|
|5 5 3 2 2 6 0 6 9 8|
|8 1 0 4 1 5 9 3 2 1|
|9 3 3 7 0 6 3 2 2 9|
|2 3 1 4 6 3 7 7 9 9|
|8 7 9 8 0 2 9 0 3 2|
|7 2 1 8 6 2 9 2 7 3|
|0 6 6 0 4 4 7 4 3 1|
|2 2 6 8 5 8 2 2 4 1|
|6 1 5 6 7 8 0 4 2 3|
 - - - - - - - - - -
*/  
```

# More than an enhanced `Random`

It's nice to be able to generate `Integer`, `Double` or `Character` values, but **mockneat** can do much more...

Actually the main reason the library was created was to enable developers to generate (big) data be it json, xml, csv or sql (inserts) format.

That's why multiple and [more complex generators](../docs#datagenerators) were created:

## Address Generators

The following generators are useful to generate user information related to addresses.

| Data Generator | MockUnit|  Description |
|----------------|---------|--------------|
| [`cities()`](../docs#cities) | `MockUnitString` | Generates arbitrary city names from US or World Capitals. |
| [`countries()`](../docs#countries) | `MockUnitString` | Generates arbitrary country names (or their [ISO2 code](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)). |
| [`usStates()`](../docs#usstates) | `MockUnitString` | Generates US states names (or their ISO2 code). |

Examples:

```java
import static java.lang.System.out;

//****

cities().us().consume(out::println);
cities().capitals().consume(out::println);

countries().names().consume(out::println);
countries().iso2().consume(out::println);

usStates().consume(out::println);
usStates().iso2().consume(out::println);

//Output:
/**
Kellner
Helsinki
Liberia
BG
Massachusetts
MN
*/
```

## Financial Generators

The following generators are useful to generate "financial" information.

| Data Generator | MockUnit | Description |
|----------------|---------|--------------|
| [`creditCards()`](../docs#creditcards) | `MockUnitString` | Generates valid credit card numbers of different types (eg: `VISA`, `MASTERCARD` or `AMEX`) |
| [`currencies()`](../docs#forexpairs) | `MockUnitString` | Generates valid [Forex Pairs](https://en.wikipedia.org/wiki/Currency_pair) (eg.: "EUR/USD"), currencies codes or currency names (eg.: "Dollar").|
| [`cvvs()`](../docs#cvvs) | `MockUnitString` | Generates valid [CVV](https://en.wikipedia.org/wiki/Card_security_code) numbers for credit cards. |
| [`ibans()`](../docs#ibans) | `MockUnitString` | Generates valid [IBAN](https://en.wikipedia.org/wiki/International_Bank_Account_Number) numbers. |
| [`money()`](../docs#money) | `MockUnitString` | Generate money amounts as `String` (Money Symbol + Sum of Money).|

Example for generating valid credit card numbers (either VISA or MASTERCARD):

```java
import static java.lang.System.out;
import static net.andreinc.mockneat.types.enums.CreditCardType.MASTERCARD;
import static net.andreinc.mockneat.types.enums.CreditCardType.VISA_16;
import static net.andreinc.mockneat.unit.financial.CreditCards.creditCards;

//...

creditCards()
  .types(VISA_16, MASTERCARD)
  .accumulate(10, "\n")
  .prepend("Valid Credit Card Numbers (VISA or Mastercard):\n")
  .consume(out::println);

// Output:            
/**
Valid Credit Card Numbers (VISA or Mastercard):
5595283075583783
5392788967714978
2720852738101152
2720445808982955
5408438861860897
2720609587283287
5118860666122093
5161222900516349
5438740188862769
5162746414517780
*/
```

## Internet Data Generators

The following generators are useful to generate information related to the networking and Internet.

| Data Generator | MockUnit | Description |
|----------------|---------|--------------|
| [`domains()`](../docs#domains) | `MockUnitString` | Generates domains suffixes for [URLs](https://en.wikipedia.org/wiki/URL). |
| [`ipv4s()`](../docs#ipv4s) | `MockUnitString` | Generates arbitrary (valid) [IPv4](https://en.wikipedia.org/wiki/IPv4) addresses. |
| [`ipv6s()`](../docs#mapkeys) | `MockUnitString` | Generates an arbitrary (valid) [IPv6](https://en.wikipedia.org/wiki/IPv6) address. |
| [`macs()`](../docs#macs) | `MockUnitString` | Generates arbitrary (valid) [MAC addresses](https://en.wikipedia.org/wiki/MAC_address). |
| [`urls()`](../docs#urls) | `MockUnitString` | This method can be used to generate random [URLs](https://en.wikipedia.org/wiki/URL). |


Example for generating a `Map<String, String>` association where the key is an IPv4 address and the value is a MAC Address:

```java
import static net.andreinc.mockneat.types.enums.IPv4Type.CLASS_A_NONPRIVATE;
import static net.andreinc.mockneat.types.enums.MACAddressFormatType.COLON_EVERY_2_DIGITS;
import static net.andreinc.mockneat.unit.networking.IPv4s.ipv4s;
import static net.andreinc.mockneat.unit.networking.Macs.macs;

//....

// Map<String, String>
// K = IPV4 Address, V = MAC
Map<String, String> networkStuff =
               ipv4s()
                   .type(CLASS_A_NONPRIVATE)
                   .mapVals(20,
                           macs()
                           .type(COLON_EVERY_2_DIGITS)
                           .supplier()
                   )
                   .get();

System.out.println(networkStuff);

// Output:
/**
{83.107.137.237=bf:95:ab:de:f3:cf,
39.175.116.211=01:42:62:10:e6:a1,
120.117.0.254=f7:cb:87:39:de:6e,
57.61.73.171=df:4a:76:bf:1e:35,
4.14.181.9=43:20:96:b4:66:00,
..... etc
}
*/
```

## Sequence Generators

Those methods are useful for generating sequences of numbers or to cycle through an already existing collection.

The resulting data is not arbitrary, but the generators can prove useful for generating SQL table ids or "unique objects" for `Maps` and `Sets`.


### [`intSeq()`](../docs#intseq) and [`longSeq()`](../docs#longseq)

The simplest usage to define a sequence of numbers is with [`intSeq()`](../docs#intseq) for `Integer` or [`longSeq()`](../docs#longseq) for `Long`.

Calling `get()` on the resulting `MockUnitInt` (or `MockUnitLong`) will return incremental values. By default both generators start at 0 and the increment is `1`. (Note: Negative increments are also supported).

```java
MockUnitInt sequence = intSeq();

System.out.println(sequence.get()); // 0 <- default
System.out.println(sequence.get()); // 1 <- 0+1
System.out.println(sequence.get()); // 2 <- 1+1

// Or for a "negative" sequence

MockUnitLong negativeSeq = longSeq().increment(-5l);

System.out.println(negativeSeq.get()); // 0
System.out.println(negativeSeq.get()); // -5
System.out.println(negativeSeq.get()); // -10
```

For example the previous "dice rolling example" (see [consume](#consume)) can be rewritten like this:

```java
intSeq()
    .start(1) // The sequence starts with 1, by default is 0
    .mapToString(i -> // The resulting i is an sequence [0, 1, 2...]
            format("Roll = %d, Dice = %d", i, ints()
                                                .range(1, 7)
                                                .get()
            )
    )
    .accumulate(10, "\n")
    .consume(System.out::println);

// Output
/**
Roll = 0, Dice = 3
Roll = 1, Dice = 1
Roll = 2, Dice = 1
Roll = 3, Dice = 3
Roll = 4, Dice = 5
Roll = 5, Dice = 1
Roll = 6, Dice = 2
Roll = 7, Dice = 5
Roll = 8, Dice = 1
Roll = 9, Dice = 3
*/    
```

Sequences allow cycling through elements by defining a `max` and/or `min`.

```java
intSeq()
  .max(5)
  .cycle(true) // by default is true
  .list(10)
  .consume(System.out::println);

// Output:
/**
[0, 1, 2, 3, 4, 5, 0, 1, 2, 3]
                // ^ cycling starts here
*/  
```

If cycling is turned off (by explicitly calling `.cicle(false)`) and the max value is reached an exception is thrown:

```java
intSeq()
  .max(5)
  .cycle(false)
  .list(10)
  .consume(System.out::println);

// Output
/**
...
Caused by: java.lang.IllegalStateException: IntSeq overflow. Values are generated inside the interval: [-2147483648, 5]. Cannot increment any further.
...
*/
```

### [`seq()`](../docs#seq)

[`seq()`](../docs#seq) is `MockUnit<T>` that allows traversing arrays or `Iterable<T>`.

Each subsequent call will return the next value. For example if we want to traverse a `String[]` containing three values, and create a `Map<String, Integer>` we can do something like:

```java
String[] keys = new String[] {"A", "B", "C"};

seq(keys)
  .mapVals(keys.length, ints().supplier())
  .consume(System.out::println);

// Output
/**
{A=-1153871365, B=1543379867, C=221800466}
*/
```          

## Text Generators

[`strings()`](../docs#strings) is the standard text generator that can be used to generate unformatted, *gibberish* strings witha given format and having a fixed length.

Fortunately more advanced generators were added:

### [`fmt()`](../docs#fmt)

[`fmt(String template).param(namedParam, mockunit)`](../docs#fmt) is a data generator that can be used to generate "formatted" text using named params. The format is defined in the `template` parameter. Internally the `fmt()` uses the [Aleph Formatter](https://github.com/nomemory/aleph-formatter) library.

Example for generating a list of 10 `String` values having following format: `#{mac} -> #{ip}`

```java
fmt("#{mac} -> #{ip}")
    .param("mac", macs().type(COLON_EVERY_2_DIGITS))
    .param("ip", ipv4s())
    .accumulate(10, "\n")
    .consume(System.out::println);

// Output
/**
02:9b:48:c3:11:c9 -> 79.197.227.84
a8:3f:62:52:47:89 -> 215.26.43.138
d0:f9:60:ee:a8:12 -> 223.178.99.247
55:6c:ac:1c:af:ab -> 243.36.40.68
71:7f:8e:2b:29:f6 -> 139.33.106.157
...
*/    
```


At each "iteration" the `#{mac}` and `#{ip}` *named params* are replaced with values generated from the associated `MockUnits`, in our case `macs()` respectively `#{ipv4s}`.

If we were to re-write the "dice rolling example" (see [`consume()`](#consume) and [`intSeq()`](#intseq)) we can start with the format and fill up the data:

```java
fmt("Roll = #{rollNr}, Dice = #{diceValue}")
      .param("rollNr", intSeq().start(1))
      .param("diceValue", ints().range(1, 7))
    .accumulate(10, "\n")
    .consume(System.out::println);

// Output:
/**
Roll = 1, Dice = 6
Roll = 2, Dice = 3
...
*/    
```

### [`regex()`](../docs#regex)

This generator acts as an wrapper for [generex](https://github.com/mifmif/Generex), a java library that can generate arbitrary strings matching a certain regular expression.

You shouldn't expect this generator to be a full-featured reverse-regex engine, but most of the simple regex expression will work.

Think of this as an "experimental" feature, heavily reliant on a third party.

Example for generating 5 phone numbers:
```java
regex("\\d{3}-\\d{3}-\\d{4}")
        .accumulate(5, "\n")
        .consume(System.out::println);

//Output:
/**
869-374-6856
444-775-2444
443-425-7671
526-144-6383
283-454-1700
*/        
```

### [`markovs()`](../docs#markovs)

[`markovs()`](../docs#markovs) is an experimental (for now) implementation of text generated from using a [Markov Chain](https://en.wikipedia.org/wiki/Markov_chain). The code is "not yet quite there" in terms of efficiency or flexibility, but it can be used in production without any concerns.

For the moment the library cannot build text from "external sources" (eg.: a .txt file), but you can use the predefined types: `MarkovChainType.LOREM_IPSUM` and `MarkovChainType.KAFKA`.

*Note:* Once the [Markov Chain](https://en.wikipedia.org/wiki/Markov_chain) is created in memory (which can be CPU/Memory intensive for bigger texts), the memory never gets released.

Example for generating [Lorem Ipsum](https://en.wikipedia.org/wiki/Lorem_ipsum) text. Each call generates a different text:

```java
markovs()
  .type(MarkovChainType.KAFKA)
  .consume(System.out::println);

//Output
/**
Pulvinar ante. Morbi in dolor lectus. Maecenas et mi vel lorem malesuada hendrerit. Donec id posuere neque. Aliquam pharetra a ex nec congue. Nunc eget bibendum est. Interdum et malesuada fames ac ante ipsum primis in faucibus. Euismod. Massa vivamus nulla. Feugiat. Enim in integer condimentum vel metus dapibus accumsan lobortis vehicula. Enim morbi consequat vestibulum aliquet sit ultrices mi quis. Etiam ligula nulla, sem vel in sed aliquam ligula velit sed porta felis. Quis ante luctus at lorem. Augue sed...
*/  
```

Example for generating text using [Frank Kafka - Metamorphosis](https://en.wikipedia.org/wiki/The_Metamorphosis) as the basis. The generated text might be borderline [NSFW](https://www.urbandictionary.com/define.php?term=NSFW) so I wouldn't use this in business contexts:

```java
markovs()
        .type(MarkovChainType.KAFKA)
        .consume(System.out::println);

// Output:
/**
Down the face of her own mother than stay anywhere near gregor. She rushed over to it; it was his mother with the chief clerk had already had in mind, she wanted to bend one of the rest of him, might be near to death; he could judge this for himself. Meanwhile, it had even become very quiet in the living room where grete had put on a lot of weight and become very unpleasant for the other hand, he made it difficult to eat a little too small, lay peacefully between its four familiar walls. A collection of tex...

*/        
```

## [`words()`](../docs#words)

This is a simple text generator that can be used to retrieve arbitrary English words.

The words are divided by their role in the phrase: NOUN, ADVERB, etc.:

Example for generating words:

```java
String adjective = words().adjectives().get();
      String adverb = words().adverbs().get();
      String noun = words().nouns().get();
      String verb = words().verbs().get();

System.out.printf("adjective=%s\nadverb=%s\nnoun=%s\nverb=%s\n",
      adjective, adverb, noun, verb);

// Output
/**
adjective=piled
adverb=creatively
noun=hydrometers
verb=lunge
*/      
```

As a more advanced example we can create an address generator. The rules for the street names are going to be generated are as follows:

* The address contains the street number in the range [1, 600), at the end;
* The street has a 25% chance of being a `"Lane"`, 25% chance of being a boulevard (`"Blvd."`) and 50% chances of being simply `"Street"`;
* The street name has a 25% of being composed from Adjective + Noun, and 75% chances of being only a Noun.

The example will combine `ints()`, `words()`, `fmt()` and `probabilities()` for generating the desired result:

```java
MockUnitString addressGenerator =
                fmt("#{adj}#{noun} #{suffix} #{nr}")
                    .param("adj", probabilities(String.class)
                                            .add(0.25, words()
                                                        .adjectives()
                                                        .format(CAPITALIZED)
                                                        .append(" ")
                                            )
                                            .add(0.75, "")
                                            .mapToString()
                    )
                    .param("noun", words().nouns().format(CAPITALIZED))
                    .param("suffix", probabilities(String.class)
                                      .add(0.25, "Lane")
                                      .add(0.25, "Blvd.")
                                      .add(0.50, "Street")
                                      .mapToString())
                    .param("nr", ints().range(1, 600));

addressGenerator
  .accumulate(30, "\n")
  .consume(System.out::println);

// Output:
/**
Saprobes Lane 130
Rhenium Street 321
Grained Mainliners Street 466
Tops Beliefs Street 184
Ain Quatrefoil Street 159
Nobbler Lane 190
Screwed Skelf Lane 348
Pomologist Street 127
Squash Lane 398
Inherited Carse Street 448
Garotte Blvd. 46
Cays Lane 364
Raised Numismatics Blvd. 548
*/
```

PS: Take care of the words generated, some of them might be slightly *[NSFW](https://www.urbandictionary.com/define.php?term=NSFW)*.

## User Data Generators

The following generators are useful to generate that is normally associated with an User.

| Data Generator | MockUnit | Description |
|----------------|---------|--------------|
| [`emails()`](../docs#emails) | `MockUnitString` | Generates valid email addresses. |
| [`genders()`](../docs#genders) | `MockUnitString` | Generates valid information related to genders. |
| [`names()`](../docs#names) | `MockUnitString` | Generates valid first names, middle names and last names. |
| [`passwords`](../docs#passwords) | `MockUnitString` | Generates passwords with different strengths. |
| [`users()`](../docs#users) | `MockUnitString` | Generates usernames. |

Example for generating a `List<String>` of 10 email addresses and their associated passwords:

```java
fmt("{email='#{email}', pass='#{pass}'}")
  .param("email", emails())
  .param("pass", passwords().types(WEAK, MEDIUM))
  .accumulate(10, "\n")
  .consume(System.out::println);

//Output:
/**
{email='stalkedharl@msn.com', pass='satchels'}
{email='confusedfilth@comcast.net', pass='C>}~0n[9F34V'}
{email='taughtstreamings@hotmail.co.uk', pass='`5l}+13e?+FW;'}
{email='loathdanielle@mail.com', pass='1r;4++$5Q1724]'}
{email='eathluckner@yahoo.co.uk', pass='plugs9'}
{email='allwade@hotmail.co.uk', pass='probe4'}
{email='jimpmarlo@mac.com', pass='swoosh'}
{email='moldysardi@me.com', pass='pAw+2+/8TYG:73'}
{email='nextwhirl@yahoo.co.uk', pass='fist79'}
{email='piquebore@aol.com', pass='compote'}
*/
```

# Composing Objects

Creating object generators with **mockneat** can be done in 4 ways:
- Reflection: setting fields directly (`reflect()`), calling a constructor (`constructor()`) or a static factory method (`factory()`);
- Through setters using lambda expressions (`filler()`).

## [filler()](../docs#filler)

This is the preferred way of creating custom `MockUnit<T>` generators for any given type `<T>`. The only limitation is that setters need to be available (declared as `public`).

Let's take for example a simple [POJO](https://en.wikipedia.org/wiki/Plain_old_Java_object) called `User`:

```java
public class User {
    private String id;
    private String firstName;
    private String lastName;
    private String middleName;
    private Date birthDate;
    private String email;

    public User() {
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    // ++ Additional Getters And Setters for all the fields
    // ++ a nicely implemente toString() method
}
```

Creating an `User` generator (`MockUnit<User>`) with `filler()` is as simple as:

```java
/**
* This method is used to create an email from a firstName and a lastName.
* All emails generated belong to "@company.com"
*/
BiFunction<String, String, String> emailComposer = (firstName, lastName) -> {
    String fName = firstName.toLowerCase();
    String lName = lastName.toLowerCase();

    return fName +
           chars().from(new char[]{'_', '.'}).get() + // the names are separated with "." or "_"
           lName +
           ints().bound(100).get() + // additional numbers are added after the lastName
           "@company.com";
};

/**
* Creating the user generator
*/
MockUnit<User> userGenerator =
               filler(() -> new User())
                       .setter(User::setFirstName, names().first())
                       .setter(User::setLastName, names().last())
                       .setter(User::setMiddleName, names().first())
                       .setter(User::setId, uuids())
                       .setter(User::setBirthDate, localDates().toUtilDate())
                       .map(user -> {
                           user.setEmail(emailComposer.apply(user.getFirstName(), user.getLastName()));
                           return user;
                       });
```

As you can see this strategy doesn't involve any "magic". The `filler()` method expects a `Supplier<User>` (we are going to use the `No Args` constructor of the POJO), then we need to reference each setter and the associated generator (`MockUnit<T>`).

The `userGenerator` can then be used to create `User` objects:

```java
/** Creates a single user */
User someUser = userGenerator.get();

/** Creates a List of 10 user */
List<User> users = userGenerator.list(10).get();

/** Prints 10 users on the console (System.out) */
userGenerator
  .mapToString() // Calls the (toString() method on each User - needs to be implemented)
  .accumulate(10, "\n")
  .consume(System.out::println);

// Output
/**
User{id='936946ef-0407-441c-bd13-56998e934781', firstName='Luana', lastName='Bontempo', middleName='Lincoln', birthDate=Sun Mar 18 00:00:00 EET 1979, email='luana.bontempo48@company.com'}
User{id='c84930a6-a9cf-45da-982c-a76e6f23928d', firstName='Paulina', lastName='Rina', middleName='Norbert', birthDate=Wed Jan 10 00:00:00 EET 1973, email='paulina.rina48@company.com'}
User{id='e9d19b1b-4e3b-4c34-a54f-c8bd880d0e0b', firstName='Rosie', lastName='Staack', middleName='Gilbert', birthDate=Wed Dec 25 00:00:00 EET 1985, email='rosie.staack25@company.com'}
User{id='b0824444-86c6-4984-a820-0adca34ea829', firstName='Eryn', lastName='Burchfiel', middleName='Lanny', birthDate=Thu Sep 21 00:00:00 EET 1972, email='eryn.burchfiel62@company.com'}
User{id='c34631f1-1ea7-4a74-a8ac-276fa5787ba3', firstName='Louise', lastName='Kallus', middleName='Nathan', birthDate=Wed May 31 00:00:00 EEST 1989, email='louise.kallus41@company.com'}
User{id='d3791c93-fe6e-4d12-8109-5b4aece9c25a', firstName='Tressie', lastName='Andrson', middleName='Leroy', birthDate=Wed Nov 09 00:00:00 EET 2016, email='tressie.andrson31@company.com'}
User{id='8c170122-8db3-4fb5-ae29-a42eff2b8a89', firstName='Dixie', lastName='Piggee', middleName='Heriberto', birthDate=Sat Apr 13 00:00:00 EEST 2002, email='dixie.piggee40@company.com'}
User{id='a0f74109-b468-4933-8907-b573de66ba7d', firstName='Adelle', lastName='Pecinousky', middleName='Reuben', birthDate=Fri Apr 12 00:00:00 EEST 2013, email='adelle.pecinousky94@company.com'}
User{id='23e8b4ca-8b83-45ef-a670-8f23c93a25cc', firstName='Ivette', lastName='Rodillas', middleName='Van', birthDate=Tue Nov 29 00:00:00 EET 2005, email='ivette_rodillas36@company.com'}
User{id='16ca3877-636e-4a21-9d12-ada0248e7038', firstName='Corrine', lastName='Eichner', middleName='Sylvester', birthDate=Mon Jan 27 00:00:00 EET 2003, email='corrine_eichner17@company.com'}
*/  
```

## [constructor()](../docs#constructor)

This is an alternative way for creating Object (`<T>`) generators (`MockUnit<T>`).

Internally the method uses reflection trying to to match the supplied list of `MockUnit<T1>, MockUnit<T2>..., MockUnit<Tn>` generators with a class constructor that accepts a list of arguments `<T1>, <T2>, ..., <Tn>`.

The safest scenario when this generator can be used is when the POJO has an All Args Constructor defined.

For example for the `User` class:

```java
public class User {

    private String id;
    private String firstName;
    private String lastName;
    private String middleName;
    private Date birthDate;
    private String email;

    // The All Args Constructor <String, String, String, String, Date, Email>
    public User(String id, String firstName, String lastName, String middleName, Date birthDate, String email) {
        this.id = id;
        this.firstName = firstName;
        this.lastName = lastName;
        this.middleName = middleName;
        this.birthDate = birthDate;
        this.email = email;
    }

    // ++ Getters and Setters (they are not really relevant for this example)
    // ++ A nice toString() implementation
}
```

The generator can be created like this:

```java
MockUnit<User> ctrUserGenerator =
               constructor(User.class).params(
                   uuids(), // MockUnitString -> matches String
                   names().first(), // MockUnitString -> matches String
                   names().last(), // MockUnitString -> matches String
                   names().first(), // MockUnitString -> matches String
                   localDates().toUtilDate(), // MockUnit<Date> -> matches Date
                   emails().domain("company.com") // MockUnitString -> matches String
               );
```

And then it can be used to generate actual `User` objects like this:

```java
// Generates a single user
User ctrUser = ctrUserGenerator.get();

// Generates a Set of users
Set<User> ctrUsersSet = ctrUserGenerator.set(10).get();

// Prints 2 Users on the console (System.out)
ctrUserGenerator
            .mapToString()
            .accumulate(2, "\n")
            .consume(System.out::println);

// Output
/**
User{id='9b0a8f06-b74b-4048-a2f9-78379ad9eb06', firstName='Bill', lastName='Mordino', middleName='Milton', birthDate=Tue Feb 08 00:00:00 EET 1972, email='gibbedpouche@company.com'}
User{id='785ab49d-e359-4953-8ac1-dc648e7213a8', firstName='Courtney', lastName='Rieff', middleName='Wilfredo', birthDate=Mon Oct 25 00:00:00 EEST 1999, email='hookedmarc@company.com'}
*/            
```

## [`factory()`](../docs#factory)

Objects are not always created directly through constructors, in this case the easiest way to create an arbitrary `Object <T>` generator is to use the [`factory()`](../docs#factory) method.

Let's say that our `User` object doesn't have an accessible constructor outside the package where it was defined:

```java
public class User {

    private String id;
    private String firstName;
    private String lastName;
    private String middleName;
    private Date birthDate;
    private String email;

    // The only constructor is not accesible
    protected User() {
    }

    // ++ more code here
}
```

An `UserFactory` class exists in the same package as `User` and it contains a **static** method to create `User` objects:

```java
public class UserFactory {
    public static User createUser(String id, String firstName, String lastName, String middleName, Date birthDate, String email) {
        User user = new User();

        user.setId(id);
        user.setFirstName(firstName);
        user.setLastName(lastName);
        user.setMiddleName(middleName);
        user.setBirthDate(birthDate);
        user.setEmail(email);

        return user;
    }
}
```

In this case the solution will be to call the `factory(targetClass, factoryClass)`:

```java
// Print an arbitrary user to console (System.out)
factory(User.class, UserFactory.class)
                .method("createUser") // Invoked through reflection
                .params(
                        uuids(),
                        names().first(),
                        names().last(),
                        names().first(),
                        localDates().toUtilDate(),
                        emails().domain("company.com")
                )
                .consume(System.out::println);

//Output
/**
User{id='14a55707-83e2-4b44-9793-5b6bfa6b72ee', firstName='Ramon', lastName='Edeker', middleName='Marcel', birthDate=Sun May 14 00:00:00 EEST 2006, email='trussedmarquis@company.com'}
*/                
```                

## [`reflect()`](../docs#reflect)

As the name suggest [`reflect()`](../docs#reflect) use reflection at field level to create objects. A No Args constructor should exist on the class, otherwise the object cannot be created.

```java
public class User {

    private String id;
    private String firstName;
    private String lastName;
    private String middleName;
    private Date birthDate;
    private String email;

    // Should be available!
    public User() {
    }
}
```

The simplest way to create a generator in this case is to:

```java
MockUnit<User> rUserGenerator =
reflect(User.class)
    .field("id", uuids())
    .field("firstName", names().first())
    .field("lastName", names().last())
    .field("middleName", names().first())
    .field("birthDate", localDates().toUtilDate())
    .field("email", emails());

System.out.println(rUserGenerator.get());
```

Setters are not used in this case, the values are "forced" at field level. It's important to remember this in case setters contain their own logic (which shouldn't be the case most of the times), because that logic/code will never be called.

By default, an unspecified `field()` remains `null`. If the actual data is not important, but the field needs to be populated with "something", default data generators can be enabled with `useDefaults(true)`:

```java
MockUnit<User> rUserGenerator = reflect(User.class)
                                            .useDefaults(true);

System.out.println(rUserGenerator.get());

// Output
/**
User{id='MbA0Ceett9bIxsAcbggNa0LsDyKrOANf', firstName='41U8JJKtklOAYHZJ07tz3BFoePocUpFH', lastName='L8oiM0bPU2PoMMtAPwpzlpbZJB9tWf9n', middleName='YBbMC8ZSROnErZdL6RuxXRja5e657Gz6', birthDate=null, email='UwaYRbPfEfJFjHL5HsJCD2B8EnbAYMTO'}
*/
```

Default data generators exist for the following types (and their wrappers if that's the case):

| Type | Data Generator |
| ---- | -------------- |
| `boolean` or `Boolean` | `mockNeat.bools()` |
| `char` or `Character` | `mockNeat.chars().letters()` |
| `double` or `Doubles` | `mockNeat.doubles().bound(10)` |
| `float` or `Float`  | `mockNeat.floats().bound(10)` |
| `int` or `Integer`  | `mockNeat.ints().bound(100)` |
| `long` or `Long`   | `mockNeat.longs().bound(100)` |
| `short` or `Short`  | `mockNeat.ints().bound(100).map(Integer::shortValue)` |
| `String`  | `mockNeat.strings().size(32)` |

In the previous example, the `birthDate=null`. This happens because by default there's no default generator associated with the `Date.class`.

Adding new default generators or overriding existing ones can be done by calling `type(Class<T1> cls, MockUnit<T1> mockUnit)`.

For example if we want to populate the `Date` fields by default with something, we can write something like this:

```java
MockUnit<User> rUserGenerator = reflect(User.class)
                                            .useDefaults(true)
                                            .type(Date.class, localDates().thisYear().toUtilDate());

System.out.println(rUserGenerator.get());

// Output
/**
User{id='eQ2kHfNsB3JUyGZsGzY8G0eD6knmy3CE', firstName='DkBb98qPh4aWxJwFf8DH6TPfuMbOuiUq', lastName='qjWlN7cECvsWdBAse0gHmobxW2lVI8GG', middleName='M3WZmHtHgrNeFV8chxm1z6n9PpHA8YlO', birthDate=Wed Apr 11 00:00:00 EEST 2018, email='DWLqd2tcqXZohIhLRi9SsjUMIoUBw5JW'}
*/
```

# More formatted data

## CSV

There is not a single way to generate [CSV](https://en.wikipedia.org/wiki/Comma-separated_values) files. A few alternatives exists.

One may use a combination of [`fmt()`](../docs#fnt) and [`accumulate()`](../docs#accumulate), like in the following example:

```java
fmt("#{id}, #{fullName}, #{someCode}")
            .param("id", uuids())
            .param("fullName", names().full().escapeCsv())
            .param("someCode", fmt("#{prefix}#{num}")
                                        .param("prefix", chars().lowerLetters())
                                        .param("num", ints().range(0, 100))
            )
            .accumulate(10, "\n")
            .consume(str -> { /* write string to disk */ });
```

And if we `cat` the resulting file, the content might look like:

```
ff13215f-da88-45c5-b77d-38ef02c1b13e, Annemarie Paramore, v60
dcfa9478-d36e-436c-ba53-2a89b1ff0fe6, Paul Giacomini, c86
607b462b-f173-4ee6-a569-4a4422698704, Malcolm Adey, k14
e3005ced-47ec-451c-8b5e-d0f0960e2b48, Nichol Hausteen, y34
d7961c05-c58a-4144-afeb-53eb6722c46b, Wesley Blyther, y58
2d101477-307b-48ee-8271-8cbd289755dc, Adalberto Macleod, l71
84269fdf-e0a3-4d4f-9d65-fdeb123da4f2, Jessika Horwitz, e84
7055aff8-3539-4d01-b7cb-247be5cdbab6, Jackson Hibdon, y90
3d79d066-f2eb-4ba8-95de-745d0bdba351, Patrick Carignan, b10
2f2a48c3-724c-4f91-a237-a5462060ae44, Lanette Steidel, u17
```

A more concise way of achieving the same results is to use directly the [`csvs()`](../docs#csvs) generator.

This is probably the safest and preferred way of generating valid CSVs because each column has it's content escaped by default, and there's no need to explicitly call `escapeCsv()`:

```java
String csv = csvs()
              .column(intSeq())
              .column(names().first())
              .column(names().last())
              .column(emails())
              .column(money().locale(Locale.GERMANY).range(1000, 5000))
              .separator("|")
              .accumulate(20, "\n")
              .get();

System.out.println(csv)                         

// Output
/**
0|Hal|Gannett|grouserefugio@live.com|"2.907,94 €"
1|Ricardo|Kirsch|thankfuldolan@gmail.com|"3.123,97 €"
2|Nolan|Inglis|webbedjustina@yahoo.co.uk|"3.418,80 €"
3|Sonny|Pareja|wrothjuliana@hotmail.com|"1.041,76 €"
....
*/
```

`csv()` offers a shortcut `write(fileName)` method that will allow the user to persist the data directly on the disk.

In case the file cannot be written on the disk, an `UncheckedIOException` will be thrown:

```java
csvs()
  .column(intSeq())
  .column(names().first())
  .column(names().last())
  .column(emails())
  .column(money().locale(Locale.GERMANY).range(1000, 5000))
  .separator("|")
  .write("filename1.csv", 100); // HERE!
```

```
> cat filename1.csv

0|Monte|Probasco|thrumutch@hotmail.com|"2.734,62 €"
1|Harrison|Adebisi|densestschipper@yahoo.com|"4.006,14 €"
2|Kristofer|Tavolieri|dreadsporters@yahoo.co.uk|"3.937,35 €"
3|Royce|Magpusao|auldlino@live.com|"1.532,08 €"
4|Reinaldo|Montori|chasmicdecos@gmx.com|"3.523,23 €"
5|Leonel|Waack|eastwardrousts@comcast.net|"3.835,48 €"
6|Rex|Pyo|pinguiddillaman@msn.com|"2.122,81 €"
7|Perry|Panagos|miffedpop@yahoo.co.uk|"3.201,20

...more
```

## JSON and XML

Arbitrary json (or XML) data can be obtained without any specialised generator.

The sole idea is to create an `Object` through [`filler()`](#filler), [`constructor()`](#constructor), [`factory`](#factory) or [`reflect()`](#reflect) and the use a third party JSON/XML library and `map()`. My library of choice when it comes to JSON is [gson](https://github.com/google/gson) and for XML it's JAXB.

This come with an apparent limitation, a set of additional classes should be created (if they don't exist) to represent the JSON structure.

Example:

```java
private static class User {
    String firstName;
    String lastName;
    String email;
    String creditCard;

    // Constructors
    // Getters and Setters
}
```

We will start by defining the actual Object (`User`) generator `MockUnit<User>`. The preferred way of creating this should be [`filler()`](#filler).


```Java
MockUnit<User> generator = filler(() -> new User())
                .setter(User::setFirstName, names().first())
                .setter(User::setLastName, names().last())
                .setter(User::setEmail, emails())
                .setter(User::setCreditCard, creditCards().type(VISA_16));
```

Then same generator can be reused for both JSON:

```java
// Create JSON
Gson gson = new GsonBuilder()
                  .setPrettyPrinting()
                  .create();

generator
  .map(gson::toJson)
  .consume(System.out::println);

// Output
/**
{
  "firstName": "Frank",
  "lastName": "Bambrick",
  "email": "renthugh@hotmail.co.uk",
  "creditCard": "4857279349007472"
}
*/
```

And XML:

```java
final Marshaller jaxbMarshaller = newInstance(User.class, User.class)
               .createMarshaller();

jaxbMarshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);

Function<User, String> toXML = (users) -> {
  StringWriter productsWriter = new StringWriter();
  try {
    jaxbMarshaller.marshal(users, productsWriter);
  } catch (JAXBException e) {
    e.printStackTrace();
  }
  return productsWriter.toString();
};

generator
  .map(toXML)
  .consume(System.out::println);

// Output
/**
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<user>
    <creditCard>4783503103187272</creditCard>
    <email>baccatetew@att.net</email>
    <firstName>Geraldo</firstName>
    <lastName>Cler</lastName>
</user>
*/  
```

Note: In order to make the above example work, the `User` class needs to be decorated with the `@XMLRootElement` annotation (from JAXB).

## SQL Inserts

TODO
