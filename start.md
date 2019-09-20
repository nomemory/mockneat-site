---
layout: single
classes: wide
permalink: /start/
title: getting started
sidebar:
  nav: "start"
---

[**mockneat**](https://github.com/nomemory/mockneat) is hosted on [jcenter()](https://bintray.com/nomemory/maven/mockneat) and it's available as a dependency for [maven](#maven), [gradle](#gradle) and ivy.

The library is *free* and *open-source*, so the the latest version can be built directly from the [source code](#sources).

*Note*: There is also an (**outdated**) Maven Central mirror, but I recommend you to use the jcenter() repository if possible, especially for the latest versions and patches.

#### **maven**

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
        <version>0.3.7</version>
    </dependency>
</dependencies>
```

#### **gradle**

```groovy
repositories {
  jcenter()
}
dependencies {
  compile 'net.andreinc.mockneat:mockneat:0.3.7'
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
