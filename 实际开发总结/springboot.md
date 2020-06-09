## 基础

### 0. 基础
启动springboot：
`mvn spring-boot:run`

### 1.重要细节
#### @Controller/@Restcontroller
* 使用@Controller 注解，在对应的方法上，视图解析器可以解析return 的jsp,html页面，并且跳转到相应页面。
* 如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。
* @RestController注解，相当于@Controller+@ResponseBody两个注解的结合，返回json数据不需要在方法前面加@ResponseBody注解了

#### @RequestMapping/@GetMapping
* @GetMapping是一个组合注解 是@RequestMapping(method = RequestMethod.GET)的缩写
#### @RequestParam
* 用来处理Content-Type: 为 application/x-www-form-urlencoded编码的内容。提交方式为get或post。（Http协议中，如果不指定Content-Type，则默认传递的参数就是application/x-www-form-urlencoded类型）
* RequestParam实质是将Request.getParameter() 中的Key-Value参数Map利用Spring的转化机制ConversionService配置，转化成参数接收对象或字段。
* **GET和POST请求传的参数会自动转换赋值到@RequestParam 所注解的变量上。
  也可以不使用@RequestParam，直接接收，此时要求controller方法中的参数名称要跟form表单中name名称一致**
#### @RequestBody
* 一般用来处理非Content-Type: application/x-www-form-urlencoded编码格式的数据。
* GET请求中，因为没有HttpEntity，所以@RequestBody并不适用。
  POST请求中，通过HttpEntity传递的参数，必须要在请求头中声明数据的类型Content-Type，SpringMVC通过使用HandlerAdapter 配置的HttpMessageConverters来解析HttpEntity中的数据，然后绑定到相应的bean上。
* @RequestBody用于post请求，不能用于get请求
* **application/json、application/xml等格式的数据，必须使用@RequestBody来处理**。
* multipart/form-data，@RequestBody不能处理这种格式的数据。
#### 启动类
```java
@SpringBootApplication
public class App{   
    public static void main( String[] args )    {        
        //启动springboot项目    
        SpringApplication.run(App.class,args);    
    }
}
```

>**@SpringBootApplication**是一个复合注解，包括@ComponentScan，@SpringBootConfiguration，@EnableAutoConfiguration。

* @ComponentScan，扫描当前包及其子包下被@Component，@Controller，@Service，@Repository注解标记的类并纳入到spring容器中进行管理。
* @SpringBootConfiguration继承自@Configuration，二者功能也一致，标注当前类是配置类，并会将当前类内声明的一个或多个以@Bean注解标记的方法的实例纳入到srping容器中，并且实例名就是方法名。
* @EnableAutoConfiguration的作用启动自动的配置，@EnableAutoConfiguration注解的意思就是Springboot根据你添加的jar包来配置你项目的默认配置，比如根据spring-boot-starter-web ，来判断你的项目是否需要添加了webmvc和tomcat，就会自动的帮你配置web项目中所需要的默认配置。

#### springboot 创建
1. jar包 (quickstart)，建议的方式
2. war包 (webapp)
  有webapp文件目录
3. 依赖
```xml
<!-- 父依赖管理其他依赖的版本-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.x RELEASE</version>
  </parent>
<denpendencies>
<!--web开发的核心依赖，包含了tomcat和springmvc，
    spring-boot-starter包也在里面。         
-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</denpendencies>
```
### 2. 配置属性
#### 配置文件的位置
1. 比如 src/main/resource 下有config文件夹，里面有 applicationContext.xml。   默认发布到target/classes内，所以web.xml的上下文配置文件路径只要写classpath:config/applicationContext.xml 就可以。   
  针对直接放在src/main/resource根目录下的文件，千万注意classpath:后不要有空格。
