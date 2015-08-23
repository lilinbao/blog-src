title: "我的第一个RESTful服务"
date: 2015-07-30 22:02:51
alias: java/index.html
tags: 
- RESTful 
- 笔记
categories: Lab

---

本文讲述基于Java SE环境的Jersey官方文档中的示例simple-service,并在此基础上扩展自己定义的REST资源服务
##从Maven原型中创建项目
所谓原型项目是指通过简单的maven命令就可以从Maven中央仓库中取回一个已经具备基本功能、依赖完好、编译和测试运行无误的示例项目。`mvn archetype:generate`可以这样理解：以指定的原型为模板，生成或者创建新的Maven项目。
``` stdout
mvn archetype:generate -DarchetypeArtifactId=jersey-quickstart-grizzly2 \
-DarchetypeGroupId=org.glassfish.jersey.archetypes -DinteractiveMode=false \
-DgroupId=com.example -DartifactId=simple-service -Dpackage=com.example \
-DarchetypeVersion=2.19
```
##测试项目的可用性
运行完上面的Maven命令后，Maven会在当前目录下生成一个simple-service的文件夹。进入simple-service目录，运行Maven测试：
``` stdout
mvn clean test
```
第一次创建需要一点点时间给Maven下载项目所需要的依赖包，下载好之后再运行Maven测试就会看到以下输出
```stdout
Linbao@LENOVO-PC /g/labs/simple-service (master)
$ mvn clean test
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building simple-service 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ simple-service ---
[INFO] Deleting g:\labs\simple-service\target
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ simple-service ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory g:\labs\simple-service\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:2.5.1:compile (default-compile) @ simple-service ---
[INFO] Compiling 2 source files to g:\labs\simple-service\target\classes
[INFO]
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ simple-service ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory g:\labs\simple-service\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:2.5.1:testCompile (default-testCompile) @ simple-service ---
[INFO] Compiling 1 source file to g:\labs\simple-service\target\test-classes
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ simple-service ---
[INFO] Surefire report directory: g:\labs\simple-service\target\surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running com.example.MyResourceTest
七月 30, 2015 10:45:46 下午 org.glassfish.grizzly.http.server.NetworkListener start
INFO: Started listener bound to [localhost:8080]
七月 30, 2015 10:45:46 下午 org.glassfish.grizzly.http.server.HttpServer start
INFO: [HttpServer] Started.
七月 30, 2015 10:45:47 下午 org.glassfish.grizzly.http.server.NetworkListener shutdownNow
INFO: Stopped listener bound to [localhost:8080]
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.079 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 3.221 s
[INFO] Finished at: 2015-07-30T22:45:47+08:00
[INFO] Final Memory: 14M/217M
[INFO] ------------------------------------------------------------------------
```
项目默认是用JDK1.6编译，如果要改更高版本编译器，需要打开pom.xml文件，修改maven-compiler-plugin的参数
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.5.1</version>
    <inherited>true</inherited>
    <configuration>
        <source>1.7</source>
        <target>1.7</target>
    </configuration>
</plugin>
			
```

查看项目结构，在项目根目录下执行`mvn denpendency:tree`命令
```stdout
Linbao@LENOVO-PC /g/labs/simple-service (master)
$ mvn dependency:tree
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building simple-service 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-dependency-plugin:2.8:tree (default-cli) @ simple-service ---
[INFO] com.example:simple-service:jar:1.0-SNAPSHOT
[INFO] +- org.glassfish.jersey.containers:jersey-container-grizzly2-http:jar:2.19:compile
[INFO] |  +- org.glassfish.hk2.external:javax.inject:jar:2.4.0-b25:compile
[INFO] |  +- org.glassfish.grizzly:grizzly-http-server:jar:2.3.19:compile
[INFO] |  |  \- org.glassfish.grizzly:grizzly-http:jar:2.3.19:compile
[INFO] |  |     \- org.glassfish.grizzly:grizzly-framework:jar:2.3.19:compile
[INFO] |  +- org.glassfish.jersey.core:jersey-common:jar:2.19:compile
[INFO] |  |  +- javax.annotation:javax.annotation-api:jar:1.2:compile
[INFO] |  |  +- org.glassfish.jersey.bundles.repackaged:jersey-guava:jar:2.19:compile
[INFO] |  |  +- org.glassfish.hk2:hk2-api:jar:2.4.0-b25:compile
[INFO] |  |  |  +- org.glassfish.hk2:hk2-utils:jar:2.4.0-b25:compile
[INFO] |  |  |  \- org.glassfish.hk2.external:aopalliance-repackaged:jar:2.4.0-b25:compile
[INFO] |  |  +- org.glassfish.hk2:hk2-locator:jar:2.4.0-b25:compile
[INFO] |  |  |  \- org.javassist:javassist:jar:3.18.1-GA:compile
[INFO] |  |  \- org.glassfish.hk2:osgi-resource-locator:jar:1.0.1:compile
[INFO] |  +- org.glassfish.jersey.core:jersey-server:jar:2.19:compile
[INFO] |  |  +- org.glassfish.jersey.core:jersey-client:jar:2.19:compile
[INFO] |  |  +- org.glassfish.jersey.media:jersey-media-jaxb:jar:2.19:compile
[INFO] |  |  \- javax.validation:validation-api:jar:1.1.0.Final:compile
[INFO] |  \- javax.ws.rs:javax.ws.rs-api:jar:2.0.1:compile
[INFO] \- junit:junit:jar:4.9:test
[INFO]    \- org.hamcrest:hamcrest-core:jar:1.1:test
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 6.315 s
[INFO] Finished at: 2015-07-30T23:08:21+08:00
[INFO] Final Memory: 13M/153M
[INFO] ------------------------------------------------------------------------
```
没有出现错误，来看看怎么创建RESTful服务
##分析项目
执行以下命令`mvn eclipse:eclipse`生成exlipse项目文件，并将项目导入eclipse
```stdout
$ mvn eclipse:eclipse
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building simple-service 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] >>> maven-eclipse-plugin:2.10:eclipse (default-cli) > generate-resources @ simple-service >>>
[INFO]
[INFO] <<< maven-eclipse-plugin:2.10:eclipse (default-cli) < generate-resources @ simple-service <<<
[INFO]
[INFO] --- maven-eclipse-plugin:2.10:eclipse (default-cli) @ simple-service ---
[INFO] Using Eclipse Workspace: null
[INFO] Adding default classpath container: org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/JavaSE-1.7
[INFO] File g:\labs\simple-service\.project already exists.
       Additional settings will be preserved, run mvn eclipse:clean if you want old settings to be removed.
