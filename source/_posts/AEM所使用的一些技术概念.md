title: "AEM所使用的一些技术概念"
date: 2015-10-09 21:17:58
alias: 
tags:
 - 语言学习
 - OSGi
 - 笔记
categories: 总结与分享

---
##前言
以下是自已在火车上看书看文档理解的一些关于AEM的概念

##OSGi一些注释的含义

### 首先是类级别的注释
`@Component`唯一一个必须注释，允许OSGi定义和描述以注册你的组件到OSGi容器中，将会定义<component>以及<implementation>将会被自动创建

`@Service`定义了这个组件是否提供或提供什么类型的服务

`@Property`定义了类级别的属性，用于定义属性的初始值，这些属性可以通过web console修改并通过ComponentContext.getProperties()得到

###其次是类成员的注释
`@Reference`定义了对其他服务的引用，这些被消费的引用将会在运行期间进行绑定，这也可以在类上面进行注释

###最后是方法的注释

`@Activate` `@Deactivate` `@Modified`
这三个注释定义了在对类进行初始化，注销和修改状态时所要进行的动作

注意注意：OSGi都是面向接口操作，所以所有的OSGi组件都要有对应的接口！！！

##Apache sling
Apache sling是一个web框架，它使用类似于apache jackrabbit 或者Adobe CRX来存储和管理内容
它有一个默认的http get和http post Servlet来处理请求，可以直接访问

如果是post操作，并且所访问的内容不存在，则创建节点，如果存在则修改
这些操作都是基于http method进行的

 - GET 查询
 - POST 添加/修改
 - DELETE 删除

##适配器模式
一个适配器帮助两个相互兼容的接口来共同工作，适配器模式是用来让两个各自实现了能够相互兼容的接口的类来共同工作。sling提供了适配器模式来更方便地把同时实现了Adapter接口的两个类进行适配。这两个类都有adaptTo(Target.class)方法，以便把当前的类翻译成目标类

比如说我想把Resource适配成Node
```java

Node node = resource.adaptTo(Node.class)
```
这将会把当前的资源映射成一个新的node对象使用
Resource还可以适配成其他11种类型，常用的有`Node`,`ValueMap`,`Map`,`Property`,`Item`,`InputStream`,`URL`,`File`,`Servlet`

而`Resource Resolver`可以适配成其他17类，常用的有`Session`,`PageManager`,`ComponentManager`,`Designer`,`Assets Manager`,`TagManager`,`UserManager`,`User`,`Authorizable`

注意：如果想要容器帮你注入(使用@Inject,@Self,@Property)则这些属性必须是用public修饰
