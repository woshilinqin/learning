## maven构建包时build标签

首先看下一个简单的pom.xml

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com</groupId>
    <artifactId>Spring-Security</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>war</packaging>

    <properties>
        <jdk.version>1.7</jdk.version>
        <spring.version>3.2.8.RELEASE</spring.version>
        <spring.security.version>3.2.3.RELEASE</spring.security.version>
        <jstl.version>1.2</jstl.version>
    </properties>

    <dependencies>

        <!-- Spring dependencies -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>

        ...

    </dependencies>

</project>
```

正常打包出来的war包结构

├─META-INF

│  └─maven

│      └─com

│          └─Spring-Security

└─WEB-INF

```
├─classes

│  └─com

│      └─controller

├─lib

└─pages
```

打包出来的是一个web项目，包括lib下的jar包，页面等。

## 打jar包

修改&lt;packaging&gt;war&lt;/packaging&gt; 的war为jar，jar包没有关联的libjar包，没有相关的页面，单纯是项目中的所有package。

├─com

│  └─controller

└─META-INF

```
└─maven

    └─com

        └─Spring-Security
```

![](/assets/jar包目录结构.png)

自定义打jar需要插件实现

```
<build>
		<finalName>hicardmvc</finalName>
		<plugins>
            <plugin>
                <artifactId>maven-jar-plugin</artifactId>
                <executions>
                    <execution>
                        <id>controller</id>
                        <goals><goal>jar</goal></goals>
                        <phase>package</phase>
                        <configuration>
                            <classifier>controller</classifier>
                            <includes>
                                <include>**/controller/**</include>
                            </includes>
                        </configuration>
                    </execution>
                    <execution>
                        <id>service</id>
                        <goals><goal>jar</goal></goals>
                        <phase>package</phase>
                        <configuration>
                            <classifier>service</classifier>
                            <includes>
                                <include>**/service/**</include>
                            </includes>
                        </configuration>
                    </execution>
                    <execution>
                        <id>dao</id>
                        <goals><goal>jar</goal></goals>
                        <phase>package</phase>
                        <configuration>
                            <classifier>dao</classifier>
                            <includes>
                                <include>**/dao/**</include>
                                <include>**/model/**</include>
                            </includes>
                        </configuration>
                    </execution>
                    <execution>
                        <id>utils</id>
                        <goals><goal>jar</goal></goals>
                        <phase>package</phase>
                        <configuration>
                            <classifier>uitls</classifier>
                            <includes>
                                <include>cn/hicard/utils/**</include>
                            </includes>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
            	<artifactId>maven-war-plugin</artifactId>
            	<configuration>
            		<version>2.5</version>
            	</configuration>
            </plugin>
		</plugins>
		
		<resources>
			<resource>
				<directory>src/main/java</directory>
				<excludes>
					<exclude>**/*.java</exclude>
					<exclude>**/.svn/*</exclude>
				</excludes>
			</resource>
			
			<resource>
				<directory>src/main/resources</directory>
				<includes>
					<include>**/*.properties</include>
					<include>**/*.xml</include>
				</includes>
			</resource>
			
		</resources>
	</build>
```





