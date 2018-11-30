---
layout: page
title: home
permalink: /
---

[**mockneat**](https://github.com/nomemory/mockneat) [![Build Status](https://travis-ci.org/nomemory/mockneat.svg?branch=master)](https://travis-ci.org/nomemory/mockneat.svg?branch=master) [![codecov](https://codecov.io/gh/nomemory/mockneat/branch/master/graph/badge.svg)](https://codecov.io/gh/nomemory/mockneat)
 is an arbitrary data-generator <sup>open-source library</sup> written in Java.

It provides a simple but powerful (fluent) API that enable developers to create [json](#json), xml, [csv](#csv) and [sql](#sql) data programatically.

It can also act as [a powerful `Random` substitute](#random) or a mocking library.

#### **json**

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


#### **csv**

```java
String csvContent = csvs()
                      .column(intSeq().start(0).increment(10))
                      .column(names().full().escapeCsv())
                      .column(localDates().thisYear().display(BASIC_ISO_DATE))
                    .accumulate(100, "\n")
                    .get();

System.out.println(csvContent);

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

#### sql

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

#### random

```java
int x = ints().range(0, 10).get();

char letter = chars().letters().get();

boolean almostTrue =
          probabilities(Boolean.class)
          .add(0.99, true)
          .add(0.01, false)
          .get();
```                        
