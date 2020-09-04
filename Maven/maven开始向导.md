## 如何创建Maven项目

我们将使用Maven的原型工具创建我们的第一个项目。原型(archetype)可以理解为一个项目模板或脚手架，能够快速的创建一个通用的Maven项目。更多关于原型(archetype)可参考 archetype.md

使用下面的命令行创建项目：

        mvn -B archetype:generate \
            -DarchetypeGroupId=org.apache.maven.archetypes \
            -DgroupId=com.mycompany.app \
            -DartifactId=my-app

执行完上面的命令，你会发现 命名 my-app的文件夹创建了，这个文件夹中包含一个 pom.xml的文件，内容如下：

        <project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                            http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>com.mycompany.app</groupId>
        <artifactId>my-app</artifactId>
        <packaging>jar</packaging>
        <version>1.0-SNAPSHOT</version>
        <name>Maven Quick Start Archetype</name>
        <url>http://maven.apache.org</url>
        <dependencies>
            <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
            </dependency>
        </dependencies>
        </project>

更多关于pom文件的描述，可参考 pom.md

通过原型创建完项目后，可以看到如下的目录结构：

    my-app
        |-- pom.xml
        `-- src
            |-- main
            |   `-- java
            |       `-- com
            |           `-- mycompany
            |               `-- app
            |                   `-- App.java
            `-- test
                `-- java
                    `-- com
                        `-- mycompany
                            `-- app
                                `-- AppTest.java

这是maven项目的标准结构，关于标准结构的描述，参考 标准目录结构.md

## 编译项目
mvn compile

第一次运行这个命令时，maven回去下载所有的插件和依赖，这可能会花费一段时间。第二次运行的时候就很快了。生成的class文件位于 ${basedir}/target/classes

## 编译测试源码并运行单元测试

mvn test

注意：
1. maven在运行test时，需要额外的下载test相关的插件和依赖
2. 在编译和运行test之前，Maven会编译源码（所有的类将会编译一遍)

如果你只是想编译测试代码，可以用下面的命令：

    mvn test-compile

## 打包jar并安装到本地存储库

    mvn package

可以看一下pom文件，你会注意到 packaging元素设置的为jar,这就是为什么maven知道要打包成jar文件，生成的jar文件会在 ${basedir}/target/ 下。

    mvn install

这个命令会将生成的jar安装到本地的存储库中。

## 清除生成

    mvn clean


## SHAPSHOT version 说明

pom.xml 中的 version 标签后面跟了一个 -SNAPSHOT 

        <project xmlns="http://maven.apache.org/POM/4.0.0"
            ...
            <groupId>...</groupId>
            <artifactId>my-app</artifactId>
            ...
            <version>1.0-SNAPSHOT</version>
            <name>Maven Quick Start Archetype</name>
            ...

这个SANPSHOT值，指的是开发分支上的最新代码，并且不保证代码是稳定的或没有做过更改的。相反，发布版本中的代码（任何没有后缀 SNAPSHOT 的版本值）是不变的。换句话说，SNAPSHOT 版本是最终版本之前的开发版本。

