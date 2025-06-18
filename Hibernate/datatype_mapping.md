# Java 类型 ↔ Hibernate 类型 ↔ 数据库 SQL 类型

下面是一个 **Java 类型 ↔ Hibernate 类型 ↔ GaussDB SQL 类型** 的对应表，涵盖了基本的类型映射关系（基于 Hibernate ORM 7 和 GaussDB）：

---

| Java 类型                                          | Hibernate 类型                                          | GaussDB SQL 类型                   |
|--------------------------------------------------|-------------------------------------------------------|----------------------------------|
| `RowId`                                          | `org.hibernate.type.SqlTypes#ROWID`                   | `rowid`                          |
| `java.lang.Boolean` / `boolean`                  | `org.hibernate.type.SqlTypes#BOOLEAN`                 | `boolean`                        |
| `java.lang.Byte` / `byte`                        | `org.hibernate.type.SqlTypes#TINYINT`                 | `smallint` (GaussDB 无 TINYINT)   |
| `java.lang.Short` / `short`                      | `org.hibernate.type.SqlTypes#SMALLINT`                | `smallint`                       |
| `java.lang.Integer` / `int`                      | `org.hibernate.type.SqlTypes#INTEGER`                 | `integer`                        |
| `java.lang.Long` / `long`                        | `org.hibernate.type.SqlTypes#BIGINT`                  | `bigint`                         |
| `java.lang.Float` / `float`                      | `org.hibernate.type.SqlTypes#FLOAT`                   | `float($p)`                      |
| `java.lang.Float`                                | `org.hibernate.type.SqlTypes#REAL`                    | `real`                           |
| `java.lang.Double` / `double`                    | `org.hibernate.type.SqlTypes#DOUBLE`                  | `double precision`               |
| `java.math.BigInteger`                           | `org.hibernate.type.SqlTypes#NUMERIC`                 | `numeric($p,$s)`                 |
| `java.math.BigDecimal`                           | `org.hibernate.type.SqlTypes#DECIMAL`                 | `decimal($p,$s)`                 |
| `java.sql.Date` / `java.time.LocalDate`          | `org.hibernate.type.SqlTypes#DATE`                    | `date`                           |
| `java.sql.Time`                                  | `org.hibernate.type.SqlTypes#TIME`                    | `time($p)`                       |
| `java.time.OffsetTime`                           | `org.hibernate.type.SqlTypes#TIME_WITH_TIMEZONE`      | `time($p) with time zone`        |
| `java.sql.Timestamp` / `java.time.LocalDateTime` | `org.hibernate.type.SqlTypes#TIMESTAMP`               | `timestamp($p)`                  |
| `java.time.OffsetDateTime`                       | `org.hibernate.type.SqlTypes#TIMESTAMP_WITH_TIMEZONE` | `timestamp($p) with time zone`   |
| `java.time.OffsetTime`                           | `org.hibernate.type.SqlTypes#TIME_UTC`                | `time($p) with time zone`        |
| `java.time.Instant`                              | `org.hibernate.type.SqlTypes#TIMESTAMP_UTC`           | `timestamp($p) with time zone`   |
| `java.lang.Character`                            | `org.hibernate.type.SqlTypes#CHAR`                    | `char($l)`                       |
| `java.lang.String`                               | `org.hibernate.type.SqlTypes#VARCHAR`                 | `varchar($l)`                    |
| `java.sql.Clob`                                  | `org.hibernate.type.SqlTypes#CLOB`                    | `clob`                           |
| `java.lang.String` (NCHAR)                       | `org.hibernate.type.SqlTypes#NCHAR`                   | `char($l)`                       |
| `java.lang.String` (NVARCHAR)                    | `org.hibernate.type.SqlTypes#NVARCHAR`                | `varchar($l)`                    |
| `java.sql.Blob`                                  | `org.hibernate.type.SqlTypes#BLOB`                    | `clob` (GaussDB 中 BLOB 实际用 CLOB) |
| `java.lang.String` (长文本)                         | `org.hibernate.type.SqlTypes#LONG32VARCHAR`           | `text`                           |
| `java.lang.String` (Unicode长文本)                  | `org.hibernate.type.SqlTypes#LONG32NVARCHAR`          | `text`                           |
| `byte[]` (二进制长数据)                                | `org.hibernate.type.SqlTypes#LONG32VARBINARY`         | `bytea`                          |
| `byte[]` (定长二进制)                                 | `org.hibernate.type.SqlTypes#BINARY`                  | `bytea`                          |
| `byte[]` (变长二进制)                                 | `org.hibernate.type.SqlTypes#VARBINARY`               | `bytea`                          |

---

### 关键说明：
1. **时间类型**：
    - Java 8+ 的 `Instant` 默认映射到带时区的 `timestamp`
    - 传统 `java.sql.Timestamp` 映射到无时区版本

2. **二进制类型**：
    - GaussDB 用 `bytea` 处理所有二进制数据，`BLOB` 实际存储为 `CLOB`

3. **精度控制**：
    - `$p` 表示精度参数（如 `timestamp(6)`）
    - `$l` 表示长度参数（如 `varchar(255)`）
    - `$s` 表示标度（小数位数）

4. **特殊映射**：
    - `TINYINT` 被映射到 `smallint`（因 GaussDB 无原生 TINYINT）
    - 长文本推荐使用 `text` 类型而非 `CLOB`