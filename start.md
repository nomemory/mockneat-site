---
layout: single
classes: wide
permalink: /start/
title: getting started
sidebar:
  nav: "start"
---

Historically, **mockneat** was hosted using bintray/jcenter. Starting with **May 1st 2021** JFrog decided not to accept new versions and packages for submission. 

From `0.4.4` version onwards, **mockneat** will be hosted on [maven central](https://search.maven.org/). 

For older versions (<= `0.4.2`) you can continue to use the older jcenter() repository, which will be up at least until **February 1st 2022**. This gives us enough time to migrate all the projects to the new maven central repo.

### **maven** (maven central, versions >= `0.4.4`)

```xml
<dependency>
  <groupId>net.andreinc</groupId>
  <artifactId>mockneat</artifactId>
  <version>0.4.5</version>
</dependency>
```

### **gradle** (maven central, versions >= `0.4.4`)

```groovy
implementation 'net.andreinc:mockneat:0.4.5'
```

#### **maven** (jcenter, versions =< `0.4.2.`)

```xml
<repositories>
    <repository>
        <id>jcenter</id>
        <url>https://jcenter.bintray.com/</url>
    </repository>
</repositories>
<dependencies>
    <dependency>
        <groupId>net.andreinc.mockneat</groupId>
        <artifactId>mockneat</artifactId>
        <version>0.4.2</version>
    </dependency>
</dependencies>
```

#### **gradle** (jcenter, versions < `0.4.2.`)

```groovy
repositories {
  jcenter()
}
dependencies {
  compile 'net.andreinc.mockneat:mockneat:0.4.2'
}
```

#### **sources**

Clone it from github.:

```
git clone https://github.com/nomemory/mockneat.git
```

Create the [fat jar](https://stackoverflow.com/questions/19150811/what-is-a-fat-jar). This will contain **mockneat** and all the associated dependencies:

```
gradle shadowJar
```

Check the `./build/libs/` for `.jar` file: `mockneat-<version>-all.jar`.

Add the `mockneat-<version>-all.jar` to the classpath of the project.
