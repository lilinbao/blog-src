title: "Summarize for Creating Webstore Project"
date: 2015-07-07 00:06:53
alias: java/index.html
tags: 笔记
categories: Project

---

webstore是本人自己在本地建立的一个项目，主要是用来做测试，目的是用来研究SSH框架的使用，以下是一点点我做这个项目的经验，留下以备不时之需
## Oracle
- **Oracle新建用户、角色，授权，建表空间**

	*创建用户*
	oracle内部有两个建好的用户：system和sys。用户可直接登录到system用户以创建其他用户，因为system具有创建别的用户的权限。 
	在安装oracle时，用户或系统管理员首先可以为自己建立一个用户：
	```sql
	-- for new user
	create user linbao identified by pwd;  
	-- for updating an exist user
	alter user linbao identified by 123;
	-- when user is logged in , use this command to alert his password
	password
	```
	其他可用用户：scott/tiger
	*删除用户*
	```sql
	drop user linbao
	-- 如果用户拥有对象，则不能直接删除，否则将返回一个错误值。
	--指定关键字cascade，可删除用户所有的对象，然后再删除用户。下面的例子用来删除用户与其对象：
　　drop user linbao cascade;
	```
	*Oracle中有3种标准角色*
	 `connect role`(连接角色)   临时用户，特别是那些不需要建表的用户

	`resource role`(资源角色)  提供给用户另外的权限以创建他们自己的表、序列、过程(procedure)、触发器(trigger)、索引(index)和簇(cluster)
	`dba role`(数据库管理员角色)  拥有所有的系统权限
	*grant(授权)命令*
	```sql
	grant connect, resource to linbao;
	```
	*revoke(撤消)权限*
	```sql
	revoke connect, resource from linbao;
	```
	*删除表的tip：*
	```sql
	 truncate table 表名  
	 ```
	 因为用drop table，delete * from 表名时，tablespace表空间该表的占用空间并未释放，反复几次drop，delete操作后，该tablespace上百兆的空间就被耗光了。
- **连接数据库**
	打开SQLPLUS，输入用户名密码 切换用户
	`as dba` conn /as dba;
	`as user` conn as username/password
- **level** 在oracle中是关键字，不能用作表字段**
- **在`oracle`中与`mysql`不相同的语句整合：**
> `mysql`  show tables; `oracle` select unique tname from col; 
> `oracle` 不支持的主键生成略：generationType.IDENTITY,但generationType.SEQUENCE支持
- **oracle中建立类似mysql的auto_increment递增字段 **
```sql
-- 1. 新建一张用户表
	create table tb_user (
        id number(10,0) not null,
        age number(10,0),
        email varchar2(255 char),
        isActive number(10,0),
        name varchar2(255 char),
        password varchar2(255 char),
        sex number(10,0),
        username varchar2(255 char),
        primary key (id)
    );
-- 2. 新建一个sequence序列
	CREATE SEQUENCE test_Sequence
 	INCREMENT BY 1   -- 每次加几个  
    START WITH 1     -- 从1开始计数  
    NOMAXVALUE       -- 不设置最大值  
    NOCYCLE ;         -- 一直累加，不循环
-- 3. 建立一个触发器
    create or replace trigger TEST_TRIGGER
    before insert
    on TB_USER
    for each row
    begin
    select TEST_TRIGGER.nextval into:NEW.id from dual;
    end;
    /

```
- **Oracle 连接url套拉字符串问题:**
我的本地连接配置如下：
```java
jdbc.driverClassName=oracle.jdbc.driver.OracleDriver
jdbc.url=jdbc:oracle:thin:@localhost:1521:DERRICK
jdbc.username=derrick
jdbc.password=root
```
	按网上后所说的， 
