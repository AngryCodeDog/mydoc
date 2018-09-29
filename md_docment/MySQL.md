

# MySQL

[TOC]



## 一、安装

windows下和mac下安装很简单，去到官网下载安装文件安装即可。

ubuntu下安装

```
sudo apt-get install mysql-server
```





##  二、基础

数据库表中的每一列都需要有一个名称和数据类型。

SQL 开发人员必须在创建 SQL 表时决定表中的每个列将要存储的数据的类型。数据类型是一个标签，是便于 SQL 了解每个列期望存储什么类型的数据的指南，它也标识了 SQL 如何与存储的数据进行交互。

下面的表格列出了 SQL 中通用的数据类型：

| 数据类型                           | 描述                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| CHARACTER(n)                       | 字符/字符串。固定长度 n。                                    |
| VARCHAR(n) 或 CHARACTER VARYING(n) | 字符/字符串。可变长度。最大长度 n。                          |
| BINARY(n)                          | 二进制串。固定长度 n。                                       |
| BOOLEAN                            | 存储 TRUE 或 FALSE 值                                        |
| VARBINARY(n) 或 BINARY VARYING(n)  | 二进制串。可变长度。最大长度 n。                             |
| INTEGER(p)                         | 整数值（没有小数点）。精度 p。                               |
| SMALLINT                           | 整数值（没有小数点）。精度 5。                               |
| INTEGER                            | 整数值（没有小数点）。精度 10。                              |
| BIGINT                             | 整数值（没有小数点）。精度 19。                              |
| DECIMAL(p,s)                       | 精确数值，精度 p，小数点后位数 s。例如：decimal(5,2) 是一个小数点前有 3 位数小数点后有 2 位数的数字。 |
| NUMERIC(p,s)                       | 精确数值，精度 p，小数点后位数 s。（与 DECIMAL 相同）        |
| FLOAT(p)                           | 近似数值，尾数精度 p。一个采用以 10 为基数的指数计数法的浮点数。该类型的 size 参数由一个指定最小精度的单一数字组成。 |
| REAL                               | 近似数值，尾数精度 7。                                       |
| FLOAT                              | 近似数值，尾数精度 16。                                      |
| DOUBLE PRECISION                   | 近似数值，尾数精度 16。                                      |
| DATE                               | 存储年、月、日的值。                                         |
| TIME                               | 存储小时、分、秒的值。                                       |
| TIMESTAMP                          | 存储年、月、日、小时、分、秒的值。                           |
| INTERVAL                           | 由一些整数字段组成，代表一段时间，取决于区间的类型。         |
| ARRAY                              | 元素的固定长度的有序集合                                     |
| MULTISET                           | 元素的可变长度的无序集合                                     |
| XML                                | 存储 XML 数据                                                |

来自 W3CSchool 的 SQL 快速参考

------

