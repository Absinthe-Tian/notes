## 知识点

### 操作对象

* 库
* 表
* 列/字段
* 行/记录
* 索引
* 视图
* 存储过程
* 函数
* 触发器
* 用户及权限



### 操作语言

* 数据定义语言**DDL**（Data Definition Language）：用于定义数据库对象（如数据库、表、索引等）的结构和关系。
* 数据查询语言**DQL**（Data Query Language）：主要用于查询数据库中的数据，DQL 的核心是 `SELECT` 语句。
* 数据操作语言**DML**（Data Manipulation Language）：用于对数据库表中的数据进行增删改操作。
* 数据控制语言**DCL**（Data Control Language）：用于定义数据库的访问权限和安全级别，管理用户权限。
* 事务控制语言**TCL**（Transaction Control Language）：用于管理数据库事务，确保数据的完整性和一致性。

注意：SQL语句以`;`结束，不区分大小写。



### 存储引擎

- **InnoDB** 更适合需要高并发、事务支持和外键约束的场景。
- **MyISAM** 更适合以读为主、对事务要求不高的场景，比如数据仓库和分析系统。



InnoDB特点：

* **支持事务**：InnoDB 是一个支持ACID事务的引擎，通过 `COMMIT`、`ROLLBACK` 和 `SAVEPOINT` 操作来确保数据完整性。
* **外键约束**：支持外键，能够维护表之间的引用完整性，防止无效的数据插入。
* **行级锁**：InnoDB 支持行级锁，可以更好地处理高并发事务。相比于表级锁，它的并发性能更高。
* **崩溃恢复**：通过日志文件和检查点进行崩溃恢复，保证数据在意外情况下的恢复能力。
* **聚簇索引**：InnoDB 使用聚簇索引，将数据和主键一起存储，因此查询主键时性能较高。

场景：

* **需要事务支持的场景**：如银行、财务等对数据完整性要求高的系统。
* **高并发读写的应用**：行级锁适合高并发写操作，比如电商网站、社交网络等。
* **需要外键约束的数据库设计**：InnoDB 提供的外键约束功能可以自动维护数据的一致性。



MyISAM特点：

* **不支持事务**：MyISAM 不支持事务，数据完整性只能通过应用程序控制。
* **表级锁**：MyISAM 采用表级锁，每次操作会锁定整个表，因此在高并发写入时性能较差。
* **查询速度较快**：MyISAM 的读性能较好，特别适合以查询为主的应用。
* **存储空间较小**：MyISAM 索引和数据分开存储，且不支持事务日志，因此占用的磁盘空间通常较小。
* **全文索引**：MyISAM 支持全文索引。

场景：

* **查询密集型应用**：如数据仓库、数据分析系统等场景，写操作较少、查询操作较多。
* **不需要事务控制的应用**：如一些简单的数据展示平台。
* **需要更少存储空间的场景**：MyISAM 占用存储空间相对较少，适合数据量较大的历史数据存储。



### 事务

事务是在数据库中执行的一系列操作单元，这些操作要么都成功提交，要么都失败回滚。



特点：

* 原子性（Atomicity）：事务中的所有操作要么全部成功，要么全部失败。
* 一致性（Consistency）：事务执行前后，数据库的状态必须保持一致，即事务必须将数据库从一个一致性状态带到另一个一致性状态。
* 隔离性（Isolation）：事务的执行不会受到其他事务的干扰。
* 持久性（Durability）：一旦事务成功提交（`COMMIT`），它对数据库的修改就是永久性的，不会因为系统崩溃或其他故障而丢失。



事务隔离级别：

* READ UNCOMMITTED（读未提交）：事务可以读取未提交事务的数据。
* READ COMMITTED（读已提交）：事务只能读取已经提交的事务的数据。
* REPEATABLE READ（可重复读）：事务在执行过程中，所有读取的数据都保持一致，不会被其他事务修改。
* SERIALIZABLE（可串行化）：事务完全隔离，每个事务都仿佛是串行执行的，其他事务无法读取、修改或插入正在进行的事务的相关数据。



| 隔离级别             | 脏读   | 不可重复读 | 幻读   | 性能 |
| -------------------- | ------ | ---------- | ------ | ---- |
| **READ UNCOMMITTED** | 允许   | 允许       | 允许   | 最好 |
| **READ COMMITTED**   | 不允许 | 允许       | 允许   | 较好 |
| **REPEATABLE READ**  | 不允许 | 不允许     | 允许   | 中等 |
| **SERIALIZABLE**     | 不允许 | 不允许     | 不允许 | 最差 |



常见的并发事务异常：

* 脏读（Dirty Read）：事务 A 在未提交的情况下读取了事务 B 的数据。若事务 B 最终回滚，事务 A 就读取到了无效数据，即事务 A 读取的数据可能不再有效或与最终提交的数据不一致。
* 不可重复读（Non-repeatable Read）：事务 A 在执行两次相同的查询时，查询结果发生了变化。换句话说，在事务 A 执行期间，其他事务修改了事务 A 正在查询的数据。
* 幻读（Phantom Read）：事务 A 在执行查询时，另一个事务 B 插入、删除或更新了使得事务 A 查询结果集发生变化的数据。事务 A 在执行两次相同查询时，查询的结果集发生了变化，虽然事务 A 查询的条件没有变化。
* 锁竞争（Lock Contention）：多个事务尝试同时访问同一数据时发生的竞争条件，可能导致事务在等待锁时产生延迟或阻塞。
* 死锁（Deadlock）：两个或多个事务相互等待对方释放锁，导致它们永远无法继续执行。



