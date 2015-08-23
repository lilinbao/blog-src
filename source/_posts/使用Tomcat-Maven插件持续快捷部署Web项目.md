title: "使用Tomcat Maven插件持续快捷部署Web项目"
date: 2015-07-12 14:21:46
alias: 
tags: 笔记
categories: Maven

---
支持中文标题。。。这货

到目前为止用maven还是蛮爽的，今天一直在测试项目，不断改不断发布不断重启也是醉了。。。所以上网找了一此资料，别说这个自动发布插件还不错，基本能满足需求，先记下来
##准备工作
1. 下载并安装tomcat,这里我选的是tomcat 7.0.62;
2. 安装好的maven,并配置好能用
3. 准备一个Maven Web项目

网上关于怎么配置怎么建立maven web项目的资料很多，可以参考，建议多看几篇
##Maven部署Web项目到Tomcat的配置
分别对tomat的tomcat-uses.xml和maven项目的pom.xml进行修改
###配置Tomcat角色，用户
```xml
<!-- tomcat-users.xml -->
<tomcat-users>
    <role rolename="admin-gui"/>
    <role rolename="admin-script"/>
    <role rolename="manager-gui"/>
    <role rolename="manager-script"/>
    <role rolename="manager-jmx"/>
    <role rolename="manager-status"/>
    <user username="admin" password="admin" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-script,admin-gui"/>
    <user username="maven" password="root" roles="manager-script"/>
</tomcat-users>
```
看到user这个标签，分配给maven的只要manager-script就行
### Maven配置
1. 打包插件配置
```xml
<build>
	<plugins>
		<!-- compiler插件, 设定JDK版本 -->
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-compiler-plugin</artifactId>
			<version>3.1</version>
			<configuration>
				<source>${java.version}</source>
				<target>${java.version}</target>
				<showWarnings>true</showWarnings>
			</configuration>
		</plugin>
		<!-- war打包插件, 设定war包名称带版本号 -->
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-war-plugin</artifactId>
			<version>2.4</version>
			<configuration>
				<warName>${project.artifactId}-${project.version}</warName>
			</configuration>
		</plugin>
	</plugins>
</build> 
```
2. 添加maven-tomcat插件依赖
```xml
<!-- tomcat 7 -->
<dependency>
	<groupId>org.apache.tomcat.maven</groupId>
	<artifactId>tomcat7-maven-plugin</artifactId>
	<version>2.2</version>
</dependency>
```
{% blockquote Apache http://mvnrepository.com/artifact/org.apache.tomcat.maven/tomcat7-maven-plugin  Apache Tomcat Maven Plugin :: Tomcat 7.x %}
 Apache Tomcat Maven Plugin :: Tomcat 7.x.
{% endblockquote %}
3. 配置maven与tomcat连通
3.1修改maven~/.m2目录下settings.xml(如没有则新建)
在<settings>节点下新增<servers>
```xml
<settings>
	<servers>
		<server>
			<id>tomcat7</id>
			<username>maven</username>
			<password>root</password>
		</server>
	</servers>
</settings>
```
在<build> <plugins>节点下新增plugin
```xml
<plugin>
	<groupId>org.apache.tomcat.maven</groupId>
	<artifactId>tomcat7-maven-plugin</artifactId>
	<version>2.2</version>
	<configuration>
		<url>http://localhost:8080/manager/text</url>
		<!-- server, username, password对应tomcat的tomcat-users.xml或maven中settings.xml下的配置 -->
		<server>tomcat7</server>
		<username>maven</username>
		<password>root</password>
		<!-- war文件路缺省情况下指向target -->
		<!--<warFile>${basedir}/target/${project.build.finalName}.war</warFile>-->
	</configuration>
</plugin>
```
###布署项目到tomcat
先确保Tomcat服务器已经启动，然后cd到项目根目录，运行下面的命令：
```maven
mvn clean install tomcat7:deploy
```
如果项目已经成功布署过，则运行下面这条命令
```maven
mvn clean install tomcat7:redeploy
```
然后就可以看到maven一点一点帮你做这些事情了，第一次发布会久一点
{% blockquote %}
[INFO]
[INFO] <<< tomcat7-maven-plugin:2.2:redeploy (default-cli) < package @ webstore <<<
[INFO]
[INFO] --- tomcat7-maven-plugin:2.2:redeploy (default-cli) @ webstore ---
[INFO] Deploying war to http://localhost:8080/webstore
Uploading: http://localhost:8080/manager/text/deploy?path=%2Fwebstore&update=true
Uploaded: http://localhost:8080/manager/text/deploy?path=%2Fwebstore&update=true (40122 KB at 17241.5 KB/sec)

[INFO] tomcatManager status code:200, ReasonPhrase:OK
[INFO] OK - Deployed application at context path /webstore
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 16.893 s
[INFO] Finished at: 2015-07-12T14:52:52+08:00
[INFO] Final Memory: 32M/355M
[INFO] ------------------------------------------------------------------------
{% endblockquote %}
然后我们就能在Tomcat安装目录的webapps目录下找到新部署的WAR包
`注意:`初次部署用的是tomcat7:deploy命令，重新部署可以用tomcat7:redeploy命令（推荐统一用这个），Tomcat Maven插件支持的命令包括：run、shutdown、run-war-only、exec-war、standalone-war-only、deploy、standalone-war、undeploy、run-war、redeploy等。
##遇到过的问题
第一次用到不是很顺利，遇到很多问题，好在有万能的google和度娘帮解决：
1. 包的路径问题
war打包插件, 有些时候可能会设定war包名称带版本号，有些时候不会，注意，当你不带版本号的时候tomcat-maven-plugin可能会报包找不到的情况

```log
[ERROR] Failed to execute goal org.apache.tomcat.maven:tomcat7-maven-plugin:2.2:deploy (default-cli) on project webstore:
```
这时就需要在tomcat7-maven-plugin中指定这个包的生成路径
```xml
<warFile>${basedir}/target/${project.build.finalName}.war</warFile>
```
2. Connection refused
一步步分析：
首先，有没有启动tomcat服务
其次，使用的命令对不对deploy和redeploy的区别
帐号与密码，权限检查一遍
3. socket write error

```log
[ERROR] Failed to execute goal org.apache.tomcat.maven:tomcat7-maven-plugin:2.2:deploy (default-cli) on project webstore: Cannot invoke Tomcat manager: Software caused connection abort: socket write error -> [Help 1]
```
每次上传到一半总是自动停止。。。原因就是因为项目已经发布过但还是用deploy，应该用redeploy
4. 401错误

```log
[ERROR] Failed to execute goal org.apache.tomcat.maven:tomcat7-maven-plugin: 2.0-SNAPSHOT:deploy (default-cli) on project webstore: Cannot invoke Tomcat manager: Server returned HTTP response code: 401 for URL: http://localhost:8080/manager/text/deploy?path=%2Fwebstore -> [Help 1]
```
原因是权限问题，检查一下tomcat的tomcat-users.xml有没有给maven用户分配manager-script权限
5. 403错误

```log
[ERROR] Failed to execute goal org.apache.tomcat.maven:tomcat7-maven-plugin: 2.0-SNAPSHOT:deploy (default-cli) on project webstore: Cannot invoke Tomcat manager: Server returned HTTP response code: 403 for URL: http://localhost:8080/manager/html/deploy?path=%2Fwebstore -> [Help 1]
```
原因是tomcat不同版本所使用的上传路径不同，tomcat7以前用
```url
<url>http://localhost:8080/manager/html</url>
```
tomcat7以上用
```url
<url>http://localhost:8080/manager/text</url>
```
如还是不行，给maven用户增加manager-gui角色
6. Windows系统下，redeploy过程无法删除旧项目的目录
报错现象是，所有的配置都正确，已经到了上传那一步，但上传到2054K的时候就断开，打开maven的DEBUG（mvn tomcat7:redeploy -X）信息说是

```log 
Cannot invoke Tomcat manager : Software caused connection abort: socket write error
```
解决办法是修改tomcat的TOMCAT_HOME/conf/context.xml, 在<Context>节点下加上
```xml
<Context antiJARLocking="true" antiResourceLocking="true">
```
##写在最后

{% blockquote 摘自:优雅先生 http://my.oschina.net/feichexia/blog/326893 开发过程使用Tomcat Maven插件持续快捷部署Web项目%}
使用这种方式布署的局限性
{%endblockquote%}

 这种方案能够实现持续快捷部署。但它有一些局限性：
 - 要求从本地开发环境能直接访问Tomcat服务器所在网段
 - 不能保留历史部署包
 - 因此初步建议只在开发环境使用这种部署方式，并且结合SVN、Git等版本控制软件做两个内部约定：
   所有可部署版本代码都必须先签入一个名为deploy-xx的分支，xx表示当前可部署版本，deploy分支代码必须保证是可以部署的代码，然后切到deploy-xx分支再部署项目，以后增加了新功能，则需新建另一个deploy分支，并增大版本号。这样可以利用版本控制软件帮我们保留各个历史可部署代码（解决了上面提到的第二个局限性）。尤其是多个项目集成时，最好保证每一次集成时各个项目的deploy分支带的版本后缀相同。这样可以方便各个项目代码集体回滚 