2. 在classes里面必须有配置文件，配置文件的目录
#### 自定义属性的配置
把配置文件的信息，读取并自动封装成实体类。
1. application.yml里写自定义的属性
```yml
ly:
  jwt:
    pubKeyPath: D:/MY_IDEA/rsa/rsa.pub # 公钥地址
    cookieName: LY_TOKEN # cookie的名称
```
2. 实体类装载配置文件信息 用@Compont和@ConfigurationProperties(prefix="") 
```java
@Compont
@ConfigurationProperties(prefix="ly.jwt")
public class JwtProperties{
    private String pubKeyPath;//公钥路径

    private String cookieName;

    private String KeypublicKey;//公钥
    
    //getter()
}
```
2.1 另一种方法  把@ConfigurationProperties直接定义在@bean的注解上
实体类上就不用再注解了

```java
@Bean
@ConfigurationProperties(prefix="ly.jwt.pubKeyPath")
public JwtProperties jwtProperties(){
return new JwtProperties();
}
```
2.2 对于单个的配置，可以直接用@value写出值
```java
@value("${ly.jwt.}")
private String pubKeyPath;
```
3. 其他类要使用时,@EnableConfigurationProperties(JwtProperties.class)
```java
@EnableConfigurationProperties(JwtProperties.class)
public class xxx{
    @autowired
    private  JwtProperties jwtprop;

}
```





### 3. 整合日志
##### 日志总结
* log4j
```java
用的是 log4j-1.2.17.jar

private static final
org.apache.log4j.Logger logger = org.apache.log4j.Logger.getLogger(Log4jTest.class);
```
* log4j2
```java
用的是 log4j-api-2.7.jar和log4j-core-2.7.jar

private static 
org.apache.logging.log4j.Logger  logger  =  org.apache.logging.log4j.LogManager.getLogger(Log4jTest.class);
```
*  slf4j+log4j
```java
log4j-1.2.17.jar   slf4j-api-1.6.4.jar    slf4j-log4j12-1.6.1.jar

private static final Logger logger = LoggerFactory.getLogger(Slf4jTest2.class);
```
* slf4j+log4j2
```java
log4j-api-2.7.jar和log4j-core-2.7.jar     slf4j-api-1.6.4.jar     log4j-slf4j-impl-2.7.jar

private static final Logger logger = LoggerFactory.getLogger(Slf4jTest2.class);
```
#####  springboot默认的日志框架 logback
* 控制台输出
  Spring Boot中默认配置ERROR、WARN和INFO级别的日志输出到控制台。您还可以通过启动您的应用程序–debug标志来启用“调试”模式（开发的时候推荐开启）。
  在application.properties中配置debug=true，该属性置为true的时候，核心Logger（包含嵌入式容器、hibernate、spring）会输出更多内容。
* 文件输出
  默认情况下，Spring Boot将日志输出到控制台，不会写到日志文件。如果要编写除控制台输出之外的日志文件，则需在application.properties中设置logging.file或logging.path属性。
```
如果只配置 logging.file，会在项目的当前路径下生成一个 xxx.log 日志文件。
如果只配置 logging.path，在 /var/log文件夹生成一个日志文件为 spring.log

默认情况下，日志文件的大小达到10MB时会切分一次，产生新的日志文件
注：二者不能同时使用，如若同时使用，则只有logging.file生效
```


#####  自定义日志
1. 使用默认的日志框架 logback
  创建名为logback-spring.xml的日志配置文件，并且放在src/main/resources下面即可。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration  scan="true" scanPeriod="60 seconds" debug="false">
    <contextName>logback</contextName>
    <property name="log.path" value="E:\\test\\logback.log" />
    
    <!--输出到控制台-->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
       <!-- 用ThresholdFilter来过滤掉ERROR级别以下的日志不输出到文件中
       <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>ERROR</level>
        </filter>
        -->
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
 
    <!--输出到文件-->
    <appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.path}</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--定义了日志的切分方式——把每一天的日志归档到一个文件中-->
            <fileNamePattern>logback.%d{yyyy-MM-dd}.log</fileNamePattern>
            <!--只保留最近30天的日志-->
             <maxHistory>30</maxHistory>
             <!--日记大小上限-->
             <totalSizeCap>1GB</totalSizeCap>
        </rollingPolicy>      
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    <!--root节点是必选节点，用来指定最基础的日志输出级别-->
    <root level="info">
        <appender-ref ref="console" />
        <appender-ref ref="file" />
    </root>
 
    <!-- <loger>用来设置某一个包或者具体的某一个类的日志打印级别、以及指定<appender>。 -->
    <logger name="com.dudu.controller"/>
    <logger name="com.dudu.controller.LearnController" level="WARN" additivity="false">
        <appender-ref ref="console"/>
    </logger>