### 数据类型

* 整数类型（使用`UNSIGNED`指定无符号）

| 数据类型     | 字节大小 | 范围（有符号）                         | 范围（无符号）                  |
|--------------|----------|----------------------------------------|---------------------------------|
| `TINYINT`    | 1        | -128 到 127                            | 0 到 255                        |
| `SMALLINT`   | 2        | -32,768 到 32,767                      | 0 到 65,535                     |
| `MEDIUMINT`  | 3        | -8,388,608 到 8,388,607                | 0 到 16,777,215                 |
| `INT` / `INTEGER` | 4   | -2,147,483,648 到 2,147,483,647        | 0 到 4,294,967,295              |
| `BIGINT`     | 8        | -9,223,372,036,854,775,808 到 9,223,372,036,854,775,807 | 0 到 18,446,744,073,709,551,615|

* 浮点类型

| 数据类型       | 字节大小 | 描述                                    |
|----------------|----------|-----------------------------------------|
| `FLOAT(M,D)`   | 4        | 单精度浮点数，可以指定精度 `M,D`        |
| `DOUBLE(M,D)`  | 8        | 双精度浮点数，可以指定精度 `M,D`        |
| `DECIMAL(M,D)` | 变量宽度 | 定点数，精确的浮点数，用于存储精确值，例如货币 |

* 字符串类型

| 数据类型        | 最大长度                   | 描述                                         |
|-----------------|----------------------------|----------------------------------------------|
| `CHAR(M)`       | 0 到 255 字符              | 固定长度字符串。未用完的字符以空格填充       |
| `VARCHAR(M)`    | 0 到 65,535 字符           | 可变长度字符串，长度取决于字符集和编码       |
| `TINYTEXT`      | 0 到 255 字符              | 小文本字符串                                 |
| `TEXT`          | 0 到 65,535 字符           | 常规文本字符串                               |
| `MEDIUMTEXT`    | 0 到 16,777,215 字符       | 中等长度文本字符串                           |
| `LONGTEXT`      | 0 到 4,294,967,295 字符    | 长文本字符串                                 |
| `BINARY(M)`     | 0 到 255 字节              | 固定长度二进制数据                           |
| `VARBINARY(M)`  | 0 到 65,535 字节           | 可变长度二进制数据                           |
| `BLOB`          | 0 到 65,535 字节           | 二进制大对象，用于存储大数据，如图片、视频等 |

* 日期和时间类型

| 数据类型       | 字节大小 | 描述                                       |
|----------------|----------|--------------------------------------------|
| `DATE`         | 3        | 日期，格式为 `YYYY-MM-DD`                  |
| `TIME`         | 3        | 时间，格式为 `HH:MM:SS`                    |
| `DATETIME`     | 8        | 日期和时间，格式为 `YYYY-MM-DD HH:MM:SS`   |
| `TIMESTAMP`    | 4        | UNIX 时间戳，自动更新为当前时间            |
| `YEAR`         | 1        | 年，范围为 `1901` 到 `2155`               |

* JSON 类型

| 数据类型       | 描述                                       |
|----------------|--------------------------------------------|
| `JSON`         | 用于存储 JSON 格式的数据                   |

* 枚举/集合类型

```mysql
ENUM(值1, 值2)

SET(值1, 值2)
```



### 约束类型

* **PRIMARY KEY**（主键）：用于唯一标识表中的每一行数据。
* **FOREIGN KEY**（外键）：用于在两个表之间建立关系，确保外键列的值在另一张表中存在（参照完整性）。
* **UNIQUE**（唯一约束）：保证列中的值是唯一的，防止重复值。
* **CHECK**（检查约束）：用于定义一个表达式，限制列中的值必须满足该条件。
* **NOT NULL**（非空约束）：确保列的值不能为空，即不能插入 `NULL` 值。
* **DEFAULT**（默认值约束）：为列设置默认值，当插入数据时，如果未指定该列的值，则使用默认值。



### 授权类型

全局权限：

这些权限适用于数据库服务器上的所有数据库对象。

- **`ALL PRIVILEGES`**：授予所有权限（包括创建、删除、修改数据库和表等权限）。
- **`CREATE`**：允许创建数据库和表。
- **`DROP`**：允许删除数据库和表。
- **`PROCESS`**：允许查看正在执行的线程（包括查询）。
- **`RELOAD`**：允许刷新数据库、缓存等。
- **`SHUTDOWN`**：允许关闭 MySQL 服务器。
- **`SUPER`**：允许执行某些高级操作，如杀死线程、设置全局变量等。
- **`CREATE USER`**：允许创建、删除或修改 MySQL 用户。
- **`REPLICATION SLAVE`**：用于设置复制关系的权限。
- **`REPLICATION CLIENT`**：允许查询复制状态。
- **`SHOW DATABASES`**：允许查看所有数据库。
- **`FILE`**：允许文件操作，如加载本地数据文件。



