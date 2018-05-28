* > > ## maven构建包时build标签

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

使用插件打多个jar包。

以下是打包复制标签&lt;resources&gt;例子

```
<build>
    <finalName>hc-service</finalName>

    <resources>
        <resource>
            <targetPath>${project.build.directory}/classes</targetPath>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
            <includes>
                <include>**/*.xml</include>
                <include>**/*.properties</include>
            </includes>
        </resource>
        <!-- 结合com.alibaba.dubbo.container.Main 按照约定把spring文件夹下面配置拷贝到META-INF下面 -->
        <resource>
            <targetPath>${project.build.directory}/classes/META-INF/spring</targetPath>
            <directory>src/main/resources/spring</directory>
            <filtering>true</filtering>
            <includes>
                <include>spring-context.xml</include>
            </includes>
        </resource>
        <!-- 复制META-INF下面的配置到生成jar下,包括自定义的jetty内置容器和ip过滤白名单 -->
        <resource>
            <targetPath>${project.build.directory}/classes/META-INF/dubbo/internal</targetPath>
            <directory>src/main/resources/META-INF/dubbo/internal</directory>
            <filtering>true</filtering>
            <includes>
                <include>*</include>
            </includes>
        </resource>
    </resources>

    <pluginManagement>
        <plugins>
            <!-- 解决Maven插件在Eclipse内执行了一系列的生命周期引起冲突 -->
            <plugin>
                <groupId>org.eclipse.m2e</groupId>
                <artifactId>lifecycle-mapping</artifactId>
                <version>1.0.0</version>
                <configuration>
                    <lifecycleMappingMetadata>
                        <pluginExecutions>
                            <pluginExecution>
                                <pluginExecutionFilter>
                                    <groupId>org.apache.maven.plugins</groupId>
                                    <artifactId>maven-dependency-plugin</artifactId>
                                    <versionRange>[2.0,)</versionRange>
                                    <goals>
                                        <goal>copy-dependencies</goal>
                                    </goals>
                                </pluginExecutionFilter>
                                <action>
                                    <ignore />
                                </action>
                            </pluginExecution>
                        </pluginExecutions>
                    </lifecycleMappingMetadata>
                </configuration>
            </plugin>
        </plugins>
    </pluginManagement>
    <plugins>
        <!-- 打包jar文件时，配置manifest文件，加入lib包的jar依赖 -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <configuration>
                <classesDirectory>target/classes/</classesDirectory>
                <archive>
                    <manifest>
                        <mainClass>com.alibaba.dubbo.container.Main</mainClass>
                        <!-- 打包时 MANIFEST.MF文件不记录的时间戳版本 -->
                        <useUniqueVersions>false</useUniqueVersions>
                        <addClasspath>true</addClasspath>
                        <classpathPrefix>lib/</classpathPrefix>
                    </manifest>
                    <manifestEntries>
                        <Class-Path>.</Class-Path>
                    </manifestEntries>
                </archive>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-dependency-plugin</artifactId>
            <executions>
                <execution>
                    <id>copy-dependencies</id>
                    <phase>package</phase>
                    <goals>
                        <goal>copy-dependencies</goal>
                    </goals>
                    <configuration>
                        <type>jar</type>
                        <includeTypes>jar</includeTypes>
                        <useUniqueVersions>false</useUniqueVersions>
                        <outputDirectory>
                            ${project.build.directory}/lib
                        </outputDirectory>
                    </configuration>
                </execution>
            </executions>
        </plugin>
        <!-- 默认jdk1.7 -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.7</source>
                <target>1.7</target>
            </configuration>
        </plugin>
    </plugins>


</build>
```



