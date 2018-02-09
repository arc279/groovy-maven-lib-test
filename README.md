`gradle` で自前のライブラリをmavenのローカルリポジトリにビルドして `groovy` から呼び出して使う。

`gradlew` は除外してあります。

## フォルダ構成

```
$ tree
.
├── README.md
├── groovy-usage
│   └── a.groovy
├── sample-lib
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── main
│           └── java
│               └── hoge
│                   └── xxxxx
│                       └── MyLibrary.java
└── sample-lib-usage
    ├── build.gradle
    ├── settings.gradle
    └── src
        ├── main
        │   └── java
        │       └── App.java
        └── test
            └── java
                └── AppTest.java
```

## version

```sh
$ gradle -v

------------------------------------------------------------
Gradle 4.1
------------------------------------------------------------

Build time:   2017-08-07 14:38:48 UTC
Revision:     941559e020f6c357ebb08d5c67acdb858a3defc2

Groovy:       2.4.11
Ant:          Apache Ant(TM) version 1.9.6 compiled on June 29 2015
JVM:          1.8.0_131 (Oracle Corporation 25.131-b11)
OS:           Mac OS X 10.12.6 x86_64
```

## ライブラリ側

```sh
$ cd sample-lib
$ gradle uploadArchive

BUILD SUCCESSFUL in 0s
3 actionable tasks: 1 executed, 2 up-to-date
```

### ライブラリビルド結果（ローカルリポジトリ）

```sh
$ tree ~/.m2/repository/hoge
/Users/kuryu/.m2/repository/hoge
└── xxxxx
    ├── 0.0.1
    │   ├── xxxxx-0.0.1.jar
    │   ├── xxxxx-0.0.1.jar.md5
    │   ├── xxxxx-0.0.1.jar.sha1
    │   ├── xxxxx-0.0.1.pom
    │   ├── xxxxx-0.0.1.pom.md5
    │   └── xxxxx-0.0.1.pom.sha1
    ├── maven-metadata-remote.xml
    ├── maven-metadata.xml
    ├── maven-metadata.xml.md5
    ├── maven-metadata.xml.sha1
    └── resolver-status.properties
```

## 使用する側(java application)

```sh
$ cd sample-lib-usage/
$ gradle run

> Task :run
HelloWorld!


BUILD SUCCESSFUL in 0s
2 actionable tasks: 2 executed
```

## 使用する側(groovy)

`grab` すると `grapes` キャッシュに落としてくる仕組みらしい。

```sh
$ cd groovy-usage/
```

### groovy script

```sh
$ cat a.groovy
@Grab(group='hoge', module='xxxxx', version='0.0.1')

import hoge.xxxxx.MyLibrary
println new MyLibrary().testMethod()


$ groovy a.groovy
HelloWorld!
```

### groovysh

```sh
$ groovysh
Groovy Shell (2.4.12, JVM: 1.8.0_131)
Type ':help' or ':h' for help.
-----------------------------------------------------------------------------------------------------------------------
groovy:000> groovy.grape.Grape.grab([group:'hoge', module:'xxxxx', version:'0.0.1'])
===> null
groovy:000> import hoge.xxxxx.MyLibrary
===> hoge.xxxxx.MyLibrary
groovy:000> println new MyLibrary().testMethod()
HelloWorld!
===> null
```

### grapes キャッシュ

```sh
$ tree ~/.groovy/grapes/hoge
/Users/kuryu/.groovy/grapes/hoge/
└── xxxxx
    ├── ivy-0.0.1.xml
    ├── ivy-0.0.1.xml.original
    ├── ivydata-0.0.1.properties
    └── jars
        └── xxxxx-0.0.1.jar
```