数据库级权限：

这些权限适用于单个数据库。

- **`CREATE`**：允许创建数据库。
- **`DROP`**：允许删除数据库。
- **`GRANT OPTION`**：允许授予给其他用户与自己相同的权限。
- **`REFERENCES`**：在外键约束中使用引用。
- **`INDEX`**：允许创建和删除索引。
- **`ALTER`**：允许修改数据库的结构（例如更改表的结构、添加字段等）。
- **`SELECT`**：允许从数据库中读取数据。
- **`INSERT`**：允许向数据库表中插入数据。
- **`UPDATE`**：允许更新数据库中的数据。
- **`DELETE`**：允许从数据库中删除数据。
- **`CREATE TEMPORARY TABLES`**：允许创建临时表。
- **`LOCK TABLES`**：允许锁定表，以便进行临时操作。
- **`EXECUTE`**：允许执行存储过程和函数。
- **`SHOW VIEW`**：允许查看视图定义。
- **`CREATE VIEW`**：允许创建视图。
- **`EVENT`**：允许使用事件调度器。
- **`TRIGGER`**：允许创建触发器。



表级权限：

这些权限适用于数据库中的单个表。

- **`SELECT`**：允许从表中读取数据。
- **`INSERT`**：允许向表中插入数据。
- **`UPDATE`**：允许更新表中的数据。
- **`DELETE`**：允许从表中删除数据。
- **`INDEX`**：允许创建和删除表的索引。
- **`ALTER`**：允许修改表的结构。



列级权限：

这些权限适用于表中的单个列。

- **`SELECT`**：允许选择列中的数据。
- **`INSERT`**：允许插入数据到列中。
- **`UPDATE`**：允许更新列中的数据。



其他权限：

- **`EXECUTE`**：允许执行存储过程和函数。
- **`SHOW VIEW`**：允许查看视图的定义。
- **`CREATE ROUTINE`**：允许创建存储过程和函数。
- **`ALTER ROUTINE`**：允许修改存储过程和函数。
- **`DROP ROUTINE`**：允许删除存储过程和函数。
- **`EVENT`**：允许使用事件调度器。
- **`TRIGGER`**：允许创建触发器。



GRANT OPTION：

`GRANT OPTION` 权限允许用户将自己的权限授予其他用户，通常与其他权限一起使用。



### 聚合函数

* **`COUNT()`**：计算行数。
* **`SUM()`**：计算列的总和。
* **`AVG()`**：计算列的平均值。
* **`MIN()`**：获取列的最小值。
* **`MAX()`**：获取列的最大值。



### 数学函数

* **`abs(x)`**：返回x绝对值。
* **`rand()`**：返回0-1的随机数。
* **`mod(x, y)`**：返回x除以y的余数。
* **`power(x, y)`**：返回x的y次方。
* **`round(x, [y])`**：返回离x最近的整数、保留y位四舍五入小数。
* **`sqrt(x)`**：返回x的平方根。
* **`truncate(x, y)`**：返回x截断为y位小数的值。
* **`ceil(x)`**：返回大于等于x的最小整数。
* **`floor(x)`**：返回小于等于x的最大整数。
* **`greatest(x, y, z, ...)`**：返回集合中的最大值。
* **`least(x, y, z, ...)`**：返回集合中的最小值。



### 字符串函数

* **`trim`**：获取去除空格的字符串。
* **`concat(x, y)`**：获取x、y字符串拼接后的字符串。
* **`substr(x, y[, z])`**：获取字符串x中第y个位置开始、长度为z的字符串。
* **`length(x)`**：获取字符串x的长度。
* **`replace(x, y, z)`**：将字符串z替换字符串x中的字符串y。
* **`upper(x)`**：将字符串x转换为大写。
* **`lower(x)`**：将字符串x转换为小写。
* **`left(x, y)`**：返回字符串x中的前y个字符。
* **`right(x, y)`**：返回字符串x中的后y个字符。
* **`repeat(x, y)`**：返回字符串x重复y次。
* **`space(x)`**：返回x个空格。
* **`strcmp(x, y)`**：比较x和y，返回-1、0、1。
* **`reverse(x)`**：将字符串x翻转。



## 数据定义语言（DDL）

### 库

#### 创建

```mysql
CREATE DATABASE `库名` [CHARACTER SET 字符集] [COLLATE 排序规则] [COMMENT '数据库注释'];
```

> `CHARACTER SET`也可以简写为`CHARSET`。

#### 使用

```mysql
USE `库名`;
```

#### 删除

```mysql
DROP DATABASE [IF EXISTS] `库名`;
```

#### 查看

```mysql
# 查看所有数据库
SHOW DATABASES;

# 查看数据库结构
SHOW CREATE DATABASE `库名`;

# 查看当前使用的数据库
SELECT DATABASE();
```

#### 修改

```mysql
# 修改库属性
ALTER DATABASE `库名` [CHARSET 字符集] [COLLATE 排序规则];

# 修改库名
RENAME DATABASE `旧库名` TO `新库名`;
```



### 表

#### 创建

