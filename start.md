---
layout: page
title: start
permalink: /start/
---

[**mockneat**](https://github.com/nomemory/mockneat) is hosted on **[jcenter()](https://bintray.com/nomemory/maven/mockneat)**. Please check the link for the latest versions. 

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
        <version>0.2.7</version>
    </dependency>
</dependencies>
```

#### **gradle**

```groovy
repositories {
  jcenter()
}
dependencies {
  compile 'net.andreinc.mockneat:mockneat:0.2.7'
}
```

#### **sources**

Clone it from github:

```
git clone https://github.com/nomemory/mockneat.git
```

Build the "fat jar" - the jar that contains MockNeat and all its dependencies:

```
gradle shadowJar
```

Check the `./build/libs/` for `.jar` file: `mockneat-<version>-all.jar`.

Add the `.jar` to the classpath.