</configuration>
```

2. 使用log4j
* 重新导入依赖
```xml
<!-- 排除springboot自带的logging 日志-->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
      <exclusions>
          <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
          </exclusion>
      </exclusions>
    </dependency>

    <!-- 重新导入log4j 
    spring-boot-starter-log4j 是 log4j+slf4j
    -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-log4j</artifactId>
      <version>1.3.8.RELEASE</version>
    </dependency>
```
* log4j.properties
```properties
log4j.rootLogger=INFO,Console,File
log4j.appender.Console=org.apache.log4j.ConsoleAppender
log4j.appender.Console.Target=System.out
log4j.appender.Console.layout =org.apache.log4j.PatternLayout
log4j.appender.Console.layout.ConversionPattern=[%p] [%d{yyyy-MM-dd HH\:mm\:ss}][%c - %L]%m%n

log4j.appender.File = org.apache.log4j.RollingFileAppender
log4j.appender.File.File = D:/logs/info/info.log
log4j.appender.File.MaxFileSize = 10MB
log4j.appender.File.Threshold = ALL
log4j.appender.File.layout = org.apache.log4j.PatternLayout
log4j.appender.File.layout.ConversionPattern =[%p] [%d{yyyy-MM-dd HH\:mm\:ss}][%c - %L]%m%n
```
* 使用
```java
private Logger logger = Logger.getLogger(getClass());
```



### 4. web方面
#### 访问静态资源
Spring Boot默认提供静态资源目录位置需置于**classpath**下（即target的根目录下,resource下），目录名需符合如下规则：
>/static
>/public
>/resources
>/META-INF/resources

#### 渲染web页面
默认的模板配置路径为：src/main/resources/templates，即html页面放在里面。
要访问模板页面必须用@controller

1. 使用非jsp模板
* 引入依赖
* controller里传递数据 
```java
public String list(Model model){
    model.addattribute("",对象);
    return "stu/list";  //页面
}
```
2. 使用jsp模板
* 在war包方式创建的项目中
* 引入依赖
```xml
<dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
</dependency>
```
* 添加配置
```propertirs
#视图配置
spring.mvc.view.prefix=/WEB-INF/view/    目录
spring.mvc.view.suffix=.jsp    后缀
```



### 5. 打包文件
一般情况下，我们用到的资源文件都放在src/main/resources下面，利用maven打包时，maven能把这些资源文件打包到相应的jar或者war里。 

maven认为src/main/java只是java的源代码路径。mybatis的mapper.xml文件，在src/main/java下面，这样利用maven打包时，这些文件不会被打包的。
* 需要修改pom文件。
```xml
<build>  
    <resources>  
        <resource>  
            <directory>src/main/resources</directory>  
            <includes>  
                <include>**/*.*</include>  
            </includes>  
        </resource>  
        
        <resource>  
            <directory>src/main/java</directory>  
            <includes>  
                <include>**/*.properties</include>  
                <include>**/*.xml</include>  
            </includes>  
        </resource>  
        
    </resources>  
    
   <plugins>
	<plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-compiler-plugin</artifactId>
		<configuration>
			<source>1.8</source>
			<target>1.8</target>
			<encoding>utf-8</encoding>
		</configuration>
	</plugin>
    
	<plugin>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-maven-plugin</artifactId>
		<configuration>
             <!--启动类的全路径-->
			<mainClass>com.gyf.App</mainClass>
		</configuration>
		<executions>
			<execution>
				<goals>
				    <goal>repackage</goal>
				</goals>
			</execution>
		</executions>
	</plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <version>2.6</version>
        <configuration>
            <!--<failOnMissingWebXml>false</failOnMissingWebXml>-->
        </configuration>
    </plugin>   
       
    </plugins>
    
</build>  
```
* 执行命令
```
mvn clean install

运行jar包
java -jar xxx.jar
```