```mysql
CREATE TABLE `表名` (
    `列名` 数据类型 [约束] [AUTO_INCREMENT] [COMMENT 注释] [CHARSET 字符集] [COLLATE 排序规则],
    ...
)[ENGINE = 引擎] [CHARSET = 字符集] [COLLATE = 排序规则] [COMMENT = '表注释'];
```

> 列中，字符集与排序规则只适用于字符串类型。
>
> 表中，可以设置存储引擎。

#### 删除

```mysql
DROP TABLE [IF EXISTS] `表名`;
```

#### 清空

```mysql
truncate table `表名`;
```

#### 修改

```mysql
# 修改表属性
ALTER TABLE `表名` [ENGINE = 引擎] [CHARSET = 字符集] [COLLATE = 排序规则] [COMMENT = '表注释'];

# 表重命名
ALTER TABLE `表名` RENAME TO `新表名`;
```

#### 复制表

```mysql
CREATE TABLE `新表名` AS SELECT * FROM `旧表名`;
```

#### 查看

```mysql
# 查看所有表
SHOW TABLES;

# 查看表概要
DESC|DESCRIBE `表名`;

# 查看表结构
SHOW CREATE TABLE `表名`;

# 查看表状态
SHOW TABLE STATUS FROM `库名` WHERE NAME='表名';
```



### 列

#### 添加

```mysql
ALTER TABLE `表名` ADD COLUMN `字段名` 数据类型 [约束] [AFTER 位置] ...;
```

#### 删除

```mysql
ALTER TABLE `表名` DROP COLUMN `列名`;
```

#### 修改

```mysql
ALTER TABLE `表名` MODIFY COLUMN `列名` 数据类型 [约束] ...;
```

#### 更改

```mysql
ALTER TABLE `表名` CHANGE COLUMN `旧列名` `新列名` 数据类型 [约束] ...;
```



### 约束

注意：非空和默认值两个约束类型只能在列中进行操作。

#### 添加

```mysql
# 主键
ALTER TABLE `表名` ADD CONSTRAINT `主键名` PRIMARY KEY (`列名`);

# 外键
ALTER TABLE `表名` ADD CONSTRAINT `外键名` FOREIGN KEY (`列名`) REFERENCES `参考表`(`参考列`);

# 唯一
ALTER TABLE `表名` ADD CONSTRAINT `唯一约束名` UNIQUE (`列名`);

# 检查
ALTER TABLE `表名` ADD CONSTRAINT `检查约束名` CHECK (条件);
```

#### 删除

```mysql
# 主键
ALTER TABLE `表名` DROP PRIMARY KEY;

# 外键
ALTER TABLE `表名` DROP FOREIGN KEY `外键名`;

# 唯一
ALTER TABLE `表名` DROP UNIQUE `唯一约束名`;

# 检查
ALTER TABLE `表名` DROP CHECK `检查约束名`;
```

#### 修改

删除 -> 添加



## 数据控制语言（DCL）

### 用户

#### 创建

```mysql
CREATE USER '用户名'@'主机' IDENTIFIED BY '密码';
```

#### 删除

```mysql
DROP USER '用户名'@'主机';
```

#### 修改

```mysql
# 5.7.6 以上版本
ALTER USER '用户名'@'主机' IDENTIFIED BY '新密码';

# 5.7.5 及以下
SET PASSWORD FOR '用户名'@'主机' = PASSWORD('新密码');

# 修改当前连接用户密码
SET PASSWORD = PASSWORD('新密码');

# 命令行修改密码
mysqladmin -u 用户名 -p'旧密码' password '新密码'
```

#### 查看

```mysql
# 返回当前客户端连接的用户名
SELECT USER();

# 返回服务器验证的当前用户
SELECT CURRENT_USER();
```



### 权限

#### 查看

```mysql
SHOW GRANTS FOR '用户名'@'主机';
```

#### 授权

```mysql
GRANT 权限类型 ON '库.表' TO '用户名'@'主机' [WITH GRANT OPTION];
```

> `WITH GRANT OPTION`：可以允许该用户授权给其他用户。

#### 撤销

```mysql
REVOKE 权限类型 ON '库.表' FROM '用户名@主机名';
```

#### 刷新

```mysql
FLUSH PRIVILEGES;
```



## 数据操作语言（DML）

### 插入

```mysql
# 单条
INSERT INTO `表名` (`列名1`, `列名2`, ...) VALUE (值1, 值2, ...);

# 多条
INSERT INTO `表名` (`列名1`, `列名2`, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), ...;

# 从另一表插入数据
INSERT INTO `表名` (`列名1`, `列名2`, ...)
SELECT `列名1`, `列名2`, ... FROM `另一表` WHERE 条件;
```

### 更新

```mysql
UPDATE `表名` SET `列名1` = 新值1[, `列名2` = 新值2] [WHERE 条件];

# 根据查询结果更新
UPDATE `表名` SET `列名` = (SELECT 值 FROM `另一表` WHERE 条件) WHERE 条件;
```

### 删除

```mysql
DELETE FROM `表名` [WHERE 条件];
```

### 合并