| SQL 语句        | 语法                                                         |
| --------------- | ------------------------------------------------------------ |
| AND / OR        | SELECT column_name(s) FROM table_name WHERE condition AND\|OR condition |
| ALTER TABLE     | ALTER TABLE table_name  ADD column_name datatypeorALTER TABLE table_name  DROP COLUMN column_name |
| AS (alias)      | SELECT column_name AS column_alias FROM table_nameorSELECT column_name FROM table_name AS table_alias |
| BETWEEN         | SELECT column_name(s) FROM table_name WHERE column_name BETWEEN value1 AND value2 |
| CREATE DATABASE | CREATE DATABASE database_name                                |
| CREATE TABLE    | CREATE TABLE table_name ( column_name1 data_type, column_name2 data_type, column_name2 data_type, ... ) |
| CREATE INDEX    | CREATE INDEX index_name ON table_name (column_name)orCREATE UNIQUE INDEX index_name ON table_name (column_name) |
| CREATE VIEW     | CREATE VIEW view_name AS SELECT column_name(s) FROM table_name WHERE condition |
| DELETE          | DELETE FROM table_name WHERE some_column=some_valueorDELETE FROM table_name  (**Note:** Deletes the entire table!!)DELETE * FROM table_name  (**Note:** Deletes the entire table!!) |
| DROP DATABASE   | DROP DATABASE database_name                                  |
| DROP INDEX      | DROP INDEX table_name.index_name (SQL Server) DROP INDEX index_name ON table_name (MS Access) DROP INDEX index_name (DB2/Oracle) ALTER TABLE table_name DROP INDEX index_name (MySQL) |
| DROP TABLE      | DROP TABLE table_name                                        |
| GROUP BY        | SELECT column_name, aggregate_function(column_name) FROM table_name WHERE column_name operator value GROUP BY column_name |
| HAVING          | SELECT column_name, aggregate_function(column_name) FROM table_name WHERE column_name operator value GROUP BY column_name HAVING aggregate_function(column_name) operator value |
| IN              | SELECT column_name(s) FROM table_name WHERE column_name IN (value1,value2,..) |
| INSERT INTO     | INSERT INTO table_name VALUES (value1, value2, value3,....)*or*INSERT INTO table_name (column1, column2, column3,...) VALUES (value1, value2, value3,....) |
| INNER JOIN      | SELECT column_name(s) FROM table_name1 INNER JOIN table_name2  ON table_name1.column_name=table_name2.column_name |
| LEFT JOIN       | SELECT column_name(s) FROM table_name1 LEFT JOIN table_name2  ON table_name1.column_name=table_name2.column_name |
| RIGHT JOIN      | SELECT column_name(s) FROM table_name1 RIGHT JOIN table_name2  ON table_name1.column_name=table_name2.column_name |
| FULL JOIN       | SELECT column_name(s) FROM table_name1 FULL JOIN table_name2  ON table_name1.column_name=table_name2.column_name |
| LIKE            | SELECT column_name(s) FROM table_name WHERE column_nameLIKE pattern |
| ORDER BY        | SELECT column_name(s) FROM table_name ORDER BY column_name [ASC\|DESC] |
| SELECT          | SELECT column_name(s) FROM table_name                        |
| SELECT *        | SELECT * FROM table_name                                     |
| SELECT DISTINCT | SELECT DISTINCT column_name(s) FROM table_name               |
| SELECT INTO     | SELECT * INTO new_table_name [IN externaldatabase] FROM old_table_name*or*SELECT column_name(s) INTO new_table_name [IN externaldatabase] FROM old_table_name |
| SELECT TOP      | SELECT TOP number\|percent column_name(s) FROM table_name    |
| TRUNCATE TABLE  | TRUNCATE TABLE table_name                                    |
| UNION           | SELECT column_name(s) FROM table_name1 UNION SELECT column_name(s) FROM table_name2 |
| UNION ALL       | SELECT column_name(s) FROM table_name1 UNION ALL SELECT column_name(s) FROM table_name2 |
| UPDATE          | UPDATE table_name SET column1=value, column2=value,... WHERE some_column=some_value |
| WHERE           | SELECT column_name(s) FROM table_name WHERE column_name operator value |

### 运算符

=     赋值和判断都是

!=，<> 这两个都是不等于

<，>，<=，>=

OR

AND

BETWEEN...AND

IN  在集合里面

 NOT IN  







## 三、操作

一些最重要的 SQL 命令

- **SELECT** - 从数据库中提取数据
- **UPDATE** - 更新数据库中的数据
- **DELETE** - 从数据库中删除数据
- **INSERT INTO** - 向数据库中插入新数据
- **CREATE DATABASE** - 创建新数据库
- **ALTER DATABASE** - 修改数据库
- **CREATE TABLE** - 创建新表
- **ALTER TABLE** - 变更（改变）数据库表
- **DROP TABLE** - 删除表
- **CREATE INDEX** - 创建索引（搜索键）
- **DROP INDEX** - 删除索引



DDL：数据定义语言----创建、删除、修改库表结构（重）

DML：数据操作语言----增加、删除、修改表的记录（重）

DCL：数据控制语言---- 用户创建以及授权（懂就行）

DQL：数据查询语言----查询数据（重）

---

### 创建

#### 创建数据库

```sql
CREATE DATABASE dbname;
```

删除数据库

```sql
DROP DATABASE dbname;
```



#### 创建表

```sql
CREATE TABLE table_name
(
column_name1 data_type(size),
column_name2 data_type(size),
column_name3 data_type(size),
....
);
```

实例：

```sql
CREATE TABLE Persons
(
PersonID int,
LastName varchar(255),
FirstName varchar(255),
Address varchar(255),
City varchar(255)
);
```

表的主键

```
CREATE TABLE Persons
(
id INT PRIMARY_KEY,
name VARCHAR(128)
);
```

外键

