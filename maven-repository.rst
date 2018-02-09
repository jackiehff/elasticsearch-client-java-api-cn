########################################
Maven仓库
########################################

Elasticsearch 托管在 ` Maven中央仓库 <http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22elasticsearch%22>`_ 中。

例如, 你可以在 `pom.xml` 文件中声明最新版本的依赖:

.. code-block:: xml

    <dependency>
        <groupId>org.elasticsearch.client</groupId>
        <artifactId>transport</artifactId>
        <version>6.2.1</version>
    </dependency>


****************************************
Lucene快照仓库
****************************************

任何主版本的第一个发布版本(比如beta)都可能构建于一个 Lucene 快照版本之上。在这种情况下，你无法解析客户端的 Lucene 依赖。

比如, 你想使用 6.0.0-beta1 这个版本, 它依赖于 Lucene 7.0.0-snapshot-00142c9 版本, 你就必须定义下面这个仓库。

如果使用 Maven，定义如下:

.. code-block:: xml

    <repository>
        <id>elastic-lucene-snapshots</id>
        <name>Elastic Lucene Snapshots</name>
        <url>http://s3.amazonaws.com/download.elasticsearch.org/lucenesnapshots/00142c9</url>
        <releases><enabled>true</enabled></releases>
        <snapshots><enabled>false</enabled></snapshots>
    </repository>

如果使用 Gradle， 定义如下:

.. code-block:: text

    maven {
        url 'http://s3.amazonaws.com/download.elasticsearch.org/lucenesnapshots/00142c9'
    }


****************************************
Log4j 2 日志记录器
****************************************

你还需要引入 Log4j 2 依赖:

.. code-block:: xml

    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.9.1</version>
    </dependency>

并且还需要在类路径中提供一个 Log4j 2 配置文件。例如, 你可以在 `src/main/resources` 这个工程目录下添加一个 `log4j2.properties` 文件, 内容如下:

.. code-block:: properties

    appender.console.type = Console
    appender.console.name = console
    appender.console.layout.type = PatternLayout

    rootLogger.level = info
    rootLogger.appenderRef.console.ref = console


****************************************
使用其它日志记录器
****************************************

如果你想要使用 Log4j 2 之外的日志记录器, 可以使用 ` SLF4J <http://www.slf4j.org/>`_ 桥接器来实现:

.. code-block:: xml

    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-to-slf4j</artifactId>
        <version>2.9.1</version>
    </dependency>

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>1.7.24</version>
    </dependency>

` SLF4J用户手册 <http://www.slf4j.org/manual.html>`_ 这篇文章中列举了所有可使用的日志实现。选择你最喜欢的日志记录器并将其作为 Maven 依赖添加进来。举个例子, 我们将使用 `slf4j-simple` 这个日志记录器:

.. code-block:: xml

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.21</version>
    </dependency>
