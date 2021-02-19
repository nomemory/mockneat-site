---
layout: single
classes: wide
title: mockneat home
permalink: /
author_profile: true
---

[**mockneat**](https://github.com/nomemory/mockneat) [![Build Status](https://travis-ci.org/nomemory/mockneat.svg?branch=master)](https://travis-ci.org/nomemory/mockneat.svg?branch=master) [![codecov](https://codecov.io/gh/nomemory/mockneat/branch/master/graph/badge.svg)](https://codecov.io/gh/nomemory/mockneat)
 is an arbitrary data-generator <sup>open-source library</sup> written in Java.

It provides a simple but powerful (fluent) API that enables developers to create json, xml, csv and sql data programatically. It can also act as a powerful `Random` substitute or a mocking library.

To install **mockneat** from sources or with gradle/maven please check visit the [start](../start) page.

To find out more about the various features, please check [the tutorial](../tutorial), or the [docs](../docs).

If you want to use **mockneat** to mock REST APIs please check: [serverneat](https://github.com/nomemory/serverneat).

If you want to see **mockneat** in action generating SQL Inserts, please check the following repository: [neat-sample-database-generators](https://github.com/nomemory/neat-sample-databases-generators).

### Quick Example For Generating SQL Inserts

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

System.out.println(departments);
System.out.println(employees);

// Output
/**
INSERT INTO deps (id, name) VALUES (0, 'Inventory');
INSERT INTO deps (id, name) VALUES (1, 'Services');
INSERT INTO deps (id, name) VALUES (2, 'Marketing');
INSERT INTO deps (id, name) VALUES (3, 'Purchasing');
INSERT INTO deps (id, name) VALUES (4, 'Operational');

INSERT INTO emps (id, first_name, last_name, email, manager_id, dep_id) VALUES (0, 'Abel', 'Skibisky', 'abel_skibisky@company.com', 8630, '3');
INSERT INTO emps (id, first_name, last_name, email, manager_id, dep_id) VALUES (10, 'Olen', 'Sourlis', 'olen_sourlis@company.com', 8630, '4');
INSERT INTO emps (id, first_name, last_name, email, manager_id, dep_id) VALUES (20, 'Barry', 'Gustin', 'barry_gustin@company.com', 8630, '0');

...more
*/
```
