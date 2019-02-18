---
layout: single
permalink: /docs/
classes: wide
title: docs
sidebar:
  nav: "docs"
---

Official docs for mockneat.

# Data generators

## `bools()`

This method help us generate `Boolean` values.

Example:
```
boolean b = bools().get();
```

Example for generating a boolean value that has 99.99% of being `true`:
```java
boolean almostAlwaysTrue = bools().probability(99.99).get();
```

## `chars()`

This method helps us generate `Character` values.

Example for generating alpha numeric characters:
```java
char an = chars().alphaNumeric().get();
```

Example for generating a digit (eg: '1', '2', ... , '0'):
```java
Character digit = chars().digits().get();
```

Example for generating a lower letter (eg: 'a', 'b', ..., 'z'):
```java
Character lowerLetter = chars().lowerLetters().get();
```

Example for generating an upper letter (eg.: 'A', 'B', ... , 'Z'):
```java
Character upperLetter = chars().upperLetters().get();
```

Example for generating a hex value:
```java
Character hex = chars().hex().get();
```

Example for generating a character from an "alphabet" of characters:
```java
char aOrBorC = chars().from(new char[]{'a', 'b', 'c'}).get();
char xOrYorZ = chars().from("xyz").get();
```

## `cities()`

This method helps us generate arbitrary city names:

```java
// Generate a city in the us
String city = cities().us().get();
System.out.println(city);

// Generate a world capital
String worldCapital = cities().capitals().get();
System.out.println(worldCapital);

// Generate a capital city in Europe
String europeCapital = cities().capitalsEurope().get();
System.out.println(europeCapital);
```

## `constructor()`

This method is used to generate / mock objects by calling the constructor of the target class (using reflection). The params supplied to the constructor can be `MockUnit` objects or constants.

The method signature is: `<T> Constructor<T> constructor(Class<T> cls)`.

Example for generating `Test` mock objects:

```java
public class Test {
    private String x;
    private Integer y;
    private Boolean z;

    public Test() {}

    // This is the constructor that is going to be called
    public Test(String x, Integer y, Boolean z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }
}
```

```java
Test t2 = constructor(Test.class)
              .params(
                 strings().size(10),
                 ints().range(0, 10),
                 true // constant here
              )
              .get();
// Possible Output:
// Test{x='g4bk67PxlT', y=185, z=false}
```

## `countries()`

This method help us generate Country names (eg.: "Romania", "Israel", "France") or ISO2 codes for countries (eg.: "RO").

Example for generating a country name:

```java
String countryName = countries().names().get();
// Possible Output: Lithuania
```

Example for generating a country iso2() code:

```java
String iso2 = countries().iso2().get();
// Possible Output: MR
```

## `creditCards()`

This method help us generate Credit Cards.

All the supported credit cards types are defined in the `CreditCardType` enum.

Example for generating a valid credit card number (by default the `CreditCardType` is `AMERICAN_EXPRESS`):

```java
String amex = creditCards().get();
```

Example for generating a 16 digits VISA:

```java
String visa16 = creditCards().type(VISA_16).get();
```

Example for generating an arbitrary credit card that can be either `VISA_16` or `MASTERCARD`:

```java
String visaOrMastercard = creditCards()
                           .types(VISA_16, MASTERCARD)
                           .get();
```

Visa, MasterCard and AMEX credit cards have shortcut methods that can be invoked directly without using `type()` or `types()`.

```java
String amex = creditCards().amex().get();
String visa = creditCards().visa().get();
String masterCard = creditCards().masterCard().get();
```

## `creditCards().names()`

This generates the name of a Credit Card.

The possible returning values are: `"American Express"`, `"China Union Pay"`, `"Diners Club"`,  `"Discover"`,  `"Inter Payment"`, `"Insta Payment"`, `"JCB"`, `"Maestro"`, `"Mastercard"`, `"Visa"`.

Example:

```java
String ccName = creditCards().names().get();
```

## `currencies()`

This method is only used to create an instance of the `Currencies` object.

The most important methods attached to the `Currencies` object are:

- [`currencies().name()`](#currenciesname);
- [`currencies().symbol()`](#currenciessymbol);
- [`currencies().code()`](#currenciescode),
- [`currencies().forexPair()`](#currenciesforexpair)

## `currencies().name()`

This method returns currency names from around the world. Some of the possible outputs: "Leu", "Krona", "Pound", etc.

Example:

```java
String currencyName = currencies().name().get();
```

## `currencies().symbol()`

This method returns currency symbols from around the world. Some of the possible outputs: "Lek", "£", "Ls", "$", etc.

Example:

```java
String currencySymbol = currencies().symbol().get();
```

## `currencies().code()`

This method returns currency codes from around the world. Some of the possible outputs: "CZK", "JPY", "SVC", "ANG", etc.

Example:

```java
String currencyCode = currencies().code().get();
```

## `currencies().forexPair()`

This method returns the most common [Forex Pairs](https://en.wikipedia.org/wiki/Currency_pair). Some of the possible outputs: "EUR/GBP", "USD/ZAR", "EUR/GBP", "EUR/HUF", etc.

Example:

```java
String forexPair = currencies().forexPair().get();
```

## `csvs()`

The method is used to generate [csv](https://en.wikipedia.org/wiki/Comma-separated_values) files from other `MockUnit<T>`s.

Example for generating a simple "csv" line:

```java
String csvLine = csvs().column(intSeq())
                                 .column(names().first())
                                 .column(names().last())
                                 .column(emails())
                                 .column(money().locale(Locale.GERMANY).range(1000, 5000))
                                 .separator("|")
                                 .get();
// Possible Output:
// 0|Dortha|Cayouette|passedbartucca@hotmail.com|"4.215,92 €"

```

Example for generating multiple .csv lines and store them in a String:

```java
String csvLine = csvs().column(intSeq())
                         .column(names().first())
                         .column(names().last())
                         .column(emails())
                         .column(money().locale(Locale.GERMANY).range(1000, 5000))
                         .separator("|")
                         .accumulate(20, "\n")
                         .get();

System.out.println(csvLine);

//Possible Output:
/**
0|Hal|Gannett|grouserefugio@live.com|"2.907,94 €"
1|Ricardo|Kirsch|thankfuldolan@gmail.com|"3.123,97 €"
2|Nolan|Inglis|webbedjustina@yahoo.co.uk|"3.418,80 €"
3|Sonny|Pareja|wrothjuliana@hotmail.com|"1.041,76 €"
4|Roscoe|Matuszak|snubkeisha@mail.com|"1.645,30 €"
5|Gaston|Hammill|fraughtbruno@hotmail.com|"3.645,93 €"
6|Deshawn|Majercin|fireproofmacqueen@hotmail.com|"4.944,91 €"
7|Jamel|Brodnax|chastekauder@comcast.net|"4.914,91 €"
8|Elliott|Peron|chirkemerita@email.com|"4.604,48 €"
9|Lance|Latina|voguemoths@mac.com|"2.662,07 €"
10|Travis|Rusert|rushdemetrice@att.net|"3.387,07 €"
11|Norris|Amadio|squintfelicia@email.com|"3.889,18 €"
12|Elisha|Brawley|nextwilber@mail.com|"2.522,31 €"
13|Barton|Bosko|pouchedfrederick@mac.com|"2.433,39 €"
14|Herschel|Dotstry|slumsade@yahoo.co.uk|"4.930,66 €"
15|Ashley|Dorenfeld|sorecarlo@comcast.net|"2.734,22 €"
16|Eric|Cheli|kraalchristi@me.com|"3.498,07 €"
17|Norberto|Arechiga|milledstupes@comcast.net|"3.908,81 €"
18|Marco|Berenguer|seenausiello@verizon.net|"3.280,29 €"
19|Douglas|Kerley|boullemerilyn@mail.com|"2.609,39 €"
*/
```

Example for writing a .csv file on the disk:

```java
csvs().column(intSeq())
        .column(names().first())
        .column(names().last())
        .column(emails())
        .column(money().locale(Locale.GERMANY).range(1000, 5000))
        .separator("|")
        .write("test.csv", 100);
```

## `cvvs()`

This method helps us generate [CVV](https://en.wikipedia.org/wiki/Card_security_code) codes. Currently it supports only two types of CVV. Those types are defined into the enum called `CVVType`. For the moment there are only two types defined:
- `CVV3` - Represents a 3 digits random String.
- `CVV4` - Represents a 4 digits random String.

By default the type we use is: `CVV3`.

Example for generating a 3-digit CVV:

```java
String cvv = cvvs().get();
```

Example for generating a 4-digit CVV:

```java
String cvv4 = cvvs().type(CVV4).get();
```

## `dicts()`

The library includes a set of internal dictionaries.

The dictionaries are plain-text files that are included in the `.jar` file.

Each of these files are mapped through an enum called `DictType`.

The current list of `DictType` is:

| Enum Value | Contents |
|:---------- |:---------|
|`COUNTRY_NAME`| Contains an exhaustive list of country names. Possible values: "Chile", "Saudi Arabia", etc.|
|`COUNTRY_ISO_CODE_2` | Contains an exhaustive list of country iso codes. Possible values: "AM", "UK", etc. |
|`DOMAIN_EMAIL` | Contains a list of possible email domains. This dictionary is used internally by [`emails()`](#emails). Possible values: "gmail.com", "msn.com", etc. |
|`DOMAIN_TOP_LEVEL_ALL`| Contains an exhaustive list of possible URL domains (suffixes). This dictionary is used internally by [`urls()`](#urls). Possible values: "com", "comcast", "ml", etc. |
|`DOMAIN_TOP_LEVEL_POPULAR`| Contains a list of the most popular URL domains (suffixes). This dictionary is used internally by [`urls()`](#urls). Possible values: "com", "org", "net", etc. |
|`FOREX_PAIRS`| Contains a list of the most popular Forex Currency Pairs. This dictionary is used internally by [`currencies().forexPair()`](#currenciesforexpair). Possible values: "USD/MXN", "USD/NOK", "USD/PLN", etc. |
|`CREDIT_CARD_NAMES`| Contains a list of the most popular credit card names. This dictionary is used internally by [`creditCards().names()`](#creditcardsnames). Possible values: "Visa", "Mastercard", etc. |
|`FIRST_NAME_MALE_AMERICAN`| Contains the most common first names for males in the US. |
|`FIRST_NAME_FEMALE_AMERICAN` | Contains the most common first names for females in the US. |
|`LAST_NAME_AMERICAN`| Contains the most common last names in the US. |
|`EN_ADJECTIVE_1SYLL`| Contains a list of 1-Syllable English adjectives. |
|`EN_ADJECTIVE_2SYLL`| Contains a list of 2-Syllable English adjectives. |
|`EN_ADJECTIVE_3SYLL`| Contains a list of 3-Syllable English adjectives. |
|`EN_ADJECTIVE_4SYLL`| Contains a list of 4-Syllable English adjectives. |
|`EN_ADVERB_1SYLL`| Contains a list of 1-Syllable English adverbs. |
|`EN_ADVERB_2SYLL`| Contains a list of 2-Syllable English adverbs. |
|`EN_ADVERB_3SYLL`| Contains a list of 3-Syllable English adverbs. |
|`EN_ADVERB_4SYLL`| Contains a list of 4-Syllable English adverbs. |
|`DEPARTMENTS`| Contains a list of possible department names from a company. Possible values: "Insurance", "Inventory", "Licenses" |

Example for generating a country name directly from the dictionary:

```java
String countryName = dicts().type(COUNTRY_NAME).get();
```

Obtaining all the data from the dictionaries in a (immutable) list can be done through the `data()` method:

```java
List<String> cIso2 = dicts().data(DictType.COUNTRY_ISO_CODE_2);
```

## `days()`

This method helps us generate name of the Days of the week.

Example for generating an arbitrary day of the week:

```java
DayOfWeek day = days().get();
// Possible Output: SUNDAY
```

Example for generating an arbitrary day of the week as `String` using `display()`:

```java
String dayStr = days().display(TextStyle.SHORT).get();
// Possible Output: "Tue"
```

Example for generating an arbitrary day of week after "Thursday" using `after()`:

```java
DayOfWeek dayAfterThursday = days().after(THURSDAY).get();
// Possible Output: "FRIDAY"
```

Example for generating an arbitrary day of the week before "Thursday" using `before`:

```java
DayOfWeek dayBeforeThursday = days().before(THURSDAY).get();
// Possible Output: "MONDAY"
```

Example for generating an arbitrary day of the week between [Sunday, Saturday] using `rangeClosed()`:
```java
DayOfWeek weekEnd = days().rangeClosed(SATURDAY, SUNDAY).get();
```

Note: There is also the `range()` method, that uses an open interval.

## `departments()`

This method is used to generate department names from a company.

```java
String dept = departments().get();
// Possible Output: "Insurance"
```

## `domains()`

This method is used to generate domains suffixes for URLs.

There are two types of domain suffixes that can be generated:
- `DomainSuffixType.ALL` - This contains an exhaustive list of domain suffixes;
- `DomainSuffixType.POPULAR` - This is a shorter list of domain suffixes (the most popular ones): "com", "net", "org", etc. If not type is specified this is picked by default.

Example for generating a domain suffix `String`:

```java
String domain = domains().get();
// Possible Output: "io"
```

Example for generating a domain suffix `String` giving the type:
```java
String all = domains().type(ALL).get();
// Possible Output: "analytics"
```
## `doubles()`

This method is used to generate double values.

Example for generating a single double value in the interval [0.0, 1.0):

```java
Double val = doubles().get();
// Possible Output: 0.26378031782078615
```

Example for generating a single double value in interval [0.0, bound):

```java
Double bound = 10.0;
Double boundedVal = doubles().bound(bound).get();
// Possible Output: 7.9842438463207905
```

Example for generating a single double value in a given range [100.0, 200.0):

```java
Double valInRange = doubles().range(100.0, 200.0).get();
// Possible Output: 194.88613464097585
```

## `emails()`

This method is used to generate emails.

Example for generating a random email address:

```java
String email = emails().get();
// Possible Output: icedvida@yahoo.com
```

Example for generating a random email with a fixed "domain". This is useful when generating emails for a specific "company".

```java
String corpEmail = emails().domain("startup.io").get();
// Possible Output: tiptoplunge@startup.io
```

Example for generating an email with fixed "domains":

```java
String domsEmail = emails().domains("abc.com", "corp.org").get();
// Possible Output: funjulius@corp.org
```

## `factory()`

This method is used to generate mock objects using static factory methods.

Example:

`Test.java` class:

```java
public class Test {
    private String x;
    private Integer y;
    private Boolean z;

    public Test() {}

    public Test(String x, Integer y, Boolean z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }
    //....
}
//....
```

`TestFactory.class` is a class that contains static methods used to create `Test` instances.

```java
public class TestFactory {
    public static Test buildTest(String x, Integer y, Boolean z) {
        return new Test(x, y, z);
    }
    // ...
}
// ...
```

The `buildTest` method can be used to instantiate mock Test objects like this:

```java
Test t3 = factory(Test.class, TestFactory.class)
              .method("buildTest")
              .params(strings(), 1, true)
              .get();

// Possible Output: Test{x='6pmmWFiAEPW35dUj9sjcnOaglfXO7hIoyu38UK395pZ8Ns1dPJSkpz0Sg0C4IVVA', y=1, z=true}
```

## `files()`

This method is useful to generate random lines from a given file.
The file is loaded in memory only once, so the recommendation is to avoid keeping large files into memory.

Example to generate a random line from a file `/Users/nomemory/Desktop/test.txt`. The file contents are:

```
text1
text2
text3
text4
```

```java
String line = files().from("/Users/andreinicolinciobanu/Desktop/test.txt").get();
// Possible Output: text2
```

## `filler()`

The `Filler` class implements `MockUnit<T>`. It is used to generate mock instances using `Supplier<T>` instead of reflection.

```java
List<User> users = filler(() -> new User())
                      .setter(User::setUserName, users())
                      .setter(User::setFirstName, names().first())
                      .setter(User::setLastName, names().last())
                      .setter(User::setCreated, localDates().thisYear().toUtilDate())
                      .setter(User::setModified, localDates().thisMonth().toUtilDate())
                    .list(() -> new ArrayList<>(), 10) // Collecting all the results ina  List of 10 elements.
                    .get();
```

## `floats()`

This method is used to generate float numbers.

Example to generate a single float value in the interval [0.0f, 1.0f):

```java
Float val = floats().get();
// Possible Output: 0.414554
```

Example to generate a single float value in interval [0.0f, bound), where bound=10.0f:

```java
Float boundVal = floats().bound(10f).get();
// Possible Output: 2.0780544
```

Example to generate a single double value in a given range [100.0f, 200.0f)

```java
Float rangeVal = floats().range(100.0f, 200.0f).get();
// Possible Output: 163.93002
```

## `fmt()`

This method is used to generate formatted `String` values. Each specified param is a named one `#{param1}`.

Example for generating a random string in the form: `#{digit1}#{digit2}#{letter}#{specialChar}` (eg.: `"12a@"`):

```java
String temp = "{#{d1}#{d2}#{l1}#{sc}";
String result = fmt(templ)
                    .param("d1", chars().digits())
                    .param("d2", chars().digits())
                    .param("l1", chars().letters())
                    .param("sc", from(SPECIAL_CHARACTERS))
                    .get();
// Possible Output: 45q'
```

Note: Other than `MockUnit`s the `param(<param name>, ...)` accepts also a constant `String` as a second parameter.

## `from()`

This method is used to return a random value from a pre-existing `List<T>`, `T[]` or `Class<T extends Enum<?>>`.
The method directly returns a `MockUnit<T>`.   

Example for obtaining a random value from a List<Double>:

```java
List<Double> list = asList(0.0, 0.1, 0.5, 0.6);
Double x = from(list).get();
// Output:
/* 0.5
*/
```

Note: If the `List` is implemented as an `ArrayList` the complexity of the algorithm will be `O(1)`. If it's a `LinkedList` the complexity will be `O(n)`.

Example for obtaining a random value from a String[]:

```java
String[] arr = {"abc", "acd", "adf" };
String s = from(arr).get();
// Possible Output: "adf"
```

Example for obtaining a random value from an Enum:

```java
DayOfWeek d = from(DayOfWeek.class).get();
// Possible Output: d
```

## `fromKeys()`

This method is used to return a random key from a `Map`. The keys of the map are copied internally into an array, and afterwards the next key is randomly picked from there.

The copying of the key elements is an expensive operation so it's not recommended to use this method, especially for large Maps.

## `fromValues()`

This method is used to return a random value from a `Map`. The values of the map are copied internally into an array, and afterwards the next value is randomly picked from there.

The copying of the value elements is an expensive operation so it's not recommended to use this method, especially for large Maps.

## `genders()`

This `Gender` class implements `MockUnitString`. It is used to generate (`"Male"`, `"Female"`) values:

```java
String gender = genders().get();
// Possible Output: "Female"
```

Or shortcut letters:

```java
String shortGender = genders().letter().get();
// Possible Output: "M"
```

## `hashes()`
This is a helper method that groups the following methods for generating hex hashes using different algorithms:
* [`hashes().md2()`](#hashesmd2);  
* [`hashes().md5()`](#hashesmd5);
* [`hashes().sha1()`](#hashessha1);
* [`hashes().sha256()`](#hashessha256);
* [`hashes().sha384()`](#hashessha384);
* [`hashes().sha512()`](#hashessha512).

## `hashes().md2()`

This method generates a MD2 Hash from a random String:

```java
String str = hashes().md2().get();
```

## `hashes().md5()`

This method generates a MD5 Hash from a random String:

```java
String str = hashes().md5().get();
```

## `hashes().sha1()`

This method generates a SHA1 Hash from a random String:

```java
String str = hashes().sha1().get();
```

## `hashes().sha256()`

This method generates a SHA256 Hash from a random String:

```java
String str = hashes().sha256().get();
```

## `hashes().sha384()`

This method generates a SHA384 Hash from a random String:

```java
String str = hashes().sha384().get();
```

## `hashes().sha512()`

This method generates a SHA512 Hash from a random String:

```java
String str = hashes().sha512().get();
```

## `ibans()`

This method is used to generate possible IBAN codes. The implementation is quite naive because it doesn't take in consideration information like Branch Code Number, Account Number, National Bank Code, etc.

It only matches the country's prefix, character configuration as defined in the standard, plus the correct length.

```java
String iban = ibans().type(GERMANY).get();
```

## `industries()`

This method is used to generate possible "Industrial Fields" as strings.

Eg.: "Sporting Goods, Hobby, Book, and Music Stores", "Support Activities for Agriculture and Forestry", "Support Activities for Mining".

## `ints()`

This method is used to generate int numbers.

Example for generating int numbers (negative or positive):

```java
Integer i1 = ints().get();
// Possible Output :1952620297
```

Example for generating int numbers bounded by a certain value: [0, bound), where bound = 10:

```java
Integer bounded = ints().bound(10).get();
// Possible Output: 4
```

Example for generating int numbers in a certain open range: [10, 20):

```java
Integer ranged = ints().range(10, 20).get();
// Possible Output: 19
```

## `intSeq()`

This method is used to generate sequence of numbers (integers).

Example for generating a sequence of integers starting with 0, incrementing each time with 1.

```java
IntSeq seq = intSeq();
for(int i = 0; i < 20; i++) {
      System.out.print(seq.get() + " ");
}
// Output: 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19
```

Example for generating a sequence of integers starting with 5, incrementing with 2, and if it reaches a value bigger than 20 it resets back to 5:

```java
IntSeq seq2 = intSeq()
                  .start(5)
                  .increment(2)
                  .max(20)
                  .cycle(true);
for(int i = 0; i < 50; i++) {
    System.out.print(seq2.get() + " ");
}
// Output: 5 7 9 11 13 15 17 19 5 7 9 11 13 15 17 19 5 7 9 11 13 15 17 19 5 7 9 11 13 15 17 19 5 7 9 11 13 15 17 19 5 7 9 11 13 15 17 19 5 7
```

Negative increments work, but instead of setting a `max()` value a `min()` needs to be set, otherwise it will default to `Integer.MIN_VALUE`.

## `ipv4s()`

This method is used to generate arbitrary IPv4 addresses.

An enum `IPv4Type` exists and it describes each possible IPv4 classes: `CLASS_A`, `CLASS_A_LOOPBACK`, `CLASS_A_PRIVATE`, `CLASS_B`, `CLASS_B_PRIVATE`, `CLASS_C`, `CLASS_C_PRIVATE`, `CLASS_D`, `CLASS_E`, `NO_CONSTRAINT`.

Example for generating an IPv4 address that has no class constraint:

```java
String ipv4 = ipv4s().get();
// Possible Output: 192.21.139.180
```

Example for generating an IPv4 address from Class A:
```java
String ipClassA = ipv4s().type(CLASS_A).get();
// Possible Output: 57.253.133.154
```

Example for generating an IPv4 address from Class A or Class B:
```java
String classAorB = ipv4s().types(CLASS_A, CLASS_B).get();
// Possible Output: 119.110.27.146
```

## `ipv6s()`

This method is used to generate an arbitrary IPv6 address.

Example:

```java
String ipv6 = iPv6s().get();
// Possible Output: 35f1:b02f:8843:9abb:82bf:967a:34f5:ed8b
```

## `issns()`

This method is used to generate arbitrary ISSN codes.

Example:
```java
String issn = issns().get();
// Possible Output: ISSN 6105-1470
```

## `localDates()`

This method is used to generate arbitrary `LocalDate` objects.

Example of generating a random date in the past between: [1970, now()):

```java
LocalDate localDate = localDates().get();
// Possible Output: 1984-05-10
```

Example for generating a random date in the past between: [1987-1-30, now()):

```java
LocalDate min = LocalDate.of(1987, 1, 30);
LocalDate past = localDates().past(min).get();
// Possible Output: 2014-05-30
```

Example for generating a random date in the future between: [now(), 2020-1-1);

```java
LocalDate max = LocalDate.of(2020, 1, 1);
LocalDate future = localDates().future(max).get();
// At this moment this is the possible output: 2019-08-03
```

Example for generating a random date between [1989-1-1, 1993-1-1):

```java
LocalDate start = LocalDate.of(1989, 1, 1);
LocalDate stop = LocalDate.of(1993, 1, 1);
LocalDate between = localDates().between(start, stop).get();
// Possible Output: 1989-02-04
```

## `longs()`

This method is used to generate int numbers.

Example for generating long numbers (negative or positive):

```java
Long i1 = longs().get();
// Possible Output : -2445487402940283422
```

Example for generating long numbers bounded by a certain value: [0, bound), where bound = 10:

```java
Long bounded = longs().bound(10).get();
// Possible Output: 8
```

Example for generating long numbers in a certain open range: [10, 20):

```java
Long ranged = longs().range(10, 20).get();
// Possible Output: 19
```

## `longSeq()`

This method is used to generate sequence of numbers (longs).

Example for generating a sequence of longs starting with 0l, incrementing each time with 1l.

```java
LongSeq seq = longSeq();
for(int i = 0; i < 20; i++) {
    System.out.print(seq.get() + " ");
}
// Output: 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19
```

Example for generating a sequence of longs starting with 5, incrementing with 2, and if it reaches a value bigger than 20 it resets back to 5:

```java
LongSeq seq2 = longSeq()
                   .start(5)
                   .increment(2)
                   .max(20)
                   .cycle(true);
for(int i = 0; i < 50; i++) {
    System.out.print(seq2.get() + " ");
}
// Output: 5 7 9 11 13 15 17 19 5 7 9 11 13 15 17 19 5 7 9 11 13 15 17 19 5 7 9 11 13 15 17 19 5 7 9 11 13 15 17 19 5 7 9 11 13 15 17 19 5 7
```

Negative increments work, but instead of setting a `max()` value a `min()` needs to be set, otherwise it will default to `Long.MIN_VALUE`.

## `macs()`

This method is used to generate arbitrary MAC addresses.

The MAC address can be formatted in a few ways. Each of these ways is mapped in the enum `MACAddressFormatType`: `DASH_EVERY_2_DIGITS`, `COLON_EVERY_2_DIGITS`, `DOT_EVERY_2_DIGITS`, `DOT_EVERY_4_DIGITS`.

Example for generating a MAC addresses and showing it in the format `DOT_EVERY_4_DIGITS`:
```java
String mac = macs().type(DOT_EVERY_4_DIGITS).get();
// Possible Output: 9a25.aa24.df7c
```

## `markovs()`

This method is used to generate Markov Text.

For the moment this feature is not:
- very memory efficient;
- there aren't many options, but to generate text from Kafka's Metamorphosis or Lorem Ipsum.

Example for generating a 512 characters text:

```java
String text = markovs().size(512).type(KAFKA).get();
//Possible Output: In his efforts to muster all the things that concerned him. This meant not only in secret. For two whole days, all the harder. Across the room came in early in the doorway with a knife, he thought it was easy to see him. He had forgotten, but instead of being afraid, the charwoman stood in the direction of the bedrooms: come and give me a bit of him and seemed, at first, that was quite incapable of going backwards in a nice, gilded frame. It showed a lady fitted out with a few raisins and almonds; some chee

// Or

String loremIpsum = markovs().size(1024).type(LOREM_IPSUM).get();
```

## `mimes()`

This method is used to generate mime types (Eg.: application/msword, application/epub+zip, image/gif, text/html
image/x-icon, etc).

```java
String mime = mimes().get();
// Possible Output: application/vnd.visio
```

## `months()`

This method is used to generate Months names. It is used to return `Month` objects.

Exmaple to generate a `Month`:

```java
Month m = months().get();
// Possible Output: FEBRUARY
```

Example to generate a month in the range `[June, August]` (summer):

```java
Month summer = months().rangeClosed(JUNE, AUGUST).get();
// Possible Output: August
```

Note: You can use the `range()` method for an open-interval.

Example to generate a month before the start of the summer, in range [January, June)

```java
Month beforeSummer = months().before(JUNE).get();
// Possible Output: APRIL
```

## `money()`

This method is used to generate money amounts as Strings (Money Symbol + Sum of Money).

Example to generate a "fortune" in Japan. By fortune I mean a value in [100, 200) interval (YEN):

```java
String japanFortune = money()
                      .locale(JAPAN)
                      .range(100, 200)
                      .get();
// Possible Output: ￥138
```

Example to generate millions of Euros:

```java
String millions  = money()
                    .locale(GERMANY)
                    .range(1000000, 10000000)
                    .get();
Possible Output: 1.604.518,56 €
```

## `names()`

This method is used to generate both first names and last names.

There is an enum class `NameType` that maps names into several categories:
- `NameType.FIRST_NAME` : Contains both female and male first names;
- `NameType.FIRST_NAME_MALE` : Contains only male first names;
- `NameType.FIRST_NAME_FEMALE` : Contains only female first names;
- `NameType.FIRST_NAME_MALE_AMERICAN` : Contains only male first names that are popular in the US;
- `NameType.FIRST_NAME_FEMALE_AMERICAN` : Contains only female first names that are popular in the US;
- `NameType.LAST_NAME` : Contains only last names;

To use those classifications the `names()` method must be used in conjunction with `type()`.

Example to generate name of type `FIRST_NAME_FEMALE` :

```java
String firstName = names().type(FIRST_NAME_FEMALE).get();
// Possible Output: Allie
```

There are also shortcuts for generating first names, last names and full names with optional initials:

Example for generating an arbitrary first name (male or female):

```java
String first = names().first().get();
```

Example for generating an arbitrary last name:

```java
String last = names().last().get();
```

Example for generating a full name that has a 90% chance to contain an "initial":

```java
String withInitial = names().full(90).get();
// Possible Output: Bryce I. Hibma
```

## `naughtyStrings()`

This is used to generate naughty Strings - strings that have a high probability of creating "problems" when they are used as user input.

There is a list of additional supported sub-methods:

- `naughtyStrings().cveVulnerabilities()` ;
- `naughtyStrings().emoji()` ;
- `naughtyStrings().fileInclusions()` ;
- `naughtyStrings().inconousStrings()` ;
- `naughtyStrings().japaneseEmoji()` ;
- `naughtyStrings().msdosSpecialFileNames()` ;
- `naughtyStrings().numeric()` ;
- `naughtyStrings().quotations()` ;
- `naughtyStrings().regionalIndicators()` ;
- `naughtyStrings().reservedKeyWords()` ;
- `naughtyStrings().rightToLeftStrings()` ;
- `naughtyStrings().rubyInjection()` ;
- `naughtyStrings().scriptInjection()` ;
- `naughtyStrings().serverCodeInjection()` ;
- `naughtyStrings().specialChars()` ;
- `naughtyStrings().sqlInjection()` ;
- `naughtyStrings().trickUnicode()` ;
- `naughtyStrings().twoByteChars()` ;
- `naughtyStrings().unicodeFont()` ;
- `naughtyStrings().unicodeNumbers()` ;
- `naughtyStrings().unicodeSubScriptSuperScript()` ;
- `naughtyStrings().unicodeSymbols()` ;
- `naughtyStrings().unicodeUpsideDown()` ;
- `naughtyStrings().unwantedInterpolation()` ;
- `naughtyStrings().xmlInjection()` ;
- `naughtyStrings().zalgoText()` ;

## `probabilities()`

This method is used to generate objects based on associated probabilities.

Example for generating letters with the following probabilities: A - 10%, B - 20%, C - 50%, D - 20%:

```java
String s = probabilites(String.class)
                    .add(0.1, "A")
                    .add(0.2, "B")
                    .add(0.5, "C")
                    .add(0.2, "D")
                    .get();
```

Note: The sum of the probabilities must exactly 1.0 when calling val.

Instead of constants, it's also possible to use any [MockUnit](MockUnits) as parameter.

Example for generating numbers in intervals based on probabilities:
- Generating a number in interval [0, 100) - 20% chance;
- Generating a number in interval [100, 200) - 50% chance;
- Generating a number in interval [200, 300) - 30% chance;

```java
Integer x = probabilites(Integer.class)
             .add(0.2, ints().range(0, 100))
             .add(0.5, ints().range(100, 200))
             .add(0.3, ints().range(200, 300))
             .get();
```

## `passwords()`

This method is used to generate passwords.

Different types of password strengths can be generated, all strengths are mapped into the enum `PassStrengthType`: `WEAK`, `MEDIUM`, `STRONG`.

Example on how to generate a medium-strength password:

```java
String medium = passwords().type(MEDIUM).get();
// Possible Output: cent>ilLion
```

## `primes()`

This method is used to generate (small) prime numbers from the following interval: `[2, 7919]`;

```java
primes()
  .mapToString()
  .accumulate(10, ",")
  .consume(System.out::println);
```

## `reflect()`

This method is used to generate mock objects through reflection.

The full method signature is: `<T> Reflect<T> reflect(Class<T> cls)` where the input parameter `cls` represents the target class.

In order for this method to work the `NO_ARG Constructor` must exist and must be accessible.

The fields that are modified through reflection can be private, but cannot be final.

Example:

```java
Test t = reflect(Test.class)
             .field("x", strings().size(10))
             .field("y", ints().range(100, 200))
             .field("z", bools())
             .get();
// Possible Output: Test{x='QeBhNPZNhK', y=2, z=false}
```

Instead of specifying a `MockUnit` (or constants) for every field the developer can call `useDefaults(true)`. Doing this will activate a fallback mechanism that will populate each `char`, `Character`, `short`, `Short`, `int`, `Integer`, `long`, `Long`, `float`, `Float`, `double`, `Double`, `String` fields by default.

So for example if the `Test` class has 3 fields:
* `x` -> `String`;
* `y` -> `Integer`;
* `z` -> `Boolean`.

We can use the default mechanism by doing this:

```java

Test tR = reflect(Test.class)
                        .useDefaults(true)
                        .get();

System.out.println("tR: " + tR);

// Possible Output:
//         tR: Test{x='KksOzDmsy8kxCYxJs7aU5db4l255E4BC', y=57, z=false}
```

If you want to override the default mechanism you can use the `type(Class<?>, MockUnit|Constant)` method.

So let's take for example a class `Test2` with 5 fields:

```java
public class Test2 {
    private int x;
    private int y;
    private short z;
    private String w1;
    private String w2;
    //....
}
//....
```

We can:
* override the default fallback random generator for all String fields (random string of size 5);
* override the default fallback random generator for all `short` fields (with a constant value of 5);
by doing the following:

```java
Test2 t4 = reflect(Test2.class)
                        .useDefaults(true)
                        .type(String.class, strings().size(5))
                        .type(Short.class, (short) 5)
                        .get();

System.out.println(t4);

//Possible Output:
// Test2{x=78, y=68, z=5, w1='1rlW5', w2='9aO3O'}
```

As you can see in the above example, all `String` fields have now a size of 5, while the `short` field `z` will always be the constant `5`.

Note: The fields are modified directly and not through getter and setters.

## `regex()`

The `Regex` class implements `MockUnitString` and it is used to generate random strings that match a given regex.

This feature is based on the [Generex library](https://github.com/mifmif/Generex).

Only a small subset of regular expressions work. Any non-trivial regex might fail.

Example for generating a `"Lol"` string:
```java
String lolRegex = "LOo{3}L{10,15}!";
String lol = regex(lolRegex).get();
// Possible Output: LOoooLLLLLLLLLLLLL!
```

Example for generating a number that has between 3 and 10 digits:
```java
String numberRegex = "\\d{3,10}";
String number = regex(numberRegex).get();
// Possible Output: 8296331806
```

Example for generating a code with a format: `XX-nnnnn-xxxxx`:
```java
String codeRegex = "[A-Z]{2}-\\d{5}-[a-z]{5}";
String code = regex(codeRegex).get();
// Possible Output: EI-54105-tjfdk
```

## `seq()`

The `Seq<T>` is used to get values by iterating to an existing dictionary (`DictType`) or an `Iterable<T>`.

Iterating through the list. When we reach the "end" we start iterating again.
Take care of concurrency issues:

```java
List<String> list = Arrays.asList("a", "b", "c", "d");
seq(list).cycle(true).list(100).consume(System.out::println);
// Prints ["a", "b", "c", "d", "a", "b"....]
```

Iterating through the list. When we reach the "end" a default value "X".
```java
seq(list).after("X").list(100).consume(System.out::println);
// Prints ["a", "b", "c", "d", "X", "X"...."X"]
```
## `sqlInserts()`

This method is used to generate individual (or group of SQL Inserts).

For example:

```java
SQLInsert oneInsert = sqlInserts()
                          .tableName("emp")
                          .column("id", intSeq().increment(10))
                          .column("first_name", names().first(), TEXT_BACKSLASH)
                          .column("last_name", names().last(), TEXT_BACKSLASH)
                          .column("username", users(), TEXT_BACKSLASH)
                          .column("email", emails(), TEXT_BACKSLASH)
                          .column("description", markovs().size(32).type(LOREM_IPSUM), TEXT_BACKSLASH)
                          .column("created", localDates().thisYear().display(BASIC_ISO_DATE), TEXT_BACKSLASH)
                          .get();

System.out.println(oneInsert);
// Possible Output:
// INSERT INTO emp (id, first_name, last_name, username, email, description, created) VALUES (0, 'Mohammad', 'Hibbets', 'lushtraci', 'villousslags@gmx.com', 'Ante ipsum primis in faucibus. E', '20180724');
```

The `TEXT_BACKSLASH` parameter is used to denote the fact the column is a text column, and the text is escaped.

For convenience, we can generate multiple SQL inserts, that can be stored in a `SQLTable` object.

```java
int empTableRows = 100;
SQLTable empTable = sqlInserts()
                          .tableName("emp")
                          .column("id", intSeq().increment(10))
                          .column("first_name", names().first(), TEXT_BACKSLASH)
                          .column("last_name", names().last(), TEXT_BACKSLASH)
                          .column("username", users(), TEXT_BACKSLASH)
                          .column("email", emails(), TEXT_BACKSLASH)
                          .column("description", markovs().size(32).type(LOREM_IPSUM), TEXT_BACKSLASH)
                          .column("created", localDates().thisYear().display(BASIC_ISO_DATE), TEXT_BACKSLASH)
                          .table(empTableRows) // Generate a table instead of a single Insert
                          .get();

// After the table is generated we can modify the data in it

empTable.updateAll((i, insert) -> {
            // Update all the descriptions with 'N/A'
            insert.setValue("description", "N/A");
});

System.out.println(empTable);

// Select only the inserts from the table where the first_name starts with and print them

empTable
        .selectWhere((sqlInsert -> sqlInsert.getValue("first_name").startsWith("A")))
        .forEach(System.out::println);
```

## `shufflers()`

The `Shufflers` class contain useful methods to create new shuffled strings, arrays or list from a source:

| Method | Description |
|:------ |:----------- |
| `T array(T[] source)` | Generic method to shuffle an array of Objects. |
| `double[] arrayDouble(double[] source)` | Method to shuffle an array of double primitive values. |
| `float[] arrayFloat(float[] source)` | Method to shuffle an array of float primitive values. |
| `int[] arrayInt(int[] source)` | Method to shuffle an array of int primitive values. |
| `long[] arrayLong(long[] source)` | Method to shuffle an array of long primitive values. |
| `String string(String source)` | Method to shuffle a String. |


Example for generating 5 random permutation of a given `int[]` array:

```java
int[] x = { 1, 2, 3, 4, 5, 6 };

shufflers()
        .arrayInt(x)
        .stream().get()
        .limit(5)
        .forEach(arr -> System.out.println(Arrays.toString(arr)));
```

## `sscs()`

This method is used to generate random Social Security Numbers.

Example:
```java
String ssc = sscs().get();
// Possible Output: 776-32-8981
```

## `space()`

This method is used to generate arbitrary "Space Object" names like: Planet Names, Star Names, etc.

```java
space().constellations().consume(System.out::println);
space().galaxies().consume(System.out::println);
space().moons().consume(System.out::println);
space().planets().consume(System.out::println);
space().stars().consume(System.out::println);

// Output:
/**
Libra
Milky Way
Triffid Nebula
Saturn
Proxima Centauri
*/
```

## `strings()`

This method is used to generate random String(s).

Additionally the size of the resulting `String` can be controlled using `size()`.

Also the types of `String` objects we can use are mapped in the `StringType` enum:
- `NUMBERS`;
- `ALPHA_NUMBERIC`;
- `LETTERS`;
- `HEX`;
- `SPECIAL_CHARACTERS`;

Example to generate a random string with a fixed-size of 15:

```java
String str1 = strings().size(15).get();
// Possible Output: 6GFLyfFRXjgC6wZ
```

Example to generate a random string with a variable-size between [1, 10]:
```java
String str2 = strings().size(ints().range(1,10)).get();
```

As you can see in the example above it's possible to generate random strings with random size. The only consideration is that the constructed `MockUnit` passed as the parameter for size should not generate negative numbers, otherwise the call will fail.

Example to generate a `String` that only contains letters (no numbers or special characters) and it has a size of 5:

```java
String onlyLetters = strings().size(5).type(LETTERS).get();
// Possible Output: "P7S9ojfEyJ47ohy"
```

## `urls()`

This method can be used to generate random URLs.

```java
urls()
  .scheme(HTTP) // all the URLS have a HTTP scheme
//.auth() -- can include auth information in the URL
  .domain(POPULAR) // only popular domain names can be used
  .host(ADVERB_VERB)
  .ports(80, 8080, 8090) // only the following ports can be used
  .list(10)
  .consume(System.out::println);
```

Possible output for the above example:
```
[http://www.tenthlyassays.net:8090, http://www.aflamecurr.io:8080, http://www.thirdlygirth.org:8080, http://www.foreprobates.net:8090, http://www.pokilyrile.org:80, http://www.cheerfullyapprizings.net:8090, http://www.whistlinglyunsettles.info:80, http://www.gratistrichinized.io:8080, http://www.sternwardssnuffle.gov:8090, http://www.yesterdaynix.edu:8090]
```

## `usStates()`

This method is used to generate US States.

```java
String usState = usStates().get();
String usStateISO2 = usStates().iso2().get();
```

## `words()`

This method is used to generate random words.

It comes in more different flavors:

- `words()` : will return arbitrary words from the English vocabulary;
- `words().adverbs()` : will return arbitrary adverbs from the English vocabulary;
- `words().adverbs1syll()` : will return arbitrary adverbs from the English vocabulary (1 syllable);
- `words().adverbs2syll()` : will return arbitrary adverbs from the English vocabulary (2 syllable);
- `words().adverbs3syll()` : will return arbitrary adverbs from the English vocabulary (3 syllable);
- `words().adverbs4syll()` : will return arbitrary adverbs from the English vocabulary (4 syllable);
- `words().adjectives()` : will return arbitrary adjectives from the English vocabulary;
- `words().adjectives1syll()` : will return arbitrary adjectives from the English vocabulary (1 syllable);
- `words().adjectives2syll()` : will return arbitrary adjectives from the English vocabulary (2 syllable);
- `words().adjectives3syll()` : will return arbitrary adjectives from the English vocabulary (3 syllable);
- `words().adjectives4syll()` : will return arbitrary adjectives from the English vocabulary (4 syllable);
- `words().nouns()` : will return arbitrary nouns from the English vocabulary;
- `words().nouns1syll()` : will return arbitrary nouns from the English vocabulary (1 syllable);
- `words().nouns2syll()` : will return arbitrary nouns from the English vocabulary (2 syllable);
- `words().nouns3syll()` : will return arbitrary nouns from the English vocabulary (3 syllable);
- `words().nouns4syll()` : will return arbitrary nouns from the English vocabulary (4 syllable);
- `words().verbs()` : will return arbitrary verbs from the English vocabulary;
- `words().verbs1syll()` : will return arbitrary verbs from the English vocabulary (1 syllable);
- `words().verbs2syll()` : will return arbitrary verbs from the English vocabulary (2 syllable);
- `words().verbs3syll()` : will return arbitrary verbs from the English vocabulary (3 syllable);
- `words().verbs4syll()` : will return arbitrary verbs from the English vocabulary (4 syllable).

Example:

```java
String word = words().get();
```

Example for generating a adjective/noun combination:

```java
fmt("#{adj} #{noun}")
  .param("adj", words().adjectives().format(CAPITALIZED))
  .param("noun", words().nouns().format(LOWER_CASE))
  .list(10)
  .consume(list -> System.out.println(list));

// Possible Output:
// [Pyelitic foehn, Slung dragonheads, Triste encumbrancer, Malicious culls, Tridactyl liege, Amicable wisteria, Stringed linns, Palladous idleness, Retractable cocksfoots, Unallayed bots]
```

<br/>
<br/>
<br/>

# MockUnit<T>

## `array()`

This method is used to generate a generic array `T[]` from a `MockUnit<T>`.

Example generating a `String[]` with size 100, where each `String` value is a first name:

```java
String[] names = names()
                    .first()
                    .array(String.class, 100)
                    .val();
```

Example for generating a matrix of integers with 5 rows and 5 cols:

```java
int[][] a = ints()
             .arrayPrimitive(5)
             .array(int[].class, 10)
             .val();
```

Example for generating an array of 10 integers using a `Supplier<T[]` instead of using reflection:

```java
Integer[] ints = ints().array(() -> new Integer[10]).val();
```

## `collection()`

This method is used to generate a `MockUnit<Collection<T>>` from a `MockUnit<T>`.

If reflection is used as the collection needs to have a public NO_ARGS constructor.

Example for generating a `Vector<Boolean>` with size 100, where each `Boolean` value has a 35.5% probability of being true:

```java
Collection<Boolean> vector = bools()
                                 .probability(35.50)
                                 .collection(Vector.class, 100)
                                 .val();
```

Example for generating a `Collection<Boolean>` (the implementation will be a `Stack<Boolean>`) with 10 `Boolean` values:

```java
Collection<Boolean> c = bools().collection(() -> new Stack<>(), 10).val();
```

Example for generating a `Collection<String>` with a variable length in the range: [0, 10).

```java
MockUnitInt sizeGenerator = ints().range(0, 10);
Collection<String> cStr = strings().collection(() -> new ArrayList<>(), sizeGenerator).val();
```

## `consume()`

This method is used to "consume" the values that are generated from a `MockUnit<T>`.

The input parameter of `consume()` is a `Consumer<T>`.

Example for printing to `stdout` a `List<String>` where each entry is an URL:

```java
urls().list(100).consume(System.out::println);
// Possible Output:
// [http://www.frenziedchi.io, http://www.eyelinersbarry.gov, ..., http://www.sphygmicmillard.com]
```

There is another option of using the `consume()` method more than once on the same input:

Example for (out)printing an "index - name" association 10 times:

```java
names().first().consume(10, (idx, name) -> {
    System.out.printf("%d - %s\n", idx, name);
});
```

Possible Output:

```
0 - Erica
1 - Frank
2 - Ardell
3 - Sofia
4 - Daryl
5 - Precious
6 - Pinkie
7 - Criselda
8 - Mercy
9 - Bettie
```

## `get()`

This method is used to return a single `<T>` value from the `MockUnit<T>`.

Example for generating a single boolean value that has a 35.5% probability of being true:

```java
MockUnit<Boolean> boolUnit = bools().probability(35.50);
Boolean val = boolUnit.get();
```

We can write all of the above simpler without keeping the reference:

```java
Boolean val = bools().probability(35.50).get();
```

*This is a Scala&Kotlin friendly alias for the [`val()`](#val) method.*

## `list()`

This method is used to create a `MockUnit<List<T>>` from a previous `MockUnit<T>`.

By default the implementation used is `ArrayList<T>`.

Example for generating a `List<String>` where every `String` is a country name. The list has 100 elements:

```java
List<String> countries = countries().names().list(100).val();
// Possible Output: [Guam, Lao People's Democratic Republic, French Polynesia, Algeria, Somalia, Iraq, ...]
```

It is also possible to specify a different `List<T>` implementation. For example, in the previous example we can use a `LinkedList<String>` instead of the default `ArrayList<String>`:

```java
List<String> countries = countries().names().list(LinkedList.class, 100).val();
// Possible Output: [Guam, Lao People's Democratic Republic, French Polynesia, Algeria, Somalia, Iraq, ...]
```

Important: For all the methods that accept a `Class<List<T>>` as an input parameter, the `List<T>` implementation needs to have implemented a No-Args constructor.

There is a another method to collect the results in a list without using reflection. In this case the developer will need to define a `Supplier<List<T>>` method and pass it to the list function:

```java
List<String> emails = emails().list(() -> new ArrayList<>(), 10).val();
```

Instead of having a fixed-length we can define a "lengthGenerator" that can generate variable lengths, that can be passed to the `list()` method:

```java
MockUnitInt lengthGenerator = ints().range(1, 100);
List<String> creditCards = creditCards().list(() -> new LinkedList<>(), lengthGenerator).val();
```

## `map()`

This method allows us to do pre-processing on the values before they are actually generated.

It is also possible to help translate a `MockUnit<T>` to a `MockUnit<R>` (of a different type).

The input parameter for the method is a `Function<T, R>`. `T` can be the same `R`.

Example for translating a `MockUnit<Boolean>` into a `MockUnit<String>` that holds the `String` values `"true"` and `"false"`:

```java
List<String> list = bools().map((b) -> b.toString()).list(10).val();
```

## `mapKeys()`

The method help us generating a `MockUnit<Map<R, T>` from a `MockUnit<T>`.

The keys can be obtained from:
- Another `MockUnit<R>`;
- From a `Supplier<R>`;
- From an `Iterable<R>`;
- From an generic array `R[]`;
- From primitives arrays: `int[]`, `double[]`, `long[]`;

Example for obtaining a `Map<Integer, Boolean>` from an array of `int[]`:

```java
int[] keys = { 100, 200, 300, 400, 500, 600 };
Map<Integer, Boolean> map = bools().mapKeys(keys).val();
// Possible Output: {400=true, 100=false, 500=false, 200=true, 600=true, 300=true}
```

Example for obtaining a `Map<Double, Boolean>` (`LinkedHashMap`) from an `Iterable<Double>`:

```java
Iterable<Double> iterable = unmodifiableList(asList(0.1, 0.2, 0.3, 0.4, 0.5));
Map<Double, Boolean> map = bools().mapKeys(LinkedHashMap.class, iterable).val();
// Possible Output: {0.1=true, 0.2=true, 0.3=false, 0.4=false, 0.5=true}
```

In order to avoid the instantiation of the `Map<T, R>` using reflection, a `Supplier<Map<T,R>>` method can be passed to the `mapKeys()` method.

Example for generating a `Map<Integer, String>` where keys are numbers in a sequence in the values are names:

```java
MockUnitInt keysGenerator = intSeq();
Map<Integer, String> namesMap = names()
                                 .mapKeys(() -> new LinkedHashMap<>(), keysGenerator.list(10).val())
                                 .val();
System.out.println(namesMap);

// Possible Output:
// {0=Afton Ragel, 1=Ofelia Labarba, 2=Brian Pezzuti, 3=Hector Starbuck, 4=Kiera Plew, 5=Meta Walling, 6=Trula Steakley, 7=Geoffrey Engelmeyer, 8=Robert Bodnar, 9=Reda Mizzelle}
```

## `mapToDouble()`

This method is used to transform a `MockUnit<T>` into a more specific `MockUnitDouble`.

The input parameter is a `Function<T, Double>`.

Example for translating a `MockUnit<Boolean>` into a `MockUnitDouble`. If the value it's true, it should return a value between [1.0, 2.0), else it should return a value between [2.0, 3.0):

```java
MockUnitDouble md = bools()
                     .mapToDouble((bool) -> {
                         if (bool)
                             return doubles().range(1.0, 2.0).val();
                         return doubles().range(2.0, 3.0).val();
                     });

Double d = md.val();
```

## `mapToInt()`

This method is used to translate a `MockUnit<T>` into a more specific `MockUnitInt`.

The input parameter for the method is a `Function<T, Integer>`.

Example for translating a `MockUnit<Boolean>` into a `MockUnitInt` by transforming `true` into `1` and `false` into `0`:

```java
MockUnitInt zeroOrOne = bools()
                         .mapToInt((b) -> (b) ? 1 : 0);

List<Integer> list = zeroOrOne.list(5).val();
// Possible Output: [0, 0, 0, 1, 0]
```

## `mapToLocalDate()`

This method is used to transform a `MockUnit<T>` into a more specific `MockUnitLocalDate` through a `Function<T, LocalDate>`.

Example:

```java
// Generates local dates only in 1987 or in 1989
ints().from(new int[]{ 1987, 1989})
      .mapToLocalDate(year -> {

                  // Obtain a random month [1, 12]
                  int month = ints()
                                .range(1, 13)
                                .get();

                  // Check how many days the month has for that year
                  int numDays = YearMonth.of(year, month).lengthOfMonth();

                  // Generate a random day
                  int day = ints()
                                .range(1, numDays + 1)
                                .get();

                  return java.time.LocalDate.of(year, month, day);
              })
      .display(BASIC_ISO_DATE)
      .accumulate(100, "\n")
      .consume(System.out::println);
```

## `mapToLong()`

This method is used to translate a `MockUnit<T>` into a more specific `MockUnitLong`.

The input parameter of the method is a `Function<T, Long>`.

Example for translating a `MockUnit<List<Long>>` into a `MockUnitLong` by calculating the sum of the numbers in the `List<Long>`:

```java
MockUnit<List<Long>> muList = longs()
                                .range(0l, 100l)
                                .list(10);

Long sum = muList.mapToLong(list -> list.stream()
                                        .mapToLong(Long::longValue)
                                        .sum())
                 .val();
```

## `mapToString()`

This method is used to translate a `MockUnit<T>` into a more specific `MockUnitString`.

If we use `mapToString()` without any parameter then `toString()` gets called directly on the generated object.

Otherwise we can use `mapToString(Function<T, String>)` and perform additional processing in the function.

Example for generating integer values as Strings:

```java
MockUnitString mus = ints().mapToString();
String integer = mus.val();
```

Example for generating a `MockUnitString` from a `MockUnitInt` that returns "odd" or "even" depending on the numbers generated:

```java
MockUnitString oddOrEven = ints().mapToString(i -> i%2==0 ? "even" : "odd");
List<String> oddOrEvenList = oddOrEven.list(10).val();
// Possible Output: [odd, even, even, odd, odd, even, even, odd, odd, odd]
```

## `mapVals()`

This method allows us to translate a `MockUnit<T>` into a `MockUnit<Map<T, R>>`.

The values can be obtained from:
- Another `MockUnit`;
- From a `Supplier<K>`;
- From an `Iterable<K>`;
- From an generic array `K[]`;
- From primitives arrays: `int[]`, `double[]`, `long[]`;

Example for generating a `Map<Boolean, Integer>` from an array of `int[]`:

```java
int[] values = { 100, 200, 300, 400, 500, 600 };
Map<Boolean, Integer> map = bools().mapKeys(keys).val();
// Possible Output: {false=500, true=600}
```

Because the `Map` cannot have duplicate keys, the result will always contain 2 keys (true, false).

Example for generating a `Map<Boolean, Long>` from `Supplier<Long>`

```java
Supplier<Long> longSupplier = () -> System.currentTimeMillis();
Map<Boolean, Long> map = bools().mapVals(10, longSupplier).val();
// Possible Output: {false=1487951761873, true=1487951761873}
```

## `val()`

This method is used to return a single `<T>` value from the `MockUnit<T>`.

Example for generating a single boolean value that has a 35.5% probability of being true:

```java
MockUnit<Boolean> boolUnit = bools().probability(35.50);
Boolean val = boolUnit.val();
```

We can write all of the above simpler without keeping the reference:

```java
Boolean val = bools().probability(35.50).val();
```

## `valStr()`

This method works exactly like `val()` but instead of the value, it first calls `toString()` on the object.

If the generated object is `null` it returns empty String.

```java
String alwaysTrue = bools().probability(100.0).valStr();
// Possible Output: "true"
```

If the generated object is `null`, an alternative `String` value can be specified:

```java
String nullll = from(new String[]{ null, null, null})
                 .valStr("NULLLL");
// Output: "NULLLL"
````

## `set()`

This method is used to obtain a `MockUnit<Set<T>>` from a `MockUnit<T>`.

Example for generating a `Set<Integer>`:

```java
Set<Integer> setInt = ints().set(100).val();
```

*Note:* 100 in our case doesn't necessarily represents the size of the `Set`. If the random numbers are not unique the `Set` may have less than 100 elements.

## `serialize()`

This method doesn't return anything. It's a closing method.

Example for storing a `Boolean` value in a file:
```java
bools().serialize(file1.obj);
```

## `stream()`

This method is used to obtain a `MockUnit<Stream<T>>` from a `MockUnit<T>`.

Example for generating an infinite `Stream<Boolean>`:

```java
Stream<Boolean> stream = bools().stream().val();
```

# MockUnitDays

The `MockUnitDays` interface extends `MockUnit<DayOfWeek>`.

```java
public interface MockUnitDays extends MockUnit<DayOfWeek> {}
```

This means that it "inherits" all the methods from [`MockUnit<DayOfWeek>`](MockUnit)

The easiest way to obtain a `MockUnitDays` is to call the [`days()`](MockNeat#days) method from `MockNeat<T>`.

## `display()`

This method generates a `MockUnitString` from a `MockUnitDays` by transforming the days of the week into localised strings.

Examples of generating a String that represents a random day of the week-end in French:

```java
String day = days()
              .after(FRIDAY)
              .display(TextStyle.FULL_STANDALONE, Locale.FRANCE)
              .val();
// Possible Output: "dimanche"
```

The method signatures are:

```java
default MockUnitString display(TextStyle textStyle, Locale locale)
```

When we want to use the default locale:
```java
default MockUnitString display(TextStyle textStyle)
```

When we want to use the default locale and the `textStyle` as `FULL_STANDALONE`:
```java
default MockUnitString display()
```

# MockUnitDouble

The `MockUnitDouble` interface extends `MockUnit<Double>`.

```java
public interface `MockUnitDouble` extends MockUnit<Double>
```

This means that it "inherits" all the methods from [`MockUnit<Double>`](MockUnit)

The easiest way to obtain a `MockUnitDouble` is to call the [`doubles()`](MockNeat#doubles) method from `MockNeat` or to call the [`mapToDouble()`](MockUnit#maptodouble) method.

## `array()`

The method is used to generate a `MockUnit<Double[]>` from a `MockUnitDouble`.

Compared to the [`array()`](MockUnit#array) method from `MockUnit<T>` there's no reason to specify the type of the array. We know it's `Double[]`.

Example for creating an array of 100 random Doubles, with values between [1000.0, 2000.0):

```java
Double[] array = doubles()
                      .range(1000.0, 2000.0)
                      .array(100)
                      .val();
````

## `arrayPrimitive()`

This method is used to generate a `MockUnit<double[]>` from a `MockUnitDouble`.

Example for creating a primitive array of 100 random doubles, with values between [1000.0, 2000.0):

```java
double[] array = doubles()
                     .range(1000, 200)
                     .arrayPrimitive(100)
                     .val();
```

## `doubleStream()`

Can be used to obtain a more specific `DoubleStream` instead of a `Stream<Double>`, which normally can be obtain with the [`stream()`](MockUnit#stream) from `MockUnit<Double>`.

# MockUnitInt

The `MockUnitInt` interface extends `MockUnit<Integer>`.

```java
public interface `MockUnitInt` extends MockUnit<Integer>
```

This means that it "inherits" all the methods from [`MockUnit<Integer>`](MockUnit)

The easiest way to obtain a `MockUnitInt` is to call the [`ints()`](MockNeat#ints) method from `MockNeat` or to call the [`mapToInt()`](MockUnit#maptoint) method.

## `array()`

The method is used to generate a `MockUnit<Integer[]>` from a `MockUnitInt`.

Compared to the [`array()`](MockUnit#array) method from `MockUnit<T>` there's no reason to specify the type of the array. We know it's `Integer[]`.

Example for creating an array of 100 random Integers, with values between [1000, 2000):

```java
Integer[] array = ints()
                      .range(1000, 2000)
                      .array(100)
                      .val();
````

### `arrayPrimitive()`

This method is used to generate a `MockUnit<int[]>` from a `MockUnitInt`.

Example for creating a primitive array of 100 random integers, with values between [1000, 2000):

```java
int[] array = ints()
                  .range(1000, 200)
                  .arrayPrimitive(100)
                  .val();
```

### `intStream()`

Can be used to obtain a more specific `IntStream` instead of a `Stream<Integer>`, which normally can be obtain with the [`stream()`](MockUnit#stream) from `MockUnit<Integer>`.

# MockUnitLocalDate

The `MockUnitLocalDate` interface extends `MockUnit<LocalDate>`.

```java
public interface `MockUnitLocalDate` extends MockUnit<LocaleDate>
```

This means that it "inherits" all the methods from [`MockUnit<LocaleDate>`](MockUnit)

The easiest way to obtain a `MockUnitLocalDate` is to call the [`localDates()`](MockNeat#localdates) method from `MockNeat`.

### `toUtilDate()`

Translates the existing `MockUnitLocalDate` into a `MockUnit<java.util.Date>`.

Example:
```java
Date date = localDates()
                  .between(
                     of(2000, 10, 10),
                     of(2020, 10, 10)
                  )
                  .toUtilDate()
                  .val();
```

# MockUnitLong

The `MockUnitLong` interface extends `MockUnit<Long>`.

```java
public interface MockUnitLong extends MockUnit<Long> {}
```

This means that it "inherits" all the methods from [`MockUnit<Long>`](MockUnit)

The easiest way to obtain a `MockUnitInt` is to call the [`longs()`](MockNeat#longs) method from `MockNeat` or to call the [`mapToLong()`](MockUnit#maptolong) method.

## `array()`

The method is used to generate a `MockUnit<Long[]>` from a `MockUnitLong`.

Compared to the [`array()`](MockUnit#array) method from `MockUnit<T>` there's no reason to specify the type of the array. We know it's `Long[]`.

Example for creating an array of 100 random Longs, with values between [1000, 2000):

```java
Long[] array = longs()
                   .range(1000l, 2000l)
                   .array(100)
                   .val();
````

## `arrayPrimitive()`

This method is used to generate a `MockUnit<long[]>` from a `MockUnitLong`.

Example for creating a primitive array of 100 random longs, with values between [1000l, 2000l):

```java
long[] array = longs()
                   .range(1000, 200)
                   .arrayPrimitive(100)
                   .val();
```

## `longStream()`

Can be used to obtain a more specific `LongStream` instead of a `Stream<Long>`, which normally can be obtain with the [`stream()`](MockUnit#stream) from `MockUnit<Long>`.

# MockUnitMonth

The `MockUnitMonth` interface extends `MockUnit<Month>`.

```java
public interface MockUnitMonth extends MockUnit<Month> {}
```

That means that it inherits all the methods from `MockUnit<Month>`.

The easiest way to obtain a MockUnitDays is to call the [`months()`](MockNeat#months) method from `MockNeat`.

### `display()`

Example:

```java
String month = months()
                    .before(JULY)
                    .display(TextStyle.FULL, Locale.CANADA)
                    .val();

//Possible Ouput: May
```

# MockUnitString

The `MockUnitString` extends the `MockUnit<String>` interface.

```java
@FunctionalInterface
public interface MockUnitString extends MockUnit<String> {}
```

This means that it "interits" all the methods from MockUnit<String>, but it also adds new functionalities related to String operations.

The easiest way to obtain a `MockUnitString` implementation is by calling [`mapToString()`](MockUnit#maptostring) on any `MockUnit<T>`. A lot of the methods are already returning `MockUnitString` by default.

### `accumulate()`

This methods concatenates multiple results into one big `String`.

Example for printing 10 arbitrary (small) prime numbers by concatenating with a `,` character:

```java
primes()
  .mapToString()
  .accumulate(10, ",")
  .consume(System.out::println);
```

Example (generating HTML code):
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

### `append()`

Example:

Example:
```java
// APPEND
String[] cityAppend = cities()
                            .capitals()
                            .append("-001") // To each generated String we append "-001"
                            .array(10)
                            .val();

// Possible Output: [Bishkek-001, Ulaanbaatar-001, Kigali-001, Bratislava-001, Sarajevo-001, Kabul-001, Lusaka-001, Port Vila-001, Tegucigalpa-001, Astana-001]
```
### `array()`

Example:
```java
// ARRAY
String[] someDays = days()
                        .display()
                        .array(10)
                        .val();

// Possible Output: [Saturday, Monday, Monday, Sunday, Sunday, Tuesday, Thursday, Friday, Monday, Wednesday]
```

### `base64()`

Example:
```java
// Generate a list of names
List<String> names = names()
                     .first()
                     .format(CAPITALIZED)
                     .list(5)
                     .val();
// Possible Output: [Carroll, Zane, Alfred, Brent, Loren]

// Using seq() we iterate through the previous list and we encode the strings
List<String> base64names = seq(names)
                            .mapToString()
                            .base64()
                            .list(5)
                            .val();
// Possible Output: [Q2Fycm9sbA==, WmFuZQ==, QWxmcmVk, QnJlbnQ=, TG9yZW4=]
```

### `escapeCsv()`

Example
```java
String[] notFriendlyCsv = { "\"", /* OTHERS */};

String friendlyCSV = from(notFriendlyCsv)
                         .mapToString()
                         .escapeCsv()
                         .val();
```

### `md2()`

Generates the `md2` hash for an arbitrary `String`.

```java
String str = strings().md2().get();
```

### `md5()`

Generates the `md5` hash for an arbitrary `String`:

```java
String str = strings().md5().get();
```

### `sha1()`

Generates the `sha1` hash for an arbitrary `String`:

```java
String str = strings().sha1().get();
```

### `sha256()`

Generates the `sha256` hash for an arbitrary `String`:

```java
String str = strings().sha256().get();
```

### `sha384()`

Generates the `sha384` hash for an arbitrary `String`:

```java
String str = strings().sha384().get();
```

### `sha512()`

Generates the `sha512` hash for an arbitrary `String`:

```java
String str = strings().sha512().get();
```