```
CONSTRAINT 外键别名 FOREIGH KEY(属性1,属性2,...,属性n) REFERENCES 表名(属性1，属性2...属性n)

#例子：
CREATE TABLE Persons
(
id INT PRIMARY_KEY,
name VARCHAR(128)
math_teacher_id INT,
sport_teacher_id INT,
CONSTRAINT t_id FOREIGN KEY (math_teacher_id,sport_teacher_id) REFERENCES teacher(math_teacher_id, sport_teacher_id)
);
# 外键别名是t_id，math_teacher_id,sport_teacher_id为外键，与之关联的是teacher表中的对应字段
```





#### 添加/删除/修改表的列

ALTER TABLE 语句用于在现有表中添加、删除或修改列。

修改表名

```sql
ALTER TABLE table_name RENAME new_table_name;
```

修改字段的数据类型

```
ALTER TABLE table_name MODIFY 属性名 数据类型;
```

修改字段名

```
ALTER TABLE table_name CHANGE 旧属性名 新属性名 新数据类型;
```

增加字段

```
ALTER TABLE table_name ADD 属性名1 数据类型 [完整性约束条件]

# 例子
ALTER TABLE user ADD phone VARCHAR(20);  # 增加phone字段到user表中
ALTER TABLE user ADD age int(4) NOT NULL; # 增加有完整性约束条件的age字段到user表中
```

删除字段

```
ALTER TABLE table_name DROP 属性名;
```

删除表的外键约束

```
ALTER TABLE table_name DROP FROEIGH KEY 外键别名;
```







### 查询

#### select

```
SELECT column1, column2, ...
FROM table_name;
```



在一张表内，一列通常包含许多重复的值; 有时你只想列出不同的（different）值。

SELECT DISTINCT语句用于仅返回不同的（different）值。

```
SELECT DISTINCT column1, column2, ...
FROM table_name;
```

#### where

WHERE子句用于提取满足指定标准的记录。

```
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

**注意：** WHERE子句不仅用于SELECT语法，还用于UPDATE，DELETE语法等！

WHERE子句可以与AND，OR和NOT运算符结合使用。

AND和OR运算符用于根据多个条件筛选记录：

- 如果由AND分隔的所有条件为TRUE，则AND运算符显示记录。
- 如果由OR分隔的任何条件为真，则OR运算符显示记录。

如果条件不为真，则NOT运算符显示记录。 

#### AND

```
SELECT column1, column2, ...
FROM table_name
WHERE condition1 AND condition2 AND condition3 ...;
```

#### OR

```
SELECT column1, column2, ...
FROM table_name
WHERE condition1 OR condition2 OR condition3 ...;
```

#### NOT

```
SELECT column1, column2, ...
FROM table_name
WHERE NOT condition;
```

过滤重复值DISTINCT：

```
SELECT DISTINCT 列名1 FROM 表名
```

连接CONCAT(列名1，列名2)

```
SELECT CONCAT(name,phone) as name_phone from user;  #连接查询的值 

+---------------------+
| name_phone          |
+---------------------+
| zhangsan13312345678 |
| 李四111111          |
+---------------------+


select concat_ws('==',name,phone) as name_phone from persons; # 以==符号连接要查询的值
+-----------------------+
| name_phone            |
+-----------------------+
| zhangsan==13312345678 |
| 李四==111111          |
+-----------------------+

```

模糊查询(很慢的，数据了到了几十上百万就不适合了)

select name from persons where name like '%zhang';



倒序，升序

order by asc，order by desc

### 插入

#### INSERT INTO

INSERT INTO 语句可以用两种形式编写。
第一个表单没有指定要插入数据的列的名称，只提供要插入的值：

```
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

如果要为表中的所有列添加值，则不需要在SQL查询中指定列名称。但是，请确保值的顺序与表中的列顺序相同。INSERT INTO语法如下所示：

```
INSERT INTO table_name
VALUES (value1, value2, value3, ...);
```



### 更新

#### UPDATE 语句

UPDATE 语句用于更新表中已存在的记录。 

