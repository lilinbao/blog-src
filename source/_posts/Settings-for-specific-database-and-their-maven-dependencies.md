title: "Settings for specific database and their maven dependencies"
date: 2015-07-04 23:03:21
alias: 
tags: 笔记
categories: Maven

---
## 各种数据库Hibernate链接配置
### Derby
**db driver maven dependency**
```xml
<dependency>
        <groupId>org.apache.derby</groupId>
        <artifactId>derbyclient</artifactId>
        <version>10.2.2.0</version>
</dependency>
```
**hibernate.properties**
``` java
hibernate.dialect=org.hibernate.dialect.DerbyDialect
hibernate.connection.driver_class=org.apache.derby.jdbc.ClientDriver
hibernate.connection.url=jdbc:derby://localhost/trails;create=true
hibernate.connection.username=any
hibernate.connection.password=value
hibernate.hbm2ddl.auto=update
```
### MySQL
**db driver maven dependency**
```xml
<dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.0.5</version>
</dependency>
```
**hibernate.properties**
``` java
hibernate.dialect=org.hibernate.dialect.MySQLInnoDBDialect
hibernate.connection.driver_class=com.mysql.jdbc.Driver
hibernate.connection.url=jdbc:mysql://localhost/trails?createDatabaseIfNotExist=true&amp;useUnicode=true&amp;characterEncoding=utf-8
hibernate.connection.username=root
hibernate.connection.password=
hibernate.hbm2ddl.auto=update
```
### H2
**db driver maven dependency**
```xml
<dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <version>1.0.20070304</version>
</dependency>
```
**hibernate.properties**
``` java
hibernate.dialect=org.hibernate.dialect.H2Dialect
hibernate.connection.driver_class=org.h2.Driver
hibernate.connection.url=jdbc:h2:trails
hibernate.connection.username=sa
hibernate.connection.password=
hibernate.hbm2ddl.auto=update
```
### Oracle
**db driver maven dependency**
```xml
<dependency>
        <groupId>com.oracle</groupId>
        <artifactId>ojdbc14</artifactId>
        <version>10.2.0.2.0</version>
</dependency>
```
**hibernate.properties**
``` java
hibernate.dialect=org.hibernate.dialect.Oracle9Dialect
hibernate.connection.driver_class=oracle.jdbc.OracleDriver
hibernate.connection.url=jdbc:oracle:thin:@localhost:1521:XE
hibernate.connection.username=system
hibernate.connection.password=system
hibernate.hbm2ddl.auto=update
 
# The Oracle JDBC driver doesn't like prepared statement caching very much.
hibernate.statement_cache.size=0
# or baching with BLOBs very much.
hibernate.jdbc.batch_size=0
 
# After a while, Oracle throws this exception: too many open cursors
# Disable PreparedStatement caching for the connection pool too.
# http://www.hibernate.org/120.html#A10
hibernate.dbcp.ps.maxIdle = 0
 
# Stoping hibernate from using the column-names in queries to retrieve data from the resultsets
# More info in http://www.jroller.com/page/dashorst?entry=hibernate_3_1_something_performance1
hibernate.jdbc.wrap_result_sets=true
```
### PostgreSQL
**db driver maven dependency**
```xml
<dependency>
        <groupId>postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <version>8.2-504.jdbc3</version>
</dependency>
```
**hibernate.properties**
``` java
hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
hibernate.connection.driver_class=org.postgresql.Driver
hibernate.connection.url=jdbc:postgresql://localhost/trails
hibernate.connection.username=postgres
hibernate.connection.password=postgres
hibernate.hbm2ddl.auto=update
```
### Microsoft SQL Server
**db driver maven dependency**
```xml
<dependency>
        <groupId>net.sourceforge.jtds</groupId>
        <artifactId>jtds</artifactId>
        <version>1.2</version>
</dependency>
```
**hibernate.properties**
``` java
hibernate.dialect=org.hibernate.dialect.SQLServerDialect
hibernate.connection.driver_class=net.sourceforge.jtds.jdbc.Driver
hibernate.connection.url=jdbc:jtds:sqlserver://localhost:1433/trails
hibernate.connection.username=sa
hibernate.connection.password=
hibernate.hbm2ddl.auto=update
```
### HSQLDB 
**db driver maven dependency**
```xml
<dependency>
        <groupId>hsqldb</groupId>
        <artifactId>hsqldb</artifactId>
        <version>1.8.0.7</version>
</dependency>
```
**hibernate.properties**
``` java
hibernate.dialect=org.hibernate.dialect.HSQLDialect
hibernate.connection.driver_class=org.hsqldb.jdbcDriver
hibernate.connection.url=jdbc:hsqldb:trails;shutdown=true
hibernate.connection.username=sa
hibernate.connection.password=
hibernate.hbm2ddl.auto=update
```