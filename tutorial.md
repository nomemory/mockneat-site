---
layout: single
permalink: /tutorial/
classes: wide
title: tutorial
sidebar:
  nav: "tutorial"
---

This tutorial is intended to describe the ways of working with **mockneat**. It's definitely not a comprehensive description of the various APIs and their usage. For this please check the [official docs](../docs).

*Disclaimer*: www.mockneat.com is a [Jekyll site](https://jekyllrb.com) and it's [open source](https://github.com/nomemory/mockneat-site), so if you see any code errors or you have any suggestions in the approach, explanations, etc. please open a PR. Also English is not my native-language, so "grammer" <sup>sigh</sup> mistakes are inherent and anxious to be corrected.

# The `MockNeat` class

The `MockNeat.class` is the "entry-point" of the library. Think of this as a *fat* factory class, responsible with the instantiation of all of the existing [data generators](../docs#datagenerators). It's the most important, and in most of the cases, the most "invisible" component from the library.

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

## Financial Generators

The following generators are useful to generate "financial" information.

| Data Generator | MockUnit | Description |
|----------------|---------|--------------|
| [`creditCards()`](../docs#creditcards) | `MockUnitString` | Generates valid credit card numbers of different types (eg: `VISA`, `MASTERCARD` or `AMEX`) |
| [`currencies()`](../docs#forexpairs) | `MockUnitString` | Generates valid [Forex Pairs](https://en.wikipedia.org/wiki/Currency_pair) (eg.: "EUR/USD"), currencies codes or currency names (eg.: "Dollar").|
| [`cvvs()`](../docs#cvvs) | `MockUnitString` | Generates valid [CVV](https://en.wikipedia.org/wiki/Card_security_code) numbers for credit cards. |
| [`ibans()`](../docs#ibans) | `MockUnitString` | Generates valid [IBAN](https://en.wikipedia.org/wiki/International_Bank_Account_Number) numbers. |
| [`money()`](../docs#money) | `MockUnitString` | Generate money amounts as `String` (Money Symbol + Sum of Money).|

## Internet Data Generators

| Data Generator | MockUnit | Description |
|----------------|---------|--------------|
| [`domains()`](../docs#domains) | `MockUnitString` | Generates domains suffixes for [URLs](https://en.wikipedia.org/wiki/URL). |
| [`ipv4s()`](../docs#ipv4s) | `MockUnitString` | Generates arbitrary (valid) [IPv4](https://en.wikipedia.org/wiki/IPv4) addresses. |
| [`ipv6s()`](../docs#mapkeys) | `MockUnitString` | Generates an arbitrary (valid) [IPv6](https://en.wikipedia.org/wiki/IPv6) address. |
| [`macs()`](../docs#macs) | `MockUnitString` | Generates arbitrary (valid) [MAC addresses](https://en.wikipedia.org/wiki/MAC_address). |
| [`urls()`](../docs#urls) | `MockUnitString` | This method can be used to generate random [URLs](https://en.wikipedia.org/wiki/URL). |

## User Data Generators