```
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

**要注意SQL UPDATE 语句中的 WHERE 子句！** 
WHERE子句指定哪些记录需要更新。如果省略WHERE子句，所有记录都将更新！

### 删除

#### DELETE 语法

DELETE 语句用于删除表中的行

```
DELETE FROM table_name
WHERE condition;
```

**注意SQL DELETE 语句中的 WHERE 子句！**
WHERE子句指定需要删除哪些记录。如果省略了WHERE子句，表中所有记录都将被删除！

您可以删除表中的所有行，而不需要删除该表。这意味着表的结构、属性和索引将保持不变：

```
DELETE FROM table_name;
```

**或者**

```
DELETE * FROM table_name;
```

**注意：**在没有备份的情况下，删除记录要格外小心！因为你删除了不能重复！

## 四、高级教程



### LIMIT

```sql
SELECT column_name(s)
FROM table_name
WHERE condition
LIMIT start,number;
```

例子：

```
SELECT *
FROM Persons
LIMIT 5;
```

LIMIT如果不指定起始位置时，默认从第一条记录开始显示

---



### LIKE

```
SELECT column1, column2, ...
FROM table_name
WHERE columnN LIKE pattern;
```

提示

：您还可以使用AND或OR运算符组合任意数量的条件。

下面是一些使用'％'和'_'通配符显示不同LIKE运算符的例子：

| LIKE 运算符                     | 描述                                 |
| ------------------------------- | ------------------------------------ |
| WHERE CustomerName LIKE 'a%'    | 查找以“a”开头的任何值                |
| WHERE CustomerName LIKE '%a'    | 查找以“a”结尾的任何值                |
| WHERE CustomerName LIKE '%or%'  | 在任何位置查找任何具有“or”的值       |
| WHERE CustomerName LIKE '_r%'   | 在第二个位置查找任何具有“r”的值      |
| WHERE CustomerName LIKE 'a_%_%' | 查找以“a”开头且长度至少为3个字符的值 |
| WHERE ContactName LIKE 'a%o'    | 找到以"a"开头，以"o"结尾的值         |

---



### IN

IN运算符允许您在WHERE子句中指定多个值。

IN运算符是多个OR条件的简写。

```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);
```

或者

```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (SELECT STATEMENT);
```

代码示例：

以下SQL语句选择位于“Germany”，“France”和“UK”的所有客户：

```sql
SELECT * FROM Customers
WHERE Country IN ('Germany', 'France', 'UK');
```

以下SQL语句选择不在“Germany”，“France”或“UK”中的所有客户：

```
SELECT * FROM Customers
WHERE Country NOT IN ('Germany', 'France', 'UK');
```

以下SQL语句选择来自同一国家的所有客户作为供应商：

```sql
SELECT * FROM Customers
WHERE Country IN (SELECT Country FROM Suppliers);
```

---



### BETWEEN

BETWEEN 操作符用于选取介于两个值之间的数据范围内的值。

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```

例如选择价格在10到20之间的所有产品：

```sql
SELECT * FROM Products
WHERE Price BETWEEN 10 AND 20;
```

要显示前面示例范围之外的产品，请使用NOT BETWEEN：

```sql
SELECT * FROM Products
WHERE Price NOT BETWEEN 10 AND 20;
```

选择价格在10到20之间但CategoryID不是1、2或3的所有产品：

```sql
SELECT * FROM Products
WHERE (Price BETWEEN 10 AND 20)
AND NOT CategoryID IN (1,2,3);
```

下面的 SQL 语句选取 name 以介于 'A' 和 'H' 之间字母开始的所有网站：

```sql
SELECT * FROM Websites
WHERE name BETWEEN 'A' AND 'H';
```

---



### JOIN

SQL join 用于把来自两个或多个表的行结合起来。

选自 "Orders" 表的数据：

| OrderID | CustomerID | OrderDate  |
| ------- | ---------- | ---------- |
| 10308   | 2          | 1996-09-18 |
| 10309   | 37         | 1996-09-19 |
| 10310   | 77         | 1996-09-20 |

然后，看看选自 "Customers" 表的数据：

| CustomerID | CustomerName                       | ContactName    | Country |
| ---------- | ---------------------------------- | -------------- | ------- |
| 1          | Alfreds Futterkiste                | Maria Anders   | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo   | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno | Mexico  |

请注意，"Orders" 表中的 "CustomerID" 列指向 "Customers" 表中的客户。上面这两个表是通过 "CustomerID" 列联系起来的。

然后，如果我们运行下面的 SQL 语句（包含 INNER JOIN）：

实例

```sql
SELECT Orders.OrderID, Customers.CustomerName, Orders.OrderDate
FROM Orders
INNER JOIN Customers
ON Orders.CustomerID=Customers.CustomerID;
```

运行结果如下所示：

| OrderID | CustomerName                       | OrderDate  |
| ------- | ---------------------------------- | ---------- |
| 10308   | Ana Trujillo Emparedados y helados | 1996-09-18 |

