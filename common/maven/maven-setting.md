1 setting.xml
-------------

maven的配置文件setting.xml存在于两个地方：

*   安装的地方：${M2\_HOME}/conf/settings.xml
*   用户的目录:${user.home}/.m2/settings.xml，该目录可以自己定义

前者称为全部变量，对操作系统的所有使用者有效；后者为用户配置，只对当前操作系统的使用者有效。如果两者共存，内容将会合并，并且用户配置会覆盖全局配置。

Maven安装后，用户目录下不会自动生成settings.xml，只有全局配置文件。如果需要创建用户范围的settings.xml，可以将安装路径下的settings复制到目录${user.home}/.m2/。Maven默认的settings.xml是一个包含了注释和例子的模板，可以快速的修改它来达到你的要求。

全局配置一旦更改，所有的用户都会受到影响，而且如果maven进行升级，所有的配置都会被清除，所以要提前复制和备份${M2\_HOME}/conf/settings.xml文件，一般情况下不推荐配置全局的settings.xml。

下面的配置文件对各个节点的含义及作用都有注解。实际应用中，经常使用的是<localRepository>、<servers>、<mirrors>、<profiles>有限几个节点，其他节点使用默认值足够应对大部分的应用场景。

关于xmlns属性的解析可以查看文章：[https://zhuanlan.zhihu.com/p/148110873](https://links.jianshu.com/go?to=https%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F148110873)

```
<?xml version="1.0" encoding="UTF-8"?>
<settings
    xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">

    
    <localRepository>usr/local/maven</localRepository>

    
    <interactiveMode>true</interactiveMode>

    
    <usePluginRegistry>false</usePluginRegistry>

    
    <offline>false</offline>

    

    <pluginGroups>
        
        <pluginGroup>org.codehaus.mojo</pluginGroup>
    </pluginGroups>

    
    <proxies>
        
        <proxy>
            
            <id>myproxy</id>
            
            <active>true</active>
            
            <protocol>http://…</protocol>
            
            <host>proxy.somewhere.com</host>
            
            <port>8080</port>
            
            <username>proxyuser</username>
            
            <password>somepassword</password>
            
            <nonProxyHosts>*.google.com|ibiblio.org</nonProxyHosts>
        </proxy>
    </proxies>

    
    <servers>
        
        <server>
            
            <id>server001</id>
            
            <username>my_login</username>
            
            <password>my_password</password>
            
            <privateKey>${usr.home}/.ssh/id_dsa</privateKey>
            
            <passphrase>some_passphrase</passphrase>
            
            <filePermissions>664</filePermissions>
            
            <directoryPermissions>775</directoryPermissions>
            
            <configuration></configuration>
        </server>
    </servers>

    
    <mirrors>
        
        <mirror>
            
            <id>planetmirror.com</id>
            
            <name>PlanetMirror Australia</name>
            
            <url>http://downloads.planetmirror.com/pub/maven2</url>
            
            <mirrorOf>central</mirrorOf>
        </mirror>
    </mirrors>

    
    <profiles>

        
        <profile>
            
            <id>test</id>

            
            <activation>
                
                <activeByDefault>false</activeByDefault>
                
                <jdk>1.7</jdk>

                
                <os>
                    
                    <name>Windows XP</name>
                    
                    <family>Windows</family>
                    
                    <arch>x86</arch>
                    
                    <version>5.1.2600</version>
                </os>

                
                <property>
                    
                    <name>mavenVersion</name>
                    
                    <value>2.0.3</value>
                </property>

                
                <file>
                    
                    <exists>/usr/local/hudson/hudson-home/jobs/maven-guide-zh-to-production/workspace/</exists>
                    
                    <missing>/usr/local/hudson/hudson-home/jobs/maven-guide-zh-to-production/workspace/</missing>
                </file>
            </activation>

            
            <properties>
                
                <user.install>usr/local/winner/jobs/maven-guide</user.install>
            </properties>

            
            <repositories>
                
                <repository>
                    
                    <id>codehausSnapshots</id>
                    
                    <name>Codehaus Snapshots</name>

                    
                    <releases>
                        
                        <enabled>false</enabled>
                        
                        <updatePolicy>always</updatePolicy>
                        
                        <checksumPolicy>warn</checksumPolicy>
                    </releases>

                    
                    <snapshots>
                        <enabled />
                        <updatePolicy />
                        <checksumPolicy />
                    </snapshots>

                    
                    <url>http://snapshots.maven.codehaus.org/maven2</url>

                    
                    <layout>default</layout>
                </repository>
            </repositories>

            
            <pluginRepositories>
                
                <pluginRepository>
                    <releases>
                        <enabled />
                        <updatePolicy />
                        <checksumPolicy />
                    </releases>
                    <snapshots>
                        <enabled />
                        <updatePolicy />
                        <checksumPolicy />
                    </snapshots>
                    <id />
                    <name />
                    <url />
                    <layout />
                </pluginRepository>
            </pluginRepositories>

            
            <activeProfiles>
                <activeProfile>env-test</activeProfile>
            </activeProfiles>
        </profile>
    </profiles>
</settings> 
```

2.pom.xml
---------

settings.xml文件主要用于配置maven运行环境等一系列通用属性，是全局级别的配置文件，pom.xml文件描述了项目的maven坐标、依赖关系等，开发者需要遵循的规则，缺陷管理系统，组织和licenses，以及其他所有的项目相关因素，是项目级别的配置文件。

2.1 基础配置
--------

一个典型的pom.xml文件配置为：

```
<project
    xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    
    <modelVersion>4.0.0</modelVersion>

    
    <groupId>com.winner.trade</groupId>

    
    <artifactId>trade-core</artifactId>

    
    <version>1.0.0-SNAPSHOT</version>

    
    <packaging>jar</packaging>

    
    <classifier>...</classifier>

    
    <dependencies>

        
        <dependency>

            
            <groupId>com.winner.trade</groupId>
            <artifactId>trade-test</artifactId>
            <version>1.0.0-SNAPSHOT</version>

            
            <scope>test</scope>

            
            <optional>false</optional>

            
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-api</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    
    <properties>
        <file.encoding>UTF-8</file.encoding>
        <java.source.version>1.5</java.source.version>
        <java.target.version>1.5</java.target.version>
    </properties>

    ...

</project> 
```

上面的配置定义了项目的基本属性。

### 2.1.1 dependencies

dependencies属性是项目相关依赖的配置，如果在父项目写的依赖，会被子项目引用。一般会在父项目中定义子项目中所有的共用的依赖。

### 2.1.2 parent

parent属性用于确认父项目的坐标位置。

```
<parent>
    <groupId>com.learnPro</groupId>
    <artifactId>SIP-parent</artifactId>
    <relativePath></relativePath>
    <version>0.0.1-SNAPSHOT</version>
</parent> 
```

groupId: 父项目的组Id标识符 artifactId:父项目的唯一标识符 relativePath：Maven首先在当前项目中找父项目的pom，然后在文件系统的这个位置（relativePath），然后在本地仓库，再在远程仓库找。 version: 父项目的版本

### 2.1.3 modules

有些maven项目会作为多模块，如SpringMVC项目就分为dal/service/controller三个模块，这个标签就是用于指定当前项目所包含的所有模块。之后对这个项目进行的maven操作就是对所有子模块进行相同操作。

```
<modules>
   <module>com-dal</>
   <module>com-service</>
   <module>com-controller</>
<modules/> 
```

### 2.1.4 properties

properties属性用于定于pom常亮，如一些依赖的版本号等。

```
<properties>
    <java.version>1.7</java.version>
</properties> 
```

此时可以在pom文件任意地方使用${Java.version}进行引用指定版本号。

### 2.1.5 dependencyManagement

在父模块中定义后，子模块不会直接使用对应依赖，但是在使用相同依赖的时候可以不加版本号,这样的好处是，父项目统一了版本，而且子项目可以在需要的时候才引用对应的依赖。

```
父项目：
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

子项目：

<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
</dependency> 
```

### 2.1.6 classifier

classifier元素用来帮助定义构建输出的一些附属构件。附属构件与主构件对应，比如主构件是 kimi-app-2.0.0.jar，该项目可能还会通过使用一些插件生成 如 kimi-app-2.0.0-javadoc.jar （Java文档）、 kimi-app-2.0.0-sources.jar（Java源代码） 这样两个附属构件。这时候，javadoc、sources就是这两个附属构件的classifier，这样附属构件也就拥有了自己唯一的坐标。

classifier的用途在于:

*   maven download javadoc / sources jar包的时候，需要借助classifier指明要下载那个附属构件
*   引入依赖的时候，有时候仅凭groupId、artifactId、version无法唯一的确定某个构件，需要借助classifier来进一步明确目标。比如JSON-lib，有时候会同一个版本会提供多个jar包，此时可以用于指定java版本。

```
<dependency>
    <groupId>net.sf.json-lib</groupId>
    <artifactId>json-lib</artifactId>
    <version>2.4</version>
    <classifier>jdk15</classifier> 
</dependency> 
```

2.2 构建配置
--------

```
<build>

     
    <finalName>myPorjectName</finalName> 

     
    <directory>${basedir}/target</directory> 

     
    <defaultGoal>install</defaultGoal>

     
    <filters>
            <filter>../filter.properties</filter>
    </filters> 

     
    <resources> 
        <resource> 

             
            <targetPath>resources</targetPath> 

             
            <filtering>true</filtering> 

             
            <directory>src/main/resources</directory> 

             
            <includes>  
                <include>**/*.properties</include>  
                <include>**/*.xml</include>  
            </includes>  

             
            <excludes>  
                <exclude>jdbc.properties</exclude>  
                </excludes> 

        </resource> 
    </resources> 

     
    <testResources> 
        <testResource> 
            <targetPath /><filtering /><directory /><includes /><excludes /> 
        </testResource> 
    </testResources> 

     
    <sourceDirectory>${basedir}\src\main\java</sourceDirectory> 

     
    <scriptSourceDirectory>${basedir}\src\main\scripts</scriptSourceDirectory>

     
    <testSourceDirectory>${basedir}\src\test\java</testSourceDirectory>

     
    <outputDirectory>${basedir}\target\classes</outputDirectory>

     
    <testOutputDirectory>${basedir}\target\test-classes</testOutputDirectory> 

     
    <extensions> 

         
        <extension> 
            <groupId>org.apache.maven.wagon</groupId>
            <artifactId>wagon-ssh</artifactId>
            <version>2.8</version>
        </extension> 

    </extensions> 

     
    <plugins> 
        <plugin> 
            <groupId></groupId>
            <artifactId>maven-assembly-plugin</artifactId>
            <version>2.5.5</version>

             
            <executions>
                <execution>

                         
                    <id>assembly</id>

                     
                    <phase>package</phase>

                     
                    <goals>
                        <goal>single</goal>
                    </goals>

                     
                    <inherited>false</inherited> 

                </execution>
            </executions>

            
            <configuration>
                <finalName>${finalName}</finalName>
                <appendAssemblyId>false</appendAssemblyId>
                <descriptor>assembly.xml</descriptor>
            </configuration>

             
            <extensions>false</extensions> 

             
            <dependencies> 
                <dependency>...</dependency> 
            </dependencies> 

             
            <inherited>true</inherited>

        </plugin> 
    </plugins> 

     
    <pluginManagement> 
        <plugins>...</plugins>
    </pluginManagement>

</build> 
```

pom里面的仓库与settings.xml仓库功能一致，主要区别在于：pom仓库是个性化，比如说大公司的setting文件是公用的，所有项目全部使用同一个settings文件，但是各自项目却会引用不同的第三方库，所以就需要在pom设置自己需要的仓库地址。

2.3 分发配置
--------

```
 <distributionManagement> 

     
    <repository> 

         
        <uniqueVersion>true</uniqueVersion> 

        <id> repo-id </id> 
        <name> repo-name </name> 
        <url> file://${basedir}/target/deploy </url> 
        <layout /> 

    </repository> 

     
    <snapshotRepository> 
        <uniqueVersion /> 
        <id />
        <name /> 
        <url /> 
        <layout /> 
    </snapshotRepository> 

     
    <site> 

         
        <id> site-id </id> 

         
        <name> site-name </name> 

         
        <url> scp://svn.baidu.com/banseon:/var/www/localhost/banseon-web </url> 

    </site> 

     
    <downloadUrl /> 

     
    <relocation> 

         
        <groupId /> 

         
        <artifactId /> 

         
        <version /> 

         
        <message /> 

    </relocation> 

     
    <status /> 

</distributionManagement> 
```

2.4 仓库配置
--------

```
 <repositories> 

     
    <repository> 

         
        <releases> 

             
            <enabled /> 

             
            <updatePolicy /> 

             
            <checksumPolicy /> 

        </releases> 

         
        <snapshots> 
            <enabled /><updatePolicy /><checksumPolicy /> 
        </snapshots> 

         
        <id> repo-id </id> 

         
        <name> repo-name </name> 

         
        <url> http://192.168.1.169:9999/repository/ </url> 

         
        <layout> default </layout> 

    </repository> 

</repositories> 

 
<pluginRepositories> 

     
    <pluginRepository /> 

</pluginRepositories> 
```

2.5 报表配置
--------

reporting元素描述使用报表插件产生报表的规范。当用户执行“mvn site”，这些报表就会运行。 在页面导航栏能看到所有报表的链接。

```
 <reporting> 

     
    <excludeDefaults /> 

     
    <outputDirectory /> 

     
    <plugins> 

        <plugin> 
            <groupId /> 
            <artifactId />
            <version />
            <inherited />
            <configuration> 
                <links> 
                    <link>http://java.sun.com/j2se/1.5.0/docs/api/</link> 
                </links> 
            </configuration>
             
            <reportSets> 

                 
                <reportSet> 

                     
                    <id>sunlink</id> 

                     
                    <configuration /> 

                     
                    <inherited /> 

                     
                    <reports>
                        <report>javadoc</report>  
                    </reports>

                </reportSet> 

            </reportSets> 

        </plugin> 

    </plugins> 

</reporting> 
```

2.6 项目配置
--------

```
 <issueManagement> 

     
    <system> jira </system> 

     
    <url> http://jira.clf.com/ </url> 

</issueManagement> 

 
<ciManagement> 

     
    <system>continumm</system> 

     
    <url>http://127.0.0.1:8080/continuum</url>

     
    <notifiers> 

         
        <notifier> 

             
            <type /> 

             
            <sendOnError /> 

             
            <sendOnFailure /> 

             
            <sendOnSuccess /> 

             
            <sendOnWarning /> 

             
            <address /> 

             
            <configuration /> 

        </notifier> 

    </notifiers> 

</ciManagement>

 
<name> banseon-maven </name> 

 
<url> http://www.clf.com/ </url> 

 
<description> A maven project to study maven. </description> 

 
<prerequisites> 

     
    <maven /> 

</prerequisites> 

 
<inceptionYear /> 

 
<mailingLists> 

     
    <mailingList> 

         
        <name> Demo </name> 

         
        <post> clf@126.com </post> 

         
        <subscribe> clf@126.com </subscribe> 

         
        <unsubscribe> clf@126.com </unsubscribe> 

         
        <archive> http:/hi.clf.com/ </archive> 

    </mailingList> 

</mailingLists> 

 
<developers> 

     
    <developer> 

         
        <id> HELLO WORLD </id> 

         
        <name> banseon </name> 

         
        <email> banseon@126.com </email> 

         
        <url /> 

         
        <roles> 
            <role> Project Manager </role> 
            <role> Architect </role> 
        </roles> 

         
        <organization> demo </organization> 

         
        <organizationUrl> http://hi.clf.com/ </organizationUrl> 

         
        <properties> 
            <dept> No </dept> 
        </properties> 

         
        <timezone> -5 </timezone> 

    </developer> 

</developers> 

 
<contributors> 

     
    <contributor> 
        <name /><email /><url /><organization /><organizationUrl />
        <roles /><timezone /><properties /> 
    </contributor> 

</contributors> 

 
<licenses> 

     
    <license> 

         
        <name> Apache 2 </name> 

         
        <url> http://www.clf.com/LICENSE-2.0.txt </url> 

         
        <distribution> repo </distribution> 

         
        <comments> A business-friendly OSS license </comments> 

    </license> 

</licenses> 

 
<scm> 

     
    <connection>scm:svn:http://svn.baidu.com/banseon/maven/</connection> 

     
    <developerConnection>scm:svn:http://svn.baidu.com/banseon/maven/</developerConnection> 

     
    <tag /> 

     
    <url> http://svn.baidu.com/banseon </url> 

</scm> 

 
<organization> 

     
    <name> demo </name> 

     
    <url> http://www.clf.com/ </url> 

</organization> 
```

2.7 profile
-----------

pom.xml中的profile可以看做pom.xml的副本，拥有与pom.xml相同的子元素与配置方法。它包含可选的activation（profile的触发器）和一系列的changes。例如test过程可能会指向不同的数据库（相对最终的deployment）或者不同的dependencies或者不同的repositories，并且是根据不同的JDK来改变的。只需要其中一个成立就可以激活profile，如果第一个条件满足了，那么后面就不会在进行匹配。

```
 <profiles> 

     
    <profile> 
        
        <activation>

             
            <activeByDefault>false</activeByDefault>

             
            <jdk>1.7</jdk>

             
            <os>

                 
                <name>Windows XP</name>

                 
                <family>Windows</family>

                 
                <arch>x86</arch>

                 
                <version>5.1.2600</version>

            </os>

             
            <property>

                 
                <name>mavenVersion</name>

                 
                <value>2.0.3</value>

            </property>

             
            <file>

                 
                <exists>/usr/local/hudson/hudson-home/jobs/maven-guide-zh-to-production/workspace/</exists>

                 
                <missing>/usr/local/hudson/hudson-home/jobs/maven-guide-zh-to-production/workspace/</missing>

            </file>

        </activation>
        <id /> 
        <build />
        <modules /> 
        <repositories /> 
        <pluginRepositories /> 
        <dependencies /> 
        <reporting /> 
        <dependencyManagement /> 
        <distributionManagement /> 
        <properties /> 
    </profile>
</profiles> 
```

profile可以让maven能够自动适应外部的环境变化，比如同一个项目，在linux下编译linux的版本，在win下编译win的版本等。一个项目可以设置多个profile，也可以在同一时间设置多个profile被激活（active）的。自动激活的 profile的条件可以是各种各样的设定条件，组合放置在activation节点中，也可以通过命令行直接指定。如果认为profile设置比较复杂，可以将所有的profiles内容移动到专门的 profiles.xml 文件中，不过记得和pom.xml放在一起。

activation节点是设置该profile在什么条件下会被激活，常见的条件有如下几个.

### 2.7.1 os

判断操作系统相关的参数，它包含如下可以自由组合的子节点元素

```
message - 规则失败之后显示的消息
arch - 匹配cpu结构，常见为x86
family - 匹配操作系统家族，常见的取值为：dos，mac，netware，os/2，unix，windows，win9x，os/400等
name - 匹配操作系统的名字
version - 匹配的操作系统版本号
display - 检测到操作系统之后显示的信息 
```

### 2.7.2 jdk

检查jdk版本，可以用区间表示。

### 2.7.3 property

检查属性值，本节点可以包含name和value两个子节点。

### 2.7.4 file

检查文件相关内容，包含两个子节点：exists和missing，用于分别检查文件存在和不存在两种情况。

如果想要某个profile默认处于激活状态，可以在<activeProfiles>中将该profile的id放进去。这样，不论环境设置如何，其对应的 profile都会被激活。

profile配置项在setting.xml中页有，是pom.xml中profile元素的裁剪版本，包含了id，activation, repositories, pluginRepositories和 properties元素。这里的profile元素只包含这五个子元素是因为setting.xml只关心构建系统这个整体（这正是settings.xml文件的角色定位），而非单独的项目对象模型设置。如果一个settings中的profile被激活，它的值会覆盖任何其它定义在POM中或者profile.xml中的带有相同id的profile。