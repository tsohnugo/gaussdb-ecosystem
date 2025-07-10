<p align="center">
  <p align="center">
    <a href="README.md"><strong>简体中文</strong></a> | <strong>English</strong>
  </p>

# Druid Connection Guide for GaussDB

Druid is a JDBC component library that includes a database connection pool, SQL Parser, and other utilities.

Official Developer Guide:[https://github.com/alibaba/druid/wiki/](https://github.com/alibaba/druid/wiki/)

Developers typically integrate Druid via Spring. Official documentation: [https://docs.spring.io/spring-boot/3.3/reference/data/sql.html#data.sql.datasource.connection-pool](https://docs.spring.io/spring-boot/3.3/reference/data/sql.html#data.sql.datasource.connection-pool)。

This document focuses on using Druid with Spring Data JPA.

> Important Notes：Druid's security features (WallFilter) and monitoring (StatFilter) rely on its AST (Abstract Syntax Tree) component, which acts as a lightweight SQL parser. This connection pool currently does not support GaussDB. If developers use Druid to access GaussDB databases, it is recommended to disable these two features to avoid unpredictable issues. Alternatively, adopt the HikariCP connection pool that comes built-in with Spring Data JPA by default.


## Technical Components

### JDBC Driver

Use GaussDB's official driver.

  * Connection string:`jdbc:gaussdb://host:port/database`

  * Driver class:`com.huawei.gaussdb.jdbc.Driver`

  * maven

    ```xml
    <dependency>
        <groupId>com.huaweicloud.gaussdb</groupId>
        <artifactId>gaussdbjdbc</artifactId>
        <version>506.0.0.b058</version>
    </dependency>
    ```

### Spring Data JPA

Spring Data JPA enables straightforward integration with Spring Boot through dependency inclusion. Note that it defaults to using **HikariCP as the connection pool**, requiring you to exclude this dependency when configuring alternative pools.  

  * maven

    ```xml
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
        <exclusions>
          <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
          </exclusion>
          <exclusion>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
          </exclusion>
        </exclusions>
        <version>3.3.12</version>
      </dependency>
    ```

### Hibernate

Spring Data JPA depends on Hibernate by default. This example uses Hibernate version 6.6.15.Final:

  * maven

    ```xml
      <dependency>
        <groupId>org.hibernate.orm</groupId>
        <artifactId>hibernate-core</artifactId>
        <version>6.6.15.Final</version>
        <scope>compile</scope>
      </dependency>
    ```

Additionally, the GaussDB-provided Hibernate Dialect implementation is required:

    ```xml
      <dependency>
        <groupId>com.huaweicloud.gaussdb</groupId>
        <artifactId>gaussdb-hibernate-dialect</artifactId>
        <version>6.0.0</version>
        <scope>compile</scope>
      </dependency>
    ```

### Druid

  * maven

    ```xml
      <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-3-starter</artifactId>
        <version>1.2.23</version>
      </dependency>
    ```

## Connecting to GaussDB: Usage Guide 

Our development example implements user authentication and authorization using Spring Data JPA with GaussDB. Follow the `Hands-on Lab` section to execute this sample.

* Add Dependencies

  Before starting, include the required software packages as described in the `Relevant Technical Components` section。

* Configure Data Source

  Spring Data JPA uses Spring DataSource for connection configuration. Add the following to your application.yml:

  ```yml
    spring:
        datasource:
            url: ${DB_URL:jdbc:gaussdb://127.0.0.1:8000/postgres?currentSchema=authentication_server_db}
            username: ${DB_USERNAME:GaussdbExamples}
            password: ${DB_PASSWORD:Gaussdb-Examples-123}
            driver-class-name: com.huawei.gaussdb.jdbc.Driver
        jpa:
            hibernate:
                ddl-auto: none
  ```
  
This example uses manual table creation via scripts, hence setting hibernate.ddl-auto to none.

* Configure Spring Data JPA

   ```java
    @Configuration
    @EnableJpaRepositories
    @EnableTransactionManagement
    public class SpringDataJPAConfig {

    }
   ```


* Configure Repository

The foundational concept in Spring Data JPA is the Repository, which represents an aggregate root in Domain-Driven Design (DDD). See the UserRepository example below:

  ```java
    public interface UserRepository extends JpaRepository<User, String> {
        @Query("select u from User u where userName = ?1")
        User selectUserByUsername(String userName);
    }
  ```

* Working with Repositories

Repositories can be utilized like any regular Spring-managed bean.

  ```java
  @Component(CommonConstants.BEAN_AUTH_USER_DETAILS_SERVICE)
    public class JDBCUserDetailsManager implements UserDetailsManager {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private RoleRepository roleRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User info = userRepository.selectUserByUsername(username);
        if (info == null) {
        throw new UsernameNotFoundException("");
        }

        return new JDBCUserDetails(info, roleRepository.selectRolesByUsername(username));
    }

    ...

    }
  ```

* Notes

  The GaussDB Dialect adheres to the following rules when generating database identifiers (table names, column names, etc.): lowercase letters are used, and double quotes are applied. Therefore, when using custom queries or creating tables, you must ensure these constraints are met. For more details, refer to: [Question Regarding Identifier Quoting in GaussDB](https://bbs.huaweicloud.com/forum/thread-0254182512348607062-1-1.html) .

## Hands-on Lab

[gaussdb-examples](https://github.com/HuaweiCloudDeveloper/gaussdb-examples) provides examples based on ServiceComb Fence. 

* Download the Code

  ```shell
  git clone https://github.com/HuaweiCloudDeveloper/gaussdb-examples.git
  git checkout -B Druid/1.2.x origin/Druid/1.2.x
  ```

Before running the examples, you need to install Zookeeper and GaussDB. Individual developers can install Zookeeper and OpenGauss via open-source mirrors.

* docker install OpenGauss

  ```shell
  docker run --name opengauss --privileged=true -d -e GS_USERNAME=GaussdbExamples -e GS_PASSWORD=Gaussdb-Examples-123 -e GS_PORT=8000 -p 8000:8000 opengauss/opengauss:7.0.0-RC1.B023
  ```

  
* docker install Zookeeper 

  ```shell
  docker run --name zookeeper --restart always -d -p 2181:2181 zookeeper:3.9.3
  ```

* Create Tables

  Refer to the [Table Creation Script](https://github.com/HuaweiCloudDeveloper/gaussdb-examples/tree/Druid/1.2.x/authentication-server/src/main/resources/sql/user.sql)，You can use database tools like DBeaver to connect and execute the relevant SQL statements.
  
* Starting and Running

  Refer to the introduction in [Java Chassis 3最佳实践（一）：Fence项目介绍](https://bbs.huaweicloud.com/blogs/433423) 或者[Open Source for Huawei Wiki](https://gitcode.com/HuaweiCloudDeveloper/OpenSourceForHuaweiWiki) to run the examples.