`MERGE` 语句在一些数据库系统中用于同时执行 `INSERT`、`UPDATE` 和 `DELETE` 操作。虽然 MySQL 在 8.0 版本之前并没有直接支持 `MERGE`，但可以通过 `INSERT ... ON DUPLICATE KEY UPDATE` 或使用 `REPLACE` 语句模拟。

```mysql
INSERT INTO `表名` (`列名1`, `列名2`, ...) 
VALUES (值1, 值2, ...)
ON DUPLICATE KEY UPDATE `列名1` = 新值1, `列名2` = 新值2;
```

### 替换

`REPLACE` 是一种特殊的 `INSERT` 操作，当插入的数据已存在时，会首先删除已存在的行，然后再插入新数据。通常用于具有主键或唯一键的表。

```mysql
REPLACE INTO `表名` (`列名1`, `列名2`, ...) 
VALUES (值1, 值2, ...);
```



## 数据查询语言（DQL）

### 单表查询

#### 基本

```mysql
SELECT *|`列1`, `列2`, ... FROM `表名`;
```

#### 条件

`WHERE` 用于过滤行数据。

```mysql
SELECT `列1`, `列2` FROM `表名` WHERE 条件;
```

#### 排序

```mysql
SELECT `列1`, `列2` FROM `表名` ORDER BY `列1` [ASC|DESC];
```

#### 分组

```mysql
SELECT `列1`, 聚合函数(`列2`) FROM `表名` GROUP BY `列1` [HAVING 条件] [WITH ROLLUP];
```

> `HAVING`用于过滤分组数据，一般与聚合函数搭配。
>
> `WITH ROLLUP`用于在结果中添加小计行。

#### 分页

```mysql
SELECT `列1`, `列2` FROM `表名` LIMIT N;
```

#### 去重

```mysql
SELECT DISTINCT `列1` FROM `表名`;
```



### 连接查询

#### 内连接

只返回两个表中都匹配的记录

```mysql
# 显式
SELECT `列1`, `列2` FROM `表1` INNER JOIN `表2` ON 表1.列 = 表2.列;

# 隐式
SELECT `列1`, `列2` FROM `表1`, `表2` WHERE 表1.列 = 表2.列;
```

> `USING`：可以简化`ON`，用于连接相同的列名。

#### 左连接

返回左表中的所有记录，以及右表中匹配的记录。如果右表没有匹配记录，结果为 `NULL`。

```mysql
SELECT 列1, 列2 FROM 表1 LEFT JOIN 表2 ON 表1.列 = 表2.列;
```

#### 右连接

返回右表中的所有记录，以及左表中匹配的记录。如果左表没有匹配记录，结果为 `NULL`。

```mysql
SELECT 列1, 列2 FROM 表1 RIGHT JOIN 表2 ON 表1.列 = 表2.列;
```

#### 全外连接

MySQL **不支持** 全外连接（`FULL OUTER JOIN`），但是可以通过使用 `UNION` 来模拟它。

```mysql
SELECT 列1, 列2 FROM 表1 LEFT JOIN 表2 ON 表1.列 = 表2.列
UNION
SELECT 列1, 列2 FROM 表1 RIGHT JOIN 表2 ON 表1.列 = 表2.列;
```

#### 交叉连接

返回两个表的笛卡尔积。即，对于每一行左表的记录，它将与右表的每一行记录进行组合。因此，返回的记录数是左表记录数与右表记录数的乘积。

```mysql
SELECT 列1, 列2, ...
FROM 表1
CROSS JOIN 表2;   
```

#### 自然连接

自动连接两个表中所有相同名称的列。

```mysql
SELECT 列1, 列2, ...
FROM 表1
NATURAL JOIN 表2;   
```

#### 自连接

一个表与其自身进行连接。可以通过别名来区分同一表的不同实例。

```mysql
SELECT 列1, 列2, ...
FROM 表1 AS 别名1
INNER JOIN 表1 AS 别名2
ON 别名1.列 = 别名2.列;
```



### 子查询

#### 单行子查询

```mysql
SELECT 列1 FROM 表名 WHERE 列2 = (SELECT 列 FROM 另一表 WHERE 条件);
```

#### 多行子查询

```mysql
SELECT 列1 FROM 表名 WHERE 列2 IN (SELECT 列 FROM 另一表 WHERE 条件);
```

#### 关联子查询

```mysql
SELECT 列1, 列2 FROM 表1 WHERE 列3 = (SELECT 列3 FROM 表2 WHERE 表1.列 = 表2.列);
```

### 集合查询

#### 合并去重

```mysql
SELECT 列1 FROM 表1
UNION
SELECT 列1 FROM 表2;
```

#### 合并不去重

```mysql
SELECT 列1 FROM 表1
UNION ALL
SELECT 列1 FROM 表2;
```

### 其他查询

#### CASE查询

```mysql
# 简单CASE
SELECT 列1, CASE 列2
    WHEN 值1 THEN '结果1'
    WHEN 值2 THEN '结果2'
    ELSE '默认结果'
END FROM 表名;

# 搜索CASE
SELECT 列1, CASE
    WHEN 条件1 THEN '结果1'
    WHEN 条件2 THEN '结果2'
    ELSE '默认结果'
END FROM 表名;
```

#### NULL处理