---



### UNION 运算符

UNION运算符用于组合两个或更多SELECT语句的结果集。

- UNION中的每个SELECT语句必须具有相同的列数
- 这些列也必须具有相似的数据类型
- 每个SELECT语句中的列也必须以相同的顺序排列

```
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

默认情况下，UNION 运算符选择一个不同的值。如果允许重复值，请使用 UNION ALL。

UNION ALL 语法

```

SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;

```

带有 WHERE 的 SQL UNION ALL

以下SQL语句使用UNIONALL从"Customers"和 "Suppliers" 表中选择所有德国城市（也是重复数值）：

```sql
SELECT City, Country FROM Customers
WHERE Country='Germany'
UNION ALL
SELECT City, Country FROM Suppliers
WHERE Country='Germany'
ORDER BY City;
```

---



### SELECT INTO 语句

使用 SQL，您可以将信息从一个表中复制到另一个表中。

SELECT INTO 语句从一个表中复制数据，然后将数据插入到另一个新表中。

我们可以把所有的列都复制到新表中：

```sql
SELECT *
INTO newtable [IN externaldb]
FROM table1; 
```

或者只复制希望的列插入到新表中：

```sql
 SELECT column_name(s)
 INTO newtable [IN externaldb]
 FROM table1;
```

---



### INSERT INTO SELECT 语句

使用SQL，您可以将信息从一个表中复制到另一个表中。

INSERT INTO SELECT 从表中复制数据，并将数据插入现有的表中。



我们可以将所有列从一个表中复制到另一个已经存在的表中：

 ```sql
