---
layout: single
classes: wide
title: mockneat home
permalink: /
author_profile: true
---

[**mockneat**](https://github.com/nomemory/mockneat) [![Build Status](https://travis-ci.org/nomemory/mockneat.svg?branch=master)](https://travis-ci.org/nomemory/mockneat.svg?branch=master) [![codecov](https://codecov.io/gh/nomemory/mockneat/branch/master/graph/badge.svg)](https://codecov.io/gh/nomemory/mockneat)
 is an arbitrary data-generator <sup>open-source library</sup> written in Java.

It provides a simple but powerful (fluent) API that enables developers to create json, xml, csv and sql data programatically.

It can also act as a powerful `Random` substitute or a mocking library.

The library is still in active development, new features or improvements are added frequently.

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