* **`IS NULL`**：检查值是否为 `NULL`。

```mysql
SELECT 列1 FROM 表名 WHERE 列2 IS NULL;
```

* **`IS NOT NULL`**：检查值是否不为 `NULL`。

```mysql
SELECT 列1 FROM 表名 WHERE 列2 IS NOT NULL;
```

* **`COALESCE`**：返回第一个非 `NULL` 的值。

```mysql
SELECT COALESCE(列1, 列2, '默认值') FROM 表名;
```

* **`IFNULL`**：如果表达式为 `NULL`，则返回默认值。

```mysql
SELECT IFNULL(列1, '默认值') FROM 表名;
```



## 数据库管理（DA）

#### 备份

```bash
# 单数据库
mysqldump -u 用户名 -p 库名 > 备份文件.sql

# 多数据库
mysqldump -u 用户名 -p --databases 库1 库2 > 多库备份.sql

# 所有数据库
mysqldump -u 用户名 -p --all-databases > 全库备份.sql

# 指定表
mysqldump -u 用户名 -p 库名 表1 表2 > 表备份.sql

# 压缩备份
mysqldump -u 用户名 -p 库名 | gzip > 压缩备份.sql.gz
```

#### 恢复

```bash
# 单/多库恢复
mysql -u 用户名 -p [库名] < 备份文件.sql

# 压缩恢复
gunzip < 压缩备份.sql.gz | mysql -u 用户名 -p 库名
```

> 一般多库恢复不需要指定库名。



## 触发器

### 理论

组成：

- 触发事件：指定触发器在哪种操作下触发。可以是 `INSERT`、`UPDATE`、`DELETE` 操作。
- 触发时间：指定触发器在操作之前或之后执行，使用 `BEFORE` 或 `AFTER`。
- 触发动作：触发器的具体 SQL 代码，即在触发时要执行的操作。



限制：

* 每个表最多只能为 `INSERT`、`UPDATE`、`DELETE` 操作各定义一个 `BEFORE` 和 `AFTER` 触发器。
* 触发器不能直接调用事务控制语句（如 `COMMIT` 和 `ROLLBACK`），因为触发器在事务内部执行。
* MySQL 不支持表上级联触发（递归触发），即触发器中不应再对该表进行操作，以免形成循环触发。



作用：

* 自动化数据验证和清理。
* 数据库事件记录（如日志表记录）。
* 实现复杂的数据一致性和约束检查。



### 操作

#### 创建

```mysql
CREATE TRIGGER 触发器名
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON 表名
FOR EACH ROW
触发器执行的操作SQL;
```

> **`OLD`**：获取触发操作之前的数据（用于 `UPDATE` 和 `DELETE`）。
>
> **`NEW`**：获取触发操作之后的数据（用于 `INSERT` 和 `UPDATE`）。

#### 删除

```mysql
DROP TRIGGER IF EXISTS 表名.触发器名;
```

#### 查看

```mysql
SHOW TRIGGERS;
```



## 索引

索引（Index）是数据库中用于加速数据查询的结构，它在指定字段上创建类似“目录”的数据结构，使数据库可以快速查找到所需数据。虽然索引能够提高查询速度，但它们会占用额外的存储空间，并且在进行数据插入、更新、删除等操作时会带来额外的维护开销。因此，索引的创建和使用需要平衡查询性能和存储、维护开销。

### 类型

1. **普通索引（Normal Index）**：最常用的索引类型，用于加速查询，允许重复和 `NULL` 值。
2. **唯一索引（Unique Index）**：在索引列上不允许出现重复的值，适合唯一性要求的字段，例如身份证号、手机号等。
3. **主键索引（Primary Key Index）**：是唯一索引的一种，只允许一列或多列的组合设置为主键，且值不允许为 `NULL`。一个表只能有一个主键索引。
4. **全文索引（Full-Text Index）**：主要用于查找文本数据中的关键词，例如对大段文本进行搜索。MySQL 中 `FULLTEXT` 只支持 `CHAR`、`VARCHAR` 和 `TEXT` 列。
5. **组合索引（Composite Index）**：在多个列上创建的索引，用于高效地处理多条件查询，但组合索引的顺序影响使用效果（最左前缀匹配）。
6. **空间索引（Spatial Index）**：用于地理数据类型的索引，例如 `POINT`、`LINESTRING`、`POLYGON` 等。通常用于 GIS（地理信息系统）应用。



### 操作

#### 创建

```mysql
# 直接
CREATE INDEX|UNIQUE|PRIMARY KEY|FULLTEXT 索引名 ON 表名(列名1[, 列名2 ...]);

# 创建表
CREATE TABLE 表名 (
    列名1 数据类型,
    ...
    INDEX [索引名] (列名1[, 列名2 ...]),
    UNIQUE [索引名] (列名1[, 列名2 ...]),
    PRIMARY KEY [索引名] (列名1[, 列名2 ...]),
    FULLTEXT [索引名] (列名1[, 列名2 ...])
);

# 修改表
ALTER TABLE 表名 ADD INDEX|UNIQUE|PRIMARY KEY|FULLTEXT 索引名 (列名1[, 列名2 ...]);
```

#### 删除

