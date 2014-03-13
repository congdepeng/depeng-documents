

Java Logging with SLF4J and Logback.md
======================================



#1, Simple Logging Facade for Java (SLF4J)

## 1.1 SLF4J的概述
SLF4J目标是试图使用门面模式来统一Java日记相关的API，在编程的时候，我们只需要和SLF4J打交道。
在运行期，你需要将对应的实现框架添加到classpath，例如log4j或者logback。

    * 如果添加了2个binding怎么办，按照优先级还是报错还是其他？（答案是发出警告，然后选择第一个找到的绑定去干活。）

SLF4J的必要性在哪里呢？ 例如你开发一个框架，比如spring，其中使用了一个日记框架，如果客户想要用你的框架，势必和你的日记框架耦合，
这是非常不爽的事情。


SLF4J的竞争框架是Jakarta Commons Logging， 很显然SLF4J更胜一筹。 因为SLF4J和log4j以及logback一样都是一个作者写的。
但是SLF4J也不是高枕无忧的，因为它的作者又开始写一个新的框架来替代它-----那就是log4j 2.x版本！（干嘛不直接写log4j 2.x？搞出个SLF
4J做什么？）

结论是，log4j 2.x的正式版发布和流行还需要一段时间，因此在几年以内，还是继续所用SLF4J吧。




## 1.2 绑定（Binding）一个具体的干活的logging框架
SLF4J作为一个门面模式的框架， 我们代码里面调用它的API去打印日记，但是其实它根本就不会干活，它就像一个中介，需要具体的干活的日记框架，例如log4j
或者logback来干活。

如果不提供具体框架，将会有如下错误提示：
 
```java
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.

```

**候选的干活的日记框架如下：**
 - slf4j-log4j12-1.7.6.jar （绑定log4j版本1.2）
 - slf4j-jdk14-1.7.6.jar (java.util.logging JDK 1.4 logging)
 - slf4j-nop-1.7.6.jar  (NOP 空转,不打印日记)
 - slf4j-simple-1.7.6.jar （写demo程序的时候用用的简单logging实现）
 - slf4j-jcl-1.7.6.jar （Jakarta Commons Logging 的绑定，将把所有SLF4J接受到的日记代理给JCL）
 - logback-classic-1.0.13.jar[依赖logback-core-1.0.13.jar] **默认最配的实现，不需要适配层，效率最高最好！！！**



**如何绑定干活的日记框架？**
只要把候选的框架加到classpath就自动绑定了。 那具体是怎么做的？

现在假设项目pom.xml依赖信息如下，依赖了slf4j接口和3个干活的实现log4j，simple和logback
```java
    <dependencies>
        <!--Log interface: slf4j-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.5</version>
        </dependency>

        <!--log implements 1: log4j12-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.0</version>
        </dependency>

        <!--log implements 2: simple-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>1.6.4</version>
        </dependency>

        <!--log implements 3: logback-->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-core</artifactId>
            <version>1.0.13</version>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.0.13</version>
        </dependency>
    </dependencies>
```

我们的应用程序中是这样调用SLF4J的API的：
```java
    Logger logger = LoggerFactory.getLogger(HelloLog.class);
    logger.info("hello logging");
```

很明显由LoggerFactory来绑定具体的Logger实现，绑定的方法如下：
 - findPossibleStaticLoggerBinderPathSet() 找出所有的StaticLoggerBinder
 - reportMultipleBindingAmbiguity() 如果有多个绑定打印警告信息
 - 如果没有找到StaticLoggerBinder，将抛出NoClassDefFoundError异常
 - 捕获NoClassDefFoundError异常， 并打印错误信息

