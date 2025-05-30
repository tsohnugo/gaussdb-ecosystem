# HikariCP连接GaussDB使用指南

HikariCP是一个高性能的数据库连接池实现。

官方开发指南参考：[https://github.com/brettwooldridge/HikariCP](https://github.com/brettwooldridge/HikariCP)

HikariCP是Spring技术体系的缺省连接池实现，官方文档参考: [https://docs.spring.io/spring-boot/3.3/reference/data/sql.html#data.sql.datasource.connection-pool](https://docs.spring.io/spring-boot/3.3/reference/data/sql.html#data.sql.datasource.connection-pool)

开发者一般很少需要直接使用HikariCP，而是通过使用Spring Data JDBC、Spring Data JPA等来间接使用HikariCP。 参考 [Spring Data JPA使用指南](../../SpringDataJPA/3.3.x/README.md) 了解GaussDB如何配置和使用HikariCP。 