```mysql
# 直接
DROP INDEX 索引名 ON 表名;

# 修改表
ALTER TABLE 表名 DROP INDEX 索引名;

# 主键索引
ALTER TABLE 表名 DROP PRIMARY KEY;
```

#### 查看

```mysql
SHOW INDEX|KEYS FROM `表名`;
```

> 使用 `EXPLAIN` 分析查询执行计划可以检查是否用到了索引。



## 视图

### 理论

视图（View）是一个**虚拟表**，表示一组通过查询获得的数据结果。它不存储实际数据，而是存储 SQL 查询。视图的使用可以简化复杂查询、隐藏数据细节、提供数据安全以及在数据展示中提升灵活性。



优点：

1. **简化复杂查询**：将复杂查询封装为视图，方便重复使用，减少代码冗余。
2. **数据安全**：视图可以限制用户访问特定列或数据行，实现数据隔离。
3. **增强灵活性**：视图的定义可以改变，而不影响依赖视图的查询。
4. **数据抽象**：提供数据的抽象层，隐藏表结构的复杂性。



缺点：

1. **性能问题**：视图没有实际存储数据，每次查询会重新执行定义的 SQL 语句。如果视图定义过于复杂，可能会降低查询性能。
2. **不支持索引**：视图本身不支持索引，无法在视图中直接使用索引提升性能。
3. **数据一致性**：视图依赖的表结构发生变化（如列名更改），视图需要重新定义。



### 操作

#### 创建

```mysql
CREATE [OR REPLACE]
[ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
[DEFINER = { user | CURRENT_USER }]
[SQL SECURITY { DEFINER | INVOKER }]
VIEW view_name [(column_list)]
AS select_statement
[WITH [CASCADED | LOCAL] CHECK OPTION];
```

选参说明：

* `OR REPLACE`：如果视图已存在，则替换旧视图的定义，而无需先删除该视图。
* `ALGORITHM`：指定视图的处理算法。
  * `UNDEFINED`（默认）：让 MySQL 自动选择最适合的算法。
  * `MERGE`：将视图的查询直接合并到实际查询中，适用于简单视图，可以提高查询效率。
  * `TEMPTABLE`：将视图的结果放到一个临时表中处理，适用于复杂的视图，但会增加系统开销，并且无法更新。
* `DEFINER`：指定创建视图的用户，即视图的“定义者”。该用户定义了视图的访问权限。
  * `user`：指定的用户具有创建视图的权限（如 `'root'@'localhost'`）。
  * `CURRENT_USER`：使用当前登录的用户为定义者。
* `SQL SECURITY`：设置视图的权限执行模式。
  * `DEFINER`：使用视图定义者的权限执行 SQL 语句（默认）。
  * `INVOKER`：使用调用者的权限执行 SQL 语句。
* `(column_list)`：视图的列名列表，可以指定每一列的名称；如果省略，则使用 `SELECT` 语句的结果列名称。
* `WITH ... CHECK OPTION`：确保通过视图插入或更新的数据符合视图定义的条件。
  * `CASCADED`（默认）：检查视图及所有依赖视图的定义条件。
  * `LOCAL`：只检查视图本身的条件。

#### 修改

```mysql
ALTER
[ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
[DEFINER = { user | CURRENT_USER }]
[SQL SECURITY { DEFINER | INVOKER }]
VIEW view_name [(column_list)]
AS select_statement
[WITH [CASCADED | LOCAL] CHECK OPTION];
```

> 或在创建语句中使用`OR REPLACE`参数可进行覆盖修改。

#### 删除

```mysql
DROP VIEW 视图名;
```

#### 更新

视图可以更新，但必须满足一些条件才能进行数据修改操作：

1. 视图基于单个表创建。
2. 视图中不包含聚合函数（如 `SUM`、`COUNT` 等）、`DISTINCT`、`GROUP BY`、`HAVING` 等。
3. 视图的定义中没有子查询或连接操作。



## 存储过程

### 理论

存储过程（Stored Procedure）是一组在数据库中存储的 SQL 语句集合，可以执行一系列的操作。通过调用存储过程，开发人员能够减少网络交互次数，并封装复杂逻辑，使代码重用性和效率提高。



优点：

1. **减少网络通信**：客户端调用存储过程，只需一次请求即可执行多个 SQL 语句，减少了网络交互的开销。
2. **提高性能**：存储过程编译后存储在数据库中，调用时无需重新编译，执行效率较高。
3. **增强安全性**：存储过程可以将复杂逻辑封装在数据库中，限制直接对表的访问。
4. **代码重用**：封装通用逻辑，便于重用，提高开发效率。



### 操作

#### 创建

```mysql
CREATE PROCEDURE 存储过程名 ([IN | OUT | INOUT] 参数名 数据类型, ...)
BEGIN
    -- 存储过程的 SQL 语句；
END;
```

* `IN`：输入参数，传入存储过程，存储过程内部可以使用该值，但不能修改。
* `OUT`：输出参数，传出存储过程，用于返回处理结果。
* `INOUT`：既可以作为输入参数传入，也可以在存储过程中修改，并返回修改后的值。



#### 流程控制

条件语句：

* `IF`：用于在存储过程中进行条件判断。