1. 端口号1521后的DERRICK是数据库名称,但这样说不完全对
2. 最后的DERRICK是 service_name，即这个service_name是在创建数据库时系统为其分配的SID名字，可能会与你设定的数据库名不同，需要自己去查看
`Windows` : window+r -> regedit -> HKEY_LOCAL_MACHINE\SOFTWARE\ORACLE\KEY_OraDb11g_home1/ORACLE_SID
`LINUX` : env
所以我的url为
`jdbc:oracle:thin:@localhost:1521:DERRICK`
- **Oracle 查看当前用户名下所有序列名**
```sql
select SEQUENCE_NAME from user_sequences;
```
- **查看某个sequence的当前值,假设sequence名字为test_seq**
```sql
select test_seq.currval from dual;
```
- **表管理**
```sql
select table_name from user_tables; --查询当前用户名下的所有表名
select table_name from all_tables; --查询数据库所有表名
desc table_name; --查看表结构
alter table (表名) add (列名 数据类型); --加列
alter table (表名) modify (列名 数据类型); -- 更改列
alter table (表名) rename column (当前列名) to (新列名); -- 更改列名
alter table (表名) drop column (列名); --删除列
alter table (当前表名) rename to (新表名);--更改表名
```
- **Oracle 清屏命令**
oracle清屏命令汇集：
1、同时按SHIFT和DELETE键，然后点OK即可清屏 。
2、若在window窗口下sqlplus 中清屏命令：host cls 或是clear screen 或只是4位clea scre。
3、若在dos的窗口下进入sql/plus就要用clear SCR。