[INFO] Wrote Eclipse project for "simple-service" to g:\labs\simple-service.
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 2.641 s
[INFO] Finished at: 2015-07-30T23:42:49+08:00
[INFO] Final Memory: 12M/222M
[INFO] ------------------------------------------------------------------------
```
执行导入eclipse
导入后项目结构如下
![](http://7xksew.com1.z0.glb.clouddn.com/imagessimple-service-capture.PNG)
套用WEB开发中的典型三层模型，RESTful中的资源类位于分层中的最高层API层，它下面为Service层和DAO层
打开MyResource.java
```java
package com.example;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
/**
 * Root resource (exposed at "myresource" path)
 */
@Path("myresource") //基于JAX-RS标准，@Path声明myresource这个资源路径
public class MyResource {
    /**
     * Method handling HTTP GET requests. The returned object will be sent
     * to the client as "text/plain" media type.
     * @return String that will be returned as a text/plain response.
     */
    @GET //只接收GET方式的请求
    @Produces(MediaType.TEXT_PLAIN) //返回的数据类型
    public String getIt() {
        return "Got it!";
    }
}
```
国为这是一个Java SE 应用，所以就必需有一个入口来启动服务，所以有Main.java这个类来扮演这个容器
```java
package com.example;

import org.glassfish.grizzly.http.server.HttpServer;
import org.glassfish.jersey.grizzly2.httpserver.GrizzlyHttpServerFactory;
import org.glassfish.jersey.server.ResourceConfig;
import java.io.IOException;
import java.net.URI;

/**
 * Main class.
 *
 */
public class Main {
    // Base URI the Grizzly HTTP server will listen on
    public static final String BASE_URI = "http://localhost:8080/myapp/";
    /**
     * Starts Grizzly HTTP server exposing JAX-RS resources defined in this application.
     * @return Grizzly HTTP server.
     */
    public static HttpServer startServer() {
        // create a resource config that scans for JAX-RS resources and providers
        // in com.example package
        final ResourceConfig rc = new ResourceConfig().packages("com.example");

        // create and start a new instance of grizzly http server
        // exposing the Jersey application at BASE_URI
        return GrizzlyHttpServerFactory.createHttpServer(URI.create(BASE_URI), rc);
    }
    /**
     * Main method.
     * @param args
     * @throws IOException
     */
    public static void main(String[] args) throws IOException {
        final HttpServer server = startServer();
        System.out.println(String.format("Jersey app started with WADL available at "
                + "%sapplication.wadl\nHit enter to stop it...", BASE_URI));
        System.in.read();
        server.stop();
    }
}
```
测试类，只有一个testGetIt方法用来测试MyResource公布的接口
```java
package com.example;

import javax.ws.rs.client.Client;
import javax.ws.rs.client.ClientBuilder;
import javax.ws.rs.client.WebTarget;

import org.glassfish.grizzly.http.server.HttpServer;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import static org.junit.Assert.assertEquals;

public class MyResourceTest {

    private HttpServer server;
    private WebTarget target;

    @Before
    public void setUp() throws Exception {
        // start the server
        server = Main.startServer();
        // create the client
        Client c = ClientBuilder.newClient();

        // uncomment the following line if you want to enable
        // support for JSON in the client (you also have to uncomment
        // dependency on jersey-media-json module in pom.xml and Main.startServer())
        // --
        // c.configuration().enable(new org.glassfish.jersey.media.json.JsonJaxbFeature());

        target = c.target(Main.BASE_URI);
    }

    @After
    public void tearDown() throws Exception {
        server.stop();
    }

    /**
     * Test to see that the message "Got it!" is sent in the response.
     */
    @Test
    public void testGetIt() {
        String responseMsg = target.path("myresource").request().get(String.class);
        assertEquals("Got it!", responseMsg);
    }
}
```
好了，回到eclipse IDE,打开Main.java文件，运行run as -> java application

服务起来以后，打开浏览器输入`localhost:8080/myapp/myresource` 并回车查看结果

##扩展服务
上例只是一个实验，以这个为模式，让我们来建立一个我们更加熟悉的，即有service层，Dao层的三层服务

###添加POJO实体类