```mysql
IF condition THEN
    -- statements;
ELSEIF condition THEN
    -- statements;
ELSE
    -- statements;
END IF;
```

* `CASE`：`CASE` 语句是一种更灵活的条件控制结构，类似于 `IF` 语句，但便于多个条件判断。

```mysql
CASE
    WHEN condition THEN
        -- statements;
    WHEN condition THEN
        -- statements;
    ELSE
        -- statements;
END CASE;
```

循环语句：

* `LOOP`：`LOOP` 是基本的循环结构，适合在循环次数不确定的情况下使用。使用 `LEAVE` 退出循环。

```mysql
[loop_label:] LOOP
    -- statements;
    IF condition THEN
        LEAVE loop_label;
    END IF;
END LOOP;
```

> 使用 `LEAVE` 退出循环。

* `WHILE`：`WHILE` 循环在满足条件时执行语句组，当条件不满足时结束循环。

```mysql
[loop_label:] WHILE condition DO
    -- statements;
END WHILE;
```

* `REPEAT`：`REPEAT` 循环和 `WHILE` 类似，但它会至少执行一次循环体。

```mysql
[loop_label:] REPEAT
    -- statements;
UNTIL condition END REPEAT;
```

* `FOR`：没有直接的 `FOR` 循环结构，但可以通过 `WHILE` 或 `REPEAT` 循环结合一个计数器变量来实现 `FOR` 循环的效果。

```mysql
DELIMITER //

CREATE PROCEDURE for_example()
BEGIN
    DECLARE i INT DEFAULT 1;
    
    WHILE i <= 10 DO
        -- 执行的操作
        SET i = i + 1;
    END WHILE;
END //

DELIMITER ;
```

跳转语句：

* `LEAVE`：用于在循环中提前退出循环（break）。
* `ITERATE`：用于重新开始循环（continue）。

错误处理语句：

* `DECLARE ... HANDLER`：用于定义错误处理器，以便在发生特定错误或异常时执行特定的操作。可以定义 `CONTINUE` 或 `EXIT` 处理类型。

  * `CONTINUE`：当异常发生时，继续执行后续代码。

  * `EXIT`：当异常发生时，退出当前块。

```mysql
DECLARE handler_type HANDLER FOR condition_value_list statement;
```

#### 调用

```mysql
CALL 存储过程名([IN | OUT | INOUT] 参数名 数据类型, ...);
```

#### 删除

```mysql
DROP PROCEDURE 存储过程名;
```

#### 查看

```mysql
# 查看所有
SHOW PROCEDURE STATUS WHERE DB = '数据库名';

# 查看指定
SHOW CREATE PROCEDURE 存储过程名;
```

#### 游标

游标是用于逐行处理查询结果集的结构，适合需要对查询结果逐行操作的情况。以下是存储过程内游标的相关内容和使用方法。

主要步骤：

1. **声明游标**：定义游标及其对应的查询语句。

```mysql
DECLARE cursor_name CURSOR FOR select_statement;
```

2. **声明结束处理器**：定义一个处理程序，用来控制游标遍历结束后的行为。

```mysql
DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
```

3. **打开游标**：初始化游标，准备提取数据。

```mysql
OPEN cursor_name;
```

4. **提取数据**：使用 `FETCH` 语句逐行获取数据。

```mysql
FETCH cursor_name INTO variable_name [, variable_name] ...;
```

5. **关闭游标**：释放游标占用的资源。

```mysql
CLOSE cursor_name;
```

示例：

```mysql
# 假设有一个 employees 表，包含 id 和 salary 列。下面的存储过程会逐行遍历员工表，将每位员工的薪水提高 10%。

sql
复制代码
DELIMITER //

CREATE PROCEDURE increase_salaries()
BEGIN
    -- 声明变量
    DECLARE emp_id INT;
    DECLARE emp_salary DECIMAL(10, 2);
    DECLARE done BOOLEAN DEFAULT FALSE;

    -- 声明游标
    DECLARE emp_cursor CURSOR FOR SELECT id, salary FROM employees;

    -- 声明结束处理器，用于检测游标遍历的结束
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;

    -- 打开游标
    OPEN emp_cursor;

    -- 开始遍历
    read_loop: LOOP
        -- 获取一行数据
        FETCH emp_cursor INTO emp_id, emp_salary;

        -- 检查是否到达结果集的末尾
        IF done THEN
            LEAVE read_loop;
        END IF;

        -- 进行所需操作，如加薪 10%
        UPDATE employees SET salary = emp_salary * 1.1 WHERE id = emp_id;
    END LOOP;

    -- 关闭游标
    CLOSE emp_cursor;
END //

DELIMITER ;
```

> **`DECLARE CONTINUE HANDLER FOR NOT FOUND`**：声明一个异常处理器，用于在遍历结束时将 `done` 变量设置为 `TRUE`。
>
> **`LOOP` 和 `LEAVE`**：`LOOP` 用于循环处理结果集，而 `LEAVE` 用于在游标到达末尾时退出循环。
>
> **`FETCH`**：每次从游标中获取一行数据，并将数据存入指定的变量。
>
> **`CLOSE`**：在操作完成后，关闭游标以释放资源。
