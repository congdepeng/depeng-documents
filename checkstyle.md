#Checkstyle
------------------------------
Javac控制了Java代码的语法，只要符合语法的源文件就可以编译。而代码在可编译和可维护之间还有一个巨大的鸿沟，Checkstyle正是为此而生。


要理解Checkstyle，需要从理解modules开始。
#modules overview

- modules 是一个树形结构（XML嘛），根节点是一个叫Checker的module<module name="Checker">
- Checker module下面包含3类子类型的module，而且可定义全局的属性
    * FileSetChecks - 取一组文件来分析错误的modules，例如TreeWalker
    * Filters - 过滤审计事件，包括错误信息等的modules
    * AuditListeners - 报告接受的事件的modules
- 很多modules都是TreeWalker FileSetCheck的子modules
- TreeWalker一个个的转换Java源文件为抽象语法树，然后让子模块一个个的去审查这个语法树
- Checkstyle使用配置文件，例如sun_checks.xml


## modules details
下面是一个样板代码，Checker是根，下面包含“FileSetChecks”：2个JavadocPackage检查Javadoc,TreeWalker包含N多子modules来检查代码。

```xml
  <module name="Checker">
    <module name="JavadocPackage"/>
    <module name="TreeWalker">
        <module name="AvoidStarImport"/>
        <module name="ConstantName"/>
        <module name="EmptyBlock"/>
    </module>
  </module>
```
很明显，Checkstyle根据module的name来识别modules的，有好几个规则来加载一个对应的module处理类
    - 写类全路径
    - 直接写类名，会去默认包下面找com.puppycrawl.tools.checkstyle, com.puppycrawl.tools.checkstyle.filters, and com.puppycrawl.tools.checkstyle.checks and its sub-packages

### Properties
Propes of a module control how the module performs its task.

### TreeWalker



## Filters
过滤器可以接受或者拒绝event，只有被所有过滤器接受的event才会被打印出来。

虽然名称叫Filters，它依然只是一个module。

```xml
    <module name="SeverityMatchFilter">
    <property name="severity" value="info"/>
    <property name="acceptOnMatch" value="false"/>
</module>

```

    - SeverityMatchFilter： 等级过滤
    - SuppressionFilter： 可以配置来抑制一些异常警告
    - SuppressionCommentFilter


## AuditListeners
自定义事件的报告。例如你想要自定义一些附加的text或者XML输出。


## Run

    - command line
        ```java
            java -D<property>=<value>  \
             com.puppycrawl.tools.checkstyle.Main \
             -c <configurationFile> \
             [-f <format>] [-p <propertiesFile>] [-o <file>] \
             [-r <dir>] file...

             java com.puppycrawl.tools.checkstyle.Main -c docs/sun_checks.xml  Check.java

        ```

    - xxx


## 列出所有现有的Checks
http://checkstyle.sourceforge.net/availablechecks.html

## 根据功能划分Checks（browse the individual checks by functionality.）
http://checkstyle.sourceforge.net/checks.html

##如何扩展Checkstyle




#### 多模块的项目如何统一配置https://maven.apache.org/plugins/maven-checkstyle-plugin/examples/multi-module-config.html