这里用了一个技巧，就是slf4j-api.jar中引用了StaticLoggerBinder，但是却不明确定义如何去引入这个类，从而达到了动态绑定。
```java
   private final static void bind() {
    try {
      Set staticLoggerBinderPathSet = findPossibleStaticLoggerBinderPathSet();
      reportMultipleBindingAmbiguity(staticLoggerBinderPathSet);
      // the next line does the binding
      StaticLoggerBinder.getSingleton();
      INITIALIZATION_STATE = SUCCESSFUL_INITIALIZATION;
      reportActualBinding(staticLoggerBinderPathSet);
      emitSubstituteLoggerWarning();
    } catch (NoClassDefFoundError ncde) {
      String msg = ncde.getMessage();
      if (messageContainsOrgSlf4jImplStaticLoggerBinder(msg)) {
        INITIALIZATION_STATE = NOP_FALLBACK_INITIALIZATION;
        Util.report("Failed to load class \"org.slf4j.impl.StaticLoggerBinder\".");
        Util.report("Defaulting to no-operation (NOP) logger implementation");
        Util.report("See " + NO_STATICLOGGERBINDER_URL
                + " for further details.");
      } else {
        failedBinding(ncde);
        throw ncde;
      }
    } catch (java.lang.NoSuchMethodError nsme) {
      String msg = nsme.getMessage();
      if (msg != null && msg.indexOf("org.slf4j.impl.StaticLoggerBinder.getSingleton()") != -1) {
        INITIALIZATION_STATE = FAILED_INITIALIZATION;
        Util.report("slf4j-api 1.6.x (or later) is incompatible with this binding.");
        Util.report("Your binding is version 1.5.5 or earlier.");
        Util.report("Upgrade your binding to version 1.6.x.");
      }
      throw nsme;
    } catch (Exception e) {
      failedBinding(e);
      throw new IllegalStateException("Unexpected initialization failure", e);
    }
  }

```

上面提到的findPossibleStaticLoggerBinderPathSet()方法代码如下， 这段代码获得ClassLoader并查找StaticLoggerBinder类，
SLF4J期望的是只找到一个StaticLoggerBinder类，该类实现了LoggerFactoryBinder接口。
```java
  // We need to use the name of the StaticLoggerBinder class, but we can't reference the class itself.
  private static String STATIC_LOGGER_BINDER_PATH = "org/slf4j/impl/StaticLoggerBinder.class";

  private static Set findPossibleStaticLoggerBinderPathSet() {
    // use Set instead of list in order to deal with  bug #138
    // LinkedHashSet appropriate here because it preserves insertion order during iteration
    Set staticLoggerBinderPathSet = new LinkedHashSet();
    try {
      ClassLoader loggerFactoryClassLoader = LoggerFactory.class
              .getClassLoader();
      Enumeration paths;
      if (loggerFactoryClassLoader == null) {
        paths = ClassLoader.getSystemResources(STATIC_LOGGER_BINDER_PATH);
      } else {
        paths = loggerFactoryClassLoader
                .getResources(STATIC_LOGGER_BINDER_PATH);
      }
      while (paths.hasMoreElements()) {
        URL path = (URL) paths.nextElement();
        staticLoggerBinderPathSet.add(path);
      }
    } catch (IOException ioe) {
      Util.report("Error getting resources from path", ioe);
    }
    return staticLoggerBinderPathSet;
  }
```


**如果添加了多个binding怎么办，如下所示，给出警告，然后选择第一个logback绑定:**
```java
    SLF4J: Class path contains multiple SLF4J bindings.
    SLF4J: Found binding in [jar:file:/home/depeng/.m2/repository/ch/qos/logback/logback-classic/1.0.13/logback-classic-1.0.13.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: Found binding in [jar:file:/home/depeng/.m2/repository/org/slf4j/slf4j-log4j12/1.7.0/slf4j-log4j12-1.7.0.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: Found binding in [jar:file:/home/depeng/.m2/repository/org/slf4j/slf4j-simple/1.6.4/slf4j-simple-1.6.4.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
    SLF4J: Actual binding is of type [ch.qos.logback.classic.util.ContextSelectorStaticBinder]
```


下图演示了真实的绑定层次，application就是指你的应用程序，
![具体绑定演示图](/images/concrete-bindings.png)

