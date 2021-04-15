---
layout: single
classes: wide
title: Examples
permalink: /examples/
author_profile: false
sidebar:
    nav: "examples"
---

This page contains a list of quick examples without any additional explanations. 
To better understand what's happening under the hood, I recommend reading the [tutorial](/tutorial) first.

# json

The following examples make use of the [objectMap()](/docs#objectmap) method.
The alternative way to generate a **JSON** is to create an intermediary POJO, as described in the [tutorial](/tutorial/#json-and-xml). 

```java
Gson gson = new Gson().newBuilder().setPrettyPrinting().create();
objectMap()
    .put("firstName", names().first())
    .put("lastName", names().last())
    .put("address",
        objectMap() // object
            .put("line1", addresses().line1())
            .put("line2", addresses().line2())
    )
    .put("financial",
            objectMap() // object
                .put("creditCard", creditCards().masterCard())
                .put("amount", doubles().range(100.0, 10_000.0))
                .put("currency", currencies().code())
    )
    .put("countries", countries().names().set(10)) // array
    .map(gson::toJson)
    .consume(System.out::println);
```

Output:
```json
{
  "firstName": "Larae",
  "lastName": "Baher",
  "address": {
    "line2": "Suite 137",
    "line1": "852 Likelihood St"
  },
  "financial": {
    "amount": 1030.729243271901,
    "currency": "BND",
    "creditCard": "2720379840607579"
  },
  "countries": [
    "Russian Federation",
    "Guinea",
    "Ireland",
    "Central African Republic",
    "Chile",
    "Paraguay",
    "Wallis And Futuna",
    "Svalbard And Jan Mayen"
  ]
}
```

# sql

This is a simple example for generating SQL inserts. For more advanced use-cases, take a look at this repository: [neat-sample-database-generators](https://github.com/nomemory/neat-sample-databases-generators).

```java
final int numEmployees = 1000;
final int numManagers = 10;
final int numDeps = 5;

// The employees ids: [0, 10, 20, ....]
List<Integer> employeesIds = intSeq().increment(10).list(numEmployees).get();
// Random values from the employees ids list
List<Integer> managerIds = fromInts(employeesIds).list(numManagers).get();

SQLTable departments = sqlInserts()
                        .tableName("deps")
                          .column("id", intSeq())
                          .column("name", departments(), MySQL.TEXT_BACKSLASH)
                        .table(numDeps) // Groups the SQL Inserts into a table
                        .get(); // Retrieves the "table" representation

SQLTable employees = sqlInserts()
                        .tableName("emps")
                          .column("id", seq(employeesIds))
                          .column("first_name", names().first(), MySQL.TEXT_BACKSLASH)
                          .column("last_name", names().last(), MySQL.TEXT_BACKSLASH)
                          .column("email", "NULL", MySQL.TEXT_BACKSLASH)
                          .column("manager_id", fromInts(managerIds))
                          .column("dep_id", departments.fromColumn("id"))
                        .table(numEmployees) // Groups the SQL Inserts inside a table
                        .get()
                        .updateAll((rowNum, row) -> {
                            // Updates each insert so that the email field
                            // is formatted as "(first_name)_(last_name)@company.com"
                            String firstName = row.getValue("first_name").toLowerCase();
                            String lastName = row.getValue("last_name").toLowerCase();
                            row.setValue("email", firstName + "_" + lastName + "@company.com");
                        });
```

Output:

```sql
INSERT INTO deps (id, name) VALUES (0, 'Inventory');
INSERT INTO deps (id, name) VALUES (1, 'Services');
INSERT INTO deps (id, name) VALUES (2, 'Marketing');
INSERT INTO deps (id, name) VALUES (3, 'Purchasing');
INSERT INTO deps (id, name) VALUES (4, 'Operational');

INSERT INTO emps (id, first_name, last_name, email, manager_id, dep_id) VALUES (0, 'Abel', 'Skibisky', 'abel_skibisky@company.com', 8630, '3');
INSERT INTO emps (id, first_name, last_name, email, manager_id, dep_id) VALUES (10, 'Olen', 'Sourlis', 'olen_sourlis@company.com', 8630, '4');
INSERT INTO emps (id, first_name, last_name, email, manager_id, dep_id) VALUES (20, 'Barry', 'Gustin', 'barry_gustin@company.com', 8630, '0');
// ...
```
