本项目收集GaussDB开源软件生态的总体信息，包括支持的开源软件、版本以及存在的缺陷等。

# 开源软件支持

| 分类  | 开源软件  | 开源软件基线版本  | GaussDB内核基线版本  | 驱动基线版本  |  使用指南 |
| :------------ | :------------ | :------------ | :------------ | :------------ | :------------ |
| ORM  | MyBatis |   |  505.2.0  | 506.0.0  |   |
| ORM  | MyBatisPlus |   |  505.2.0  | 506.0.0  |   |
| ORM  | Hibernate |   |  505.2.0  | 506.0.0  |   |
| ORM  | Spring Data JPA |   |  505.2.0  | 506.0.0  |   |
| ORM  | Spring Data JDBC |   |  505.2.0  | 506.0.0  |   |
| ORM  | Spring Data R2DBC |   |  505.2.0  | 506.0.0  |   |
| 连接池  | HikariCP  |   | 505.2.0  | 506.0.0  |   |
| 连接池  | Druid  |   | 505.2.0  | 506.0.0  |   |
| 数据库工具  | Dbeaver  |   | 505.2.0  | 506.0.0 | [Dbeaver 连接 GaussDB 使用指南](./dbeaver/25/database-driver-gaussdb.md)  |
| 大数据软件  |  Flink |   | 505.2.0  | 506.0.0 |   |
| 大数据软件  |  FlinkCDC |   | 505.2.0  | 506.0.0 |   |

# JDBC开源驱动

* maven

```xml
<dependencies>
  <dependency>
    <groupId>com.huaweicloud.gaussdb</groupId>
    <artifactId>gaussdbjdbc</artifactId>
    <version>506.0.0.b058</version>
  </dependency>
</dependencies>
```

* gradle

```gradle
dependencies {
    implementation 'com.huaweicloud.gaussdb:gaussdbjdbc:506.0.0.b058'
}  
```

