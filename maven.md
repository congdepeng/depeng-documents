

mvn install -Dmaven.test.skip=true



```xml
     <plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-surefire-plugin</artifactId>
	<version>2.12.4</version>
	<configuration>
		<skipTests>true</skipTests>
	</configuration>
    </plugin>
```