## maven

#### 1. 本地jar包加入maven库

`mvn install:install -file -D groupId=cn.com.citycloud -D artifactId=authorization -Dversion=1.0 -D packaging=jar -D file=authorization.jar完整路径`

>mvn install:install-file -DgroupId=com.citycloud.qcloud -DartifactId=qcloud-oauth2-client -Dversion=2.2.0 -Dpackaging=jar -Dfile=qcloud-oauth2-resource-2.2.0.RELEASE.jar
#### 2. 本地的包放在本地文件夹，加入pom文件

```xml
<scope>system</scope>
<systemPath>${project.basedir}/lib/xxx.jar</systemPath>

## 这种方式引入的jar包在打包时不会被包含在war包中
```
#### 3.pom.xml

```xml

<groupId>xxx</groupId> 通常使用全限定的包名区分该项目和其他项目。
<artifactId>xxx</artifactId> 通常和项目名称保持统一
<packaging> jar </packaging>

<repositories> 发现依赖和扩展的远程仓库列表
    <repository>
        <id>alimaven</id>
        <name>aliyun maven</name>       
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </repository>
</repositories>

<dependencies>
<!-- <scope>
compile 默认就是compile,依赖项目需要参与当前项目的编译，后续的测试，运行
test 依赖项目仅仅参与测试相关的工作，包括测试代码的编译，执行
provided 打包的时候可以不用包进去
system 被依赖项不会从maven仓库抓，而是从本地文件系统拿，一定需要配合systemPath属性使用
-->
    <scope>test</scope>
    从依赖构件列表里，列出被排除的依赖构件集,此元素主要用于解决版本冲突问题       
    <exclusions>
        <exclusion>
            <artifactId>spring-core</artifactId>
            <groupId>org.springframework</groupId>
        </exclusion>
    </exclusions>
<dependencies/>

```



### 打包

#### 1. jar包：
```xml
<build>
<!-- 打成war不需要这个maven插件，如果打成jar则是必须的 -->
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
    <finalName>springbootdemo</finalName>
</build>
```
spring-boot 默认提供内嵌的tomcat，所以打包直接生成jar 包，用java -jar 命令就可以启动。
//nohup 意思是不挂断运行命令,当账户退出或终端关闭时,程序仍然运行
//当用 nohup 命令执行作业时，缺省情况下该作业的所有输出被重定向到nohup.out的文件中
`nohup java -jar test.jar >temp.txt &`

Spring程序会按优先级从下面这些路径来加载application.properties配置文件
* 当前目录下的/config目录
* 当前目录
* classpath里的/config目录
* classpath 跟目录

`nohup java -jar -Dspring.config.location=D:\config\config.properties xxx.jar> /xx/nohup.out 2>&1 &`

#### 2. war包：
有时候我们更希望一个tomcat 来管理多个项目，这种情况下就需要项目是war 格式的。
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>
</dependency>
```
spring-boot-starter-tomcat 是原来被传递过来的依赖，默认会打到包里，所以我们再次引入此依赖，并指定依赖范围为provided，这样tomcat 相关的jar就不会打包到war 里了

添加ServletInitializer将项目打成war包，部署到外部的tomcat中，这个时候，不能直接访问spring boot 项目中配置文件配置的端口。

application.yml中配置的server.port配置的是spring boot内置的tomcat的端口号, **打成war包部署在独立的tomcat上之后, 你配置的server.port是不起作用的**。

#### 3. assembly插件

为什么需要Assembly插件呢？因为对于**服务类型的后台程序**，线上运行时一般通过脚本的方式启动和停止，而项目中的目录结构复杂且不够直观，且**项目文件也需要合并和过滤**，Assembly插件就可以帮助我们完成。


比如现在我们的项目目录如下：
```
+ src 
    + main 
        + java 
        + resources 
            + jdbc.properties 
        + bin 
            + start.sh 
            + stop.sh 
        + assembly 
            + assembly.xml
```

最终想要的目录：
```
+ bin 
    start.sh 
    stop.sh 
