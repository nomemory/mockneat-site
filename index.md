---
layout: page
title: home
permalink: /
---

[**mockneat**](https://github.com/nomemory/mockneat) [![Build Status](https://travis-ci.org/nomemory/mockneat.svg?branch=master)](https://travis-ci.org/nomemory/mockneat.svg?branch=master) [![codecov](https://codecov.io/gh/nomemory/mockneat/branch/master/graph/badge.svg)](https://codecov.io/gh/nomemory/mockneat)
 is an arbitrary data-generator <sup>open-source library</sup> written in Java.

It provides a simple but powerful (fluent) API that enable developers to create [json](#json), [xml](#xml), [csv](#csv) and [sql](#sql) data programatically.

It can also act as a powerful `Random` substitute or a mocking library.

#### **json**

By default **mockneat** does not include "native" json capabilities. An external library needs to be included. For simplicity we've picked [gson](https://github.com/google/gson), but others can be used.

[POJO](https://en.wikipedia.org/wiki/Plain_old_Java_object) class(es) should be defined (if not already) to reflect the structure of the resulting json.

```java
@Data
@NoArgsConstructor
private static class User {
    String firstName;
    String lastName;
    String email;
    String creditCard;
}
```

```java
Gson gson = new GsonBuilder().setPrettyPrinting().create();

filler(() -> new User())
    .setter(User::setFirstName, names().first())
    .setter(User::setLastName, names().last())
    .setter(User::setEmail, emails())
    .setter(User::setCreditCard, creditCards().type(VISA_16))
  .list(3)
  .map(gson::toJson)
  .consume(System.out::println);

// Output:
/**
[
  {
    "firstName": "Walker",
    "lastName": "Kalawe",
    "email": "chirkodis@verizon.net",
    "creditCard": "4866340567588905"
  },
  {
    "firstName": "Isaac",
    "lastName": "Jirik",
    "email": "schemingmccant@mac.com",
    "creditCard": "4693791204190779"
  },
  {
    "firstName": "Merrill",
    "lastName": "Adams",
    "email": "swepthilda@mail.com",
    "creditCard": "4873002244218519"
  }
]
*/                      
```

Explanation:
* `filler(...)` creates new `User`s by supplying data through setters;
* `names().first()` is used to generate a random first name at each iteration;
* `names().last()` is used to generate random last names at each iteration;
* `creditCard().type(VISA_16)` is used to generate **valid** credit card numbers for the given type (`VISA_16`);
* `list(3)` creates on the spot a list of the 3 `User`s;
* `map(gson::toJson)` transforms the resulting `List<User>` into a valid json `String`;
* `consume(System.out::println)` consumes the resulting json `String` by printing it to `System.out`.

For more a detailed explanation please check the [tutorial](/tutorial) and [docs](/docs) sections.

#### **xml**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@XmlRootElement(name = "product")
public static class Product {
  Long id;
  String name;
  Date date;
}

@Data
@NoArgsConstructor
@AllArgsConstructor
@XmlRootElement(name = "products")
public static class Products {
  List<Products> products;
}
```

```java
final Marshaller jaxbMarshaller = newInstance(Products.class, Product.class)
                                           .createMarshaller();

jaxbMarshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);

Function<Products, String> toXML = (products) -> {
  StringWriter productsWriter = new StringWriter();
  try { jaxbMarshaller.marshal(products, productsWriter); }
  catch (JAXBException e) { e.printStackTrace(); }
  return productsWriter.toString();
};

constructor(Products.class)
  .params(
    constructor(Product.class)
      .params(
        longSeq().start(0).increment(10),
        fmt("#{noun} #{adj}")
          .param("noun", words().nouns().format(CAPITALIZED))
          .param("adj", words().adjectives().format(CAPITALIZED)),
        localDates().thisMonth().toUtilDate()
      )
      .list(3)
  )
  .map(toXML)
  .consume(System.out::println);

// Output:
/**
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<products>
    <products xsi:type="product" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <date>2018-12-07T00:00:00+02:00</date>
        <id>0</id>
        <name>Hurdler Mat</name>
    </products>
    <products xsi:type="product" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <date>2018-12-29T00:00:00+02:00</date>
        <id>10</id>
        <name>Tonnes Histioid</name>
    </products>
    <products xsi:type="product" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <date>2018-12-04T00:00:00+02:00</date>
        <id>20</id>
        <name>Heresy Splurgy</name>
    </products>
</products>
*/
```


#### **csv**

```java
String csvContent = csvs()
                      .column(intSeq().start(0).increment(10))
                      .column(names().full().escapeCsv())
                      .column(localDates().thisYear().display(BASIC_ISO_DATE))
                    .accumulate(100, "\n")
                    .get();

System.out.println(csvContent);

// Output:
/**
0,Calvin Floch,20181224
10,Sherwood Halgrimson,20180711
20,Carolina Fraine,20180420
30,Cruz Hawke,20180905
40,Laure Galizia,20180921
50,Marni Weal,20180323
60,Merlin Barbier,20180327
70,Carma Eshom,20181105
80,Pablo Garlovsky,20180901
90,Nerissa Crittendon,20180514
100,Ricky Vieyra,20180807
...more
*/
```

#### **sql**

```java
sqlInserts()
  .tableName("emp")
    .column("id", intSeq().increment(10))
    .column("first_name", names().first(), TEXT_BACKSLASH)
    .column("last_name", names().last().format(UPPER_CASE), TEXT_BACKSLASH)
    .column("email", emails().domain("company.com"), TEXT_BACKSLASH)
    .column("description", markovs().size(32).type(LOREM_IPSUM), TEXT_BACKSLASH)
    .column("created", localDates().thisYear().display(BASIC_ISO_DATE), TEXT_BACKSLASH)
  .table(10)
  .map(table -> table.updateAll((i, insert) -> insert.setValue("description", "N/A")))
  .consume(System.out::println);

// Output:
/**
INSERT INTO emp (id, first_name, last_name, email, description, created) VALUES (0, 'Donovan', 'GARNESS', 'eightsley@company.com', 'N/A', '20180605');
INSERT INTO emp (id, first_name, last_name, email, description, created) VALUES (10, 'Lucien', 'TRAUNFELD', 'pacedalejandro@company.com', 'N/A', '20180726');
INSERT INTO emp (id, first_name, last_name, email, description, created) VALUES (20, 'Jamaal', 'LOLAGNE', 'supersize@company.com', 'N/A', '20180524');
INSERT INTO emp (id, first_name, last_name, email, description, created) VALUES (30, 'Asa', 'LIKES', 'livedadella@company.com', 'N/A', '20180710');
INSERT INTO emp (id, first_name, last_name, email, description, created) VALUES (40, 'Boyd', 'KWAPNIEWSKI', 'stalkedczyzewski@company.com', 'N/A', '20181207');
INSERT INTO emp (id, first_name, last_name, email, description, created) VALUES (50, 'Mohammad', 'AUNGST', 'coarsemonroe@company.com', 'N/A', '20180911');
INSERT INTO emp (id, first_name, last_name, email, description, created) VALUES (60, 'Collin', 'KIRSCHNER', 'barechiquita@company.com', 'N/A', '20181214');
INSERT INTO emp (id, first_name, last_name, email, description, created) VALUES (70, 'Pedro', 'WHITEHILL', 'birklatonia@company.com', 'N/A', '20180705');
INSERT INTO emp (id, first_name, last_name, email, description, created) VALUES (80, 'Lester', 'LEFEBURE', 'grouseken@company.com', 'N/A', '20180525');
INSERT INTO emp (id, first_name, last_name, email, description, created) VALUES (90, 'Thanh', 'BALDENEGRO', 'merewinburn@company.com', 'N/A', '20180110');
*/    
```                     