- **Oracle的SQL集合**
{% blockquote sutku http://blog.csdn.net/sutku/article/details/7900876  Oracle的SQL语句大全 %}
 Oracle的SQL语句大全（摘）-- 复杂.
{% endblockquote %}

## Spring 4.x

### Spring声明式事务配置管理方法
- Spring可以用xml配置能配符来匹配需要事务的业务方法，基于约束先于配置的法则，比如方法开头以add,update,delete,get云云
- 也可以使用注解的方式为某些方法添加事务，两者选一样即可
xml配置如下 
![](http://7xiztk.com1.z0.glb.clouddn.com/201108281004069849.png)
{% blockquote irelandken的专栏 http://blog.csdn.net/irelandken/article/details/7193123 Spring @Transactional声明式事务管理  %}
Spring @Transactional声明式事务管理  getCurrentSession
{% endblockquote %}
## Hibernate 4.x
{% blockquote Red Hat, Inc. https://docs.jboss.org/hibernate/orm/4.0/javadocs/deprecated-list.html Deprecated API %}
Deprecated API List(Hibernate java doc ).
{% endblockquote %}

{% blockquote Blex http://www.360doc.com/content/11/0816/01/987036_140679068.shtml Hibernate Annotation API %}
Hibernate Annotation API
{% endblockquote %}
```java
@Entity
@Table(name="tb_user")
@SequenceGenerator(name="seqUser",sequenceName="seq_tb_user",allocationSize=1)
public class User implements Serializable{
	
	/**
	 * 
	 */
	private static final long serialVersionUID = 1L;
	@Id
	@GeneratedValue(strategy=GenerationType.SEQUENCE,generator="seqUser")
	private Integer id;
	private String name;
	private Integer age;
	private Integer sex;
	private String username;
	private String password;
	private String email;
	private Integer isActive;
	@ManyToMany
	@JoinTable(name="tb_role_user",
		joinColumns={@JoinColumn(name="user_id",referencedColumnName="id")},
		inverseJoinColumns={@JoinColumn(name="role_id",referencedColumnName="id")}
	)
	private Set<Role> roles = new HashSet<Role>(0);
}

	//getters and setters are ignored ...
```
```java
@Entity
@Table(name="tb_role")
@SequenceGenerator(name="seqRole",sequenceName="seq_tb_role",allocationSize=1)
public class Role implements Serializable{
	@Id
	@GeneratedValue(strategy=GenerationType.SEQUENCE,generator="seqRole")//seqRole要对应@SequenceGenerator中的name
	private Integer id;
	private int roleLevel;
	private String name;
	private String description;
	@ManyToMany
	@JoinTable(name="tb_role_resource",
		joinColumns={@JoinColumn(name="role_id",referencedColumnName="id")},
		inverseJoinColumns={@JoinColumn(name="res_id",referencedColumnName="id")}
	)
	private Set<Resources> resources = new HashSet<Resources>(0);

```
### hibernate 的id生成策略问题
- 若业务比较繁忙的表，建议一个表创建一个序列，如上所示
- 若查询修改不是很频繁的表可以多个表共享一个序列，配置类时不在id上加generator就可以
- `allocationSize=1`设定主键自增1

## Struts 2.x
### ActionForward
ActionForward是 Struts的核心类之一，其基类仅有4个属性`name / path / redirect / classname`。在基于Struts的Web应用程序开发过程中，Action操作完毕后程序会通过Struts的配置文件struts- config.xml链接到指定的ActionForward，传到Struts的核心类ActionServlet，ActionServlet使用 ActionForward提供的路径，将控制传递给下一个JSP或类。ActionForward控制接下来程序的走向。ActionForward代表一个应用的URI，它包括路径和参数，例如：`path=”/login.jsp”` 或`path=“/modify.do?method=edit&id=10”` ActionForward的参数除了在struts-config.xml和页面中设置外，还可以通过在Action类中添加参数，或重新在Action中创建一个ActionForward。
actionForward的作用：封装转发路径，通俗点说就是说完成页面的跳转和转向。那它既然是转向，到底是转发还是重定向呢？默认的情况下，actionForward采用的是转发的方式进行页面跳转的。
顺便说一下 转发和重定向的区别。最大的区别就是转发的时候，页面的url地址不变，而重定向的时候页面的url地址会发生变化。简单说明一下原因，因为转发的时候是采用的同一个request（请求），既然页面跳转前后是同一个request，页面url当然不会变了；而重定向采用的是2个request，因为是二次转发页面跳转前后的url当然会不同了。
既然actionForward跳转的方式默认的是转发，那如果我非要用重定向的方式，该如何设置呢？恩，这很简单，大家都在struts-config.xml做过actionForward的配置吧，比如这句     `<forward name=”login” path=”/login.jsp” redirect=”true”/> `
## Juint Test 4.x
### Junit Test 输出建表语句
```java
public class HibernateSchemeExportTest {

	public static LocalSessionFactoryBean localSessionFactory = null;
	public void test() {
		fail("Not yet implemented");
	}
	@BeforeClass
	public static void beforeClass(){
		ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
		localSessionFactory = (LocalSessionFactoryBean) ctx.getBean("&sessionFactory");
	}
	@Test
	public void testSchemaEcport(){
		
		Configuration conf = localSessionFactory.getConfiguration();
		new SchemaExport(conf).create(true,true);
	}
	@AfterClass
	public static void destroy(){
		localSessionFactory.destroy();
	}
}
```
## 其他
### maven中net.sf.json报错的解决方法：加编译器版本号
```xml
<dependency>
    <groupId>net.sf.json-lib</groupId>
    <artifactId>json-lib</artifactId>
    <version>2.4</version>
    <classifier>jdk15</classifier>
</dependency>
```
### struts2 输出 JSON
{% blockquote 杨胜寒 http://www.open-open.com/lib/view/open1325518231062.html Struts2返回JSON数据的具体应用范例  %}
深入了解Struts2返回JSON数据的原理及具体应用范例
{% endblockquote %}
### JVM调优
{% blockquote 神勇小白鼠-OSChina http://my.oschina.net/shootercn/blog/15393% JVM调优总结 %}
JVM调优总结
{% endblockquote %}
{% blockquote 360doc http://www.360doc.com/content/12/0113/08/1073512_179088229.shtml JVM 调优总结 %}
JVM 调优总结
{% endblockquote %}