+ conf
    jdbc.properties 
+ lib(依赖jar包目录) 
    xxx.jar
```



### settings.xml

#### 1.MIrrors

* 使用镜像的一些原因：互联网上有一个同步镜像，地理位置更近，速度更快
* 您希望将特定存储库替换为您可以更好地控制的内部存储库
```xml

<mirrors>
    <mirror>
    <!--id，name：id必须唯一(不要与仓库id一样)，name随意-->
      <id>planetmirror.com</id>
      <name>PlanetMirror Australia</name>
      <!--url：镜像的地址-->
      <url>http://downloads.planetmirror.com/pub/maven2</url>
      <!--mirrorOf：需要被镜像的仓库id。例如，要指向Maven中央存储库（https://repo.maven.apache.org/maven2/）的镜像，请将此元素设置为central-->
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>


```

#### 2.Profiles
settings.xml中的profile元素是pom.xml profile元素的精简版本。
它由activation，repositories，pluginRepositories和properties元素组成.
**如果profile在settings.xml中被激活，则其值将覆盖POM或profiles.xml文件中任何等效的ID配置。**

```xml
 <profile>  
     <id>downloadSources</id>  
    <properties>  
      <downloadSources>true</downloadSources>  
      <downloadJavadocs>true</downloadJavadocs>             
    </properties>  
  </profile>  

```

#### 3.Repositories
Repositories是Maven用于填充构建系统的本地存储库的项目的远程集合。它来自这个本地存储库，Maven称之为插件和依赖项。不同的远程存储库可能包含不同的项目，并且在启用的profile下，可以搜索它们以查找匹配的版本或快照工件。

#### 4.Plugin Repositories
pluginRepositories元素块的结构类似于repositories元素。 pluginRepository元素指定Maven可以在哪里找到新插件的远程位置。

```xml
<profile>  
      <id>nexus_150</id>  
      <repositories>  
        <repository>	
          <id>maven-releases</id>  
          <name>local private nexus</name>  
          <url>http://115.231.97.150/nexus/repository/releases/</url>  
          <releases>  
            <enabled>true</enabled>  
          </releases>  
          <snapshots>  
            <enabled>false</enabled>  
          </snapshots>  
        </repository>  
        <repository>  
          <id>maven-snapshots</id>  
          <name>local private nexus</name>  
          <url>http://115.231.97.150/nexus/repository/maven-snapshots/</url>  
          <releases>  
            <enabled>false</enabled>  
          </releases>  
          <snapshots>  
            <enabled>true</enabled>  
          </snapshots>  
        </repository>

<!--
  qcloud-oauth2-client-1.1.0

-->
<!--	<repository>
         <id>cci-snapshots</id>
         <url>http://10.10.81.107:8080/nexus/content/repositories/snapshots/</url>
        </repository>
        <repository>
         <id>cci-releases</id>
         <url>http://10.10.81.107:8080/nexus/content/groups/public/</url>
        </repository>-->
      </repositories>  
      
      <pluginRepositories>  
        <pluginRepository>  
          <id>maven-releases</id>  
          <name>local private nexus</name>  
          <url>http://115.231.97.150/nexus/repository/releases/</url>  
          <releases>  
            <enabled>true</enabled>  
          </releases>  
          <snapshots>  
            <enabled>false</enabled>  
          </snapshots>  
        </pluginRepository>  
        <pluginRepository>  
          <id>maven-snapshots</id>  
          <name>local private nexus</name>  
          <url>http://115.231.97.150/nexus/repository/maven-snapshots/</url>  
          <releases>  
            <enabled>false</enabled>  
          </releases>  
          <snapshots>  
            <enabled>true</enabled>  
          </snapshots>  
        </pluginRepository>  
      </pluginRepositories> 
    </profile>  
```

#### 5.启用Profiles

```xml
  <activeProfiles>  
  <activeProfile>nexus_150</activeProfile>
  <!--    <activeProfile>downloadSources</activeProfile>  
  <activeProfile>public-snapshots</activeProfile>   -->
  </activeProfiles> 

```