INSERT INTO table2
SELECT * FROM table1; 
 ```

或者我们可以把想要的列复制到另一个现有的表中：

```sql
INSERT INTO table2
(column_name(s))
SELECT column_name(s)
FROM table1; 
```



## 五、函数

### COUNT

统计数量

```
select count(*) from user;
```

如果数据量很大，可以选择直接用最后一个数据的id 来作为总数，展示个大概的数据就好。

### SUM

求和

```
select sum(列名) from 表名;
```



### AVG

平均数

```
select avg(列名) from 表名;
```



### MAX

最大值，查询该列最大值

```
select max(列名) from 表名;
```



### MIN

最小值，查询该列最小值

```
select min(列名) from 表名;
```



### GROUP BY

分组查询

GROUP BY语句通常与集合函数（COUNT，MAX，MIN，SUM，AVG）一起使用，以按一个或多个列对结果集进行分组。

```
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
```

```
SELECT * FROM 表名 GROUP BY sex; # 按照性别分组
SELECT * FROM 表名 GROUP BY sex HAVING COUNT(*)>3 # 根据性别分组并且找到数量大于3的那一组
```



### 内连接



### 左连接

SQL左链接LEFT JOIN关键字返回左表（表1）中的所有行，即使在右表（表2）中没有匹配。



### 右连接

SQL右链接 RIGHT JOIN 关键字返回右表（table2）的所有行，即使在左表（table1）上没有匹配。



首推内连接





## MySQL优化

### 表的优化

- 1、定长与变长分离

  入id int,占4个字节，char(4)占4个字符长度，也是定长，

  即每一单元值占的字节是固定的。

  核心且常用的字段，宜建成定长，放在一张表。

  而varchar,text,blob这种变长字段，适合单放一张表，用主键与核心表关联起来。

- 2、常用字段和不常用字段分离

  用户表：姓名，邮箱，头像几乎天天用，再说个人介绍就不常用，可以放在另外一个表。



- 3、在1对多，需要关联统计的字段上，添加冗余字段



**列类型选择**：

- 1、字段类型优先级：整型> date,time>enum,char>varchar>blob,text

  性别：tinyint() , 0,1,2 定长一个字节



- 2、够用就行，不要慷慨

  如年龄，用tinyint 就行，可以存储255岁，足够了，用int浪费了3个字节

  varchar(10),varchar(300)存储的内容相同，但在表联查时，varchar(300)要花更多内存



- 3、列的选择尽量避免用NULL

  原因：NULL不利于索引，要用特殊的字节来标注。

  在磁盘上占据的空间其实更大。（mysql5.7已对null做了改进，但是仍是不便）



### 索引优化策略

#### 索引类型

**btree索引**

> 就是一颗二叉树索引，一次查询的最多次数是二叉树的层级，对于范围查询也很好。
>
> 误区：在where条件常用得列上都加上索引，这是错误的，同时只能用1个索引，所以你给多列加索引并不能优化。
>
> 索引本身是有序的，可以提升排序， 



**hash索引**

> memory表李默认是hash，hash的理论查询实际复杂度为O(1)，
>
> 但是hash有地址冲突，
>
> 还可能造成磁盘很多空白空间，
>
> 无法利用前缀索引，排序也无法优化，



索引不是随便优化的，一个网站的优化不是说说的，你起码的观察一个星期，看看哪几个sql语句查询的最多，然后再建立联合索引进行优化。



左前缀索引，多列索引，当几列共同建立一个联合索引时候，索引发挥规则是从左往右的，如果断了，后面的列就用不了索引了。



#### 聚簇索引和非聚簇索引

**聚簇索引**

> innodb是把数据直接放在索引树里面了，所以是聚簇索引。次级索引指向主键索引
>
> - 1、主键索引，即存储索引值，又在叶子中存储行的数据
> - 2、如果没有主键，则会Unique key做主键
> - 3、如果没有unique，则系统生成一个内部的rowid做主键
> - 4、像innodb中，主键的索引结构中，即存储了主键值，又储存了行数据，这种结构成为“聚簇索引”
>
> 优势：根据主键查询条目比较少时，不用回行（数据就在主键节点下）
>
> 劣势：如果碰到不规则数据插入时，造成索引频繁的页分裂





**非聚簇索引**

> myisam是非聚簇索引，指的是数据和索引是分开的，索引只存储了数据的地址，指向行在磁盘上的位置。

**MyISAM更适合读密集的表，而InnoDB更适合写密集的的表。**



**索引覆盖**



**理想的索引**

- 1、查询频繁

- 2、区分度高

  100万用户，性别基本上男女各为50W，区分度就低

- 3、长度小

  索引长度直接影响索引文件的大小，影响增删改的速度，并间接影响查询速度（占用内存多）

- 4、尽量能覆盖常用字段



技巧1：

http://www.baidu.com

http://www.sina.com

这时候根据左前缀原则，就浪费了http://www.这11个前缀字节了，增加了索引的大小，会降低速度；

这时候可以采用把这个字符串进行倒叙后再存入数据库，这样左前缀就少了很多了。

技巧2：

直接把字符串通过hash算法生成一个整型数，然后把这个数存起来，把索引建立在这个整型数上。（当然会有重复的值，但是也比直接存字符串好）







### 索引和排序

- 对于索引覆盖，直接在索引上查询时，就是有顺序的，using index，

  在innodb中，沿着索引字段排序，也是自然有序的，对于myisam，如果按某索引字段排序，入id，但取出的字段中，有未索引字段，如goods_name,myisam的做法不是索引->回行，索引->回行，而是先取出所有行，再进行排序。

 

### 索引碎片和维护

通过nop操作（不产生对数据实质影响的操作），来修改表。

比如：表的引擎为innodb，可以alter table xxx engine innodb

optimize table 表名; 也可以修复。



### sql语句优化

等待时间，执行时间

执行时间又花在哪？

a：查找---> 沿着索引查找，慢着可能全表扫描

b：取出---> 查到行后，把数据取出来



如何查询快？

a：查询的快----联合索引的顺序，区分度，长度

b：取得快，索引覆盖

c：传输的少，更少的行和列



切分查询：按数据拆成多次

例：插入10000行数据，每1000条为单位，插入。

分解查询：按逻辑把多表连接查询分成多个简单的sql。



sql语句优化思路？

答：不查--->少查--->高效的查

不查：通过业务逻辑来计算；

少查：尽量精准数据，少取行；

必须要查：尽量走索引上查询行；

用explain来定量分析sql语句执行的效果

explain select * from users \G

这样会输出该sql执行的情况，是否使用了索引；

type：all最差，index较快，const那是极快了（达到常量级了）

extra：index是用到了索引覆盖，效率很高，useing where 是指光靠索引定位不来，还得where判断一下；using temporary 是指用上了临时表，不是很好；using filesort是指文件排序（文件可能在磁盘也可能在内存）





limit翻页优化

limit offset N 

- 1、从业务上解决：不允许翻过100页

- 2、不用offset，用条件查询：select id,name form user limit 5000000 limit 10;

  不过这样查询数据不能被物理删除过，否则就会有空洞。

  解决办法是平时删除时候可以用逻辑删除

