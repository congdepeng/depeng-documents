
find 路径 -name '*.c'

  * -name怎么才一个dash呢？？
  * '*.c' 不加引号就无法正则表达式

   <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-checkstyle-plugin</artifactId>
                <version>2.11</version>
                <configuration>
                    <configLocation>../visenze_checks.xml</configLocation>
                    <excludes>
                        com/weardex/visualsearch/thrift/**,com/weardex/visualindex/thrift/**,com/weardex/thrift/server/**,com/weardex/thrift/server/*
                    </excludes>
                </configuration>
            </plugin>