图片引用自：[SLF4J用户手册](http://www.slf4j.org/manual.html)


## 1.3 类库日记框架混杂问题如何解决

项目中使用了一个第三方的jar，该jar使用了log4j来打印日记，而我们的新项目使用了logback，如果我们期望第三方jar打印的日记也统一的
由logback来处理，怎么办？

很明显，第三方的jar绑定到log4j上了，它不会主动的去适配我们的项目，只有我们的项目去主动解决这个问题。幸好SLF4J已经为我们解决了这个问题，
SLF4J提供了一系列桥接jar，来将log4j，JCL和java.util.logging的API调用重定向到SLF4J，其效果就跟第三方jar直接调用SLF4J一样。

 - jcl-over-slf4j.jar
> 对于已经用了Jakarta Commons Logging的第三方类库，可以用jcl-over-slf4j.jar来将日记桥接到SLF4J。

 - log4j-over-slf4j.jar
> 对于使用了log4j而不能改动的类库，可以使用log4j-over-slf4j.jar来桥接，注意，此时log4j的配置文件将不起作用，需要转换为logback.xml。
logback网站上有提供转换器：http://logback.qos.ch/translator/。

 - jul-to-slf4j.jar
> 替换jdk logging。需要在程序开始时调用SLF4JBridgeHandler.install()来注册listener。

结论，所以为了防止项目中将要或者已经引用了有历史遗留问题的jar，我们需要将这些桥接的jar包加入依赖，防患于未然。

![具体桥接演示图](/images/legacy.png)





# 2, Logback

Logback作为SLF4J的首选日记框架，有着比log4j更多的优点，值得使用。


## 2.1 Logback Advantage

 - 自动重新载入配置文件，不需要重新启动应用程序服务器
 - 性能提升，更快的执行速度
 - 进过更充分的测试，更稳定
 - 非常自然的实现SLF4J
 - 非常完善的文档
 - 非常优雅的从I/O异常恢复功能
 - 自动压缩归档日志文件
 - 配置清除旧的日志归档文件
 - [谨慎模式](http://logback.qos.ch/manual/appenders.html#prudent)
 - [Lilith](http://lilith.huxhorn.de/)
 - [配置文件中的条件处理](http://logback.qos.ch/manual/configuration.html#conditional)
 - [过滤功能](http://logback.qos.ch/manual/filters.html)
 - [SiftingAppender](http://logback.qos.ch/manual/appenders.html#SiftingAppender)
 - Logback-access模块，提供了通过HTTP访问日志的能力


### 2.1.1性能损耗举例
避免了之前为了减少字符串拼接的性能损耗而不得不写的如下判断：

```java
if(logger.isDebugEnable())
```

现在你可以直接写：
```java
logger.debug(“current user is: {}”, user)
```

拼装消息被推迟到了它能够确定是不是要显示这条消息的时候，但是获取参数的代价并没有幸免。
同时，日志中的参数若超过三个，则需要将参数以数组的形式传入，如：

```java
    Object[] params = {value1, value2, value3};
    logger.debug(“first value: {}, second value: {} and third value: {}.”, params);
```



## 2.2 Logback的使用举例

### 2.2.1 加入Maven依赖

如下所示，加入到pom.xml文件。
```xml
    <dependencies>
        <!--Log interface: slf4j-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.5</version>
        </dependency>

        <!--log implements: logback-->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-core</artifactId>
            <version>1.0.13</version>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.0.13</version>
        </dependency>

        <!--log slf4j bridges-->

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jcl-over-slf4j</artifactId>
            <version>1.7.5</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>log4j-over-slf4j</artifactId>
            <version>1.7.5</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jul-to-slf4j</artifactId>
            <version>1.7.5</version>
        </dependency>

    </dependencies>
```

### 2.2 加入Logback的配置文件

Logback如何加载他的配置文件呢？如下所示：
 1. Logback试图在classpath查找logback.groovy
 2. 如果找不到，logback接着找logback-test.xml
 3. 如果还找不到，就找logback.xml
 4. 如果仍然找不到就使用BasicConfigurator将日记打印到命令行


 **那我们可以自定义logback的配置文件路径吗？**
 ```java
    <listener>
        <listener-class>
            com.boaotech.util.LogbackConfigListener
        </listener-class>
    </listener>
    <context-param>
        <param-name>logbackConfigLocation</param-name>
        <param-value>WEB-INF/logback.xml</param-value>
    </context-param>

```

### 2.3 Logback的配置文件格式

有过配置log4j经验的人很容易就可以看懂Logback的配置文件格式：
```xml
    <configuration scan="true" scanPeriod="30 seconds">

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>STDOUT %date %level [%thread] %logger.%class{0}#%method [%file:%line] %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="ROLLING" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <prudent>true</prudent>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>
                myApp-%d{yyyy-MM-dd}.%i.log
            </fileNamePattern>
            <maxHistory>30</maxHistory>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <encoder>
            <pattern>ROLLING %date %level [%thread] %logger.%class{0}#%method [%file:%line] %msg%n</pattern>
        </encoder>
    </appender>


    <root level="info">
        <appender-ref ref="STDOUT"/>
    </root>
    <!--thos logger name is very important, java source code must match this to print log-->
    <logger name="com">
        <appender-ref ref="ROLLING"/>
    </logger>
</configuration>
```
具体的配置语法见[config syntax](http://logback.qos.ch/manual/configuration.html#syntax)


### 2.4 log4j迁移到Logback

Logback网站上提供了log4j配置文件的转换器 [Translator](http://logback.qos.ch/translator/)