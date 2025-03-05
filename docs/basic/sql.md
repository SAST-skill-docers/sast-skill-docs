# 数据库 & SQL

## 一、关系型数据库

### 1. 什么是数据库？

所谓“数据库”是以一定方式储存在一起、能予多个用户共享、具有尽可能小的冗余度、与应用程序彼此独立的数据集合。应用程序只需调用数据库软件提供的 API 接口便可以实现数据的读写，而无需在意数据在文件中如何存储以及如何索引。同学们可在“数据库系统概论”课程深入学习数据的存储方式与检索方式。

根据数据模型进行分类，可以将数据库分为：

- **关系型数据库**，包括 [MySQL](https://www.mysql.com/)、[PostgreSQL](https://www.postgresql.org)、[SQLite](https://www.sqlite.org/)、[Oracle](https://www.oracle.com/database/) 等；
- **非关系型数据库（NoSQL）**，包括 [MongoDB](https://www.mongodb.com/)、[Redis](https://redis.io/)、[Apache Cassandra](https://cassandra.apache.org/) 等。

### 2. 什么是关系型数据库？

提到数据存储，我们很容易想到 Excel 表格。Excel 表格使用列表示属性、行表示不同的实例，这种方式确实能够很好地遍历和管理数据。而关系型数据库是一种建立在关系模型基础上的数据库，使用表（Table）来存储数据。关系型数据库中的表与 Excel 表格相似，由行和列组成，其中：

- 每一行代表一条**记录（Record）**，也就是一条完整的数据；
- 每一列代表一个**字段（Field）**，代表一条记录的某个属性。

与 Excel 不同的是，关系型数据库通过关系（Relations）将不同表中的数据连接起来，使得用户可以更方便地进行数据查询和操作。

### 3. 关系型数据库中的关系

关系型数据库中的“关系”是指表与表之间的连接或关联。这种关系通过特定的键（Key）来实现，主要包括以下几种类型：

- **一对一关系（One-to-One Relationship）**：每一行数据在一个表中与另一表中的一行数据相对应。例如，某公司的人事信息表与员工详细信息表，可能每个员工都有一个唯一的记录与其对应的详细信息。

- **一对多关系（One-to-Many Relationship）**：一个表中的一行数据可以与另一表中的多行数据相关联。这是最常见的关系类型。例如，一个部门表和员工表，一个部门可以有多个员工，但一个员工只能属于一个部门。

- **多对多关系（Many-to-Many Relationship）**：一个表中的多行数据可以与另一表中的多行数据相对应。例如，学生表和课程表，一个学生可以选修多门课程，一门课程也可以有多名学生选修。多对多关系通常通过一个中间表（关联表）来实现。

!!! note "note: 关系型数据库的优越性"

    为何要学习使用数据库，我们以学生和课程的关系为例：

    - 每位同学拥有 `[sid, name, major]` 三个属性；
    - 每个课程拥有 `[cid, title, credit, time]` 四个属性，对于每个同学都有 `grade` 属性。

    倘若用一张 Excel 表格来存储，假设有 10 个学生，每个学生均选择了 10 门相同的课程，那么 Excel 表格的数据存储量约为 $(3 + 5) \times 10 \times 10 = 800$，其中存在大量冗余。而在关系型数据库中，我们可以设计三张数据表（现阶段我们只需考虑 Field 与 Type 两列，每个 Field 表示一个字段：

    ```mysql
    mysql> DESC Students;
    +-------+--------------+------+-----+---------+----------------+
    | Field | Type         | Null | Key | Default | Extra          |
    +-------+--------------+------+-----+---------+----------------+
    | id    | int          | NO   | PRI | NULL    | auto_increment |
    | sid   | char(10)     | NO   | UNI | NULL    |                |
    | name  | varchar(255) | NO   |     | NULL    |                |
    | major | varchar(255) | NO   |     | NULL    |                |
    +-------+--------------+------+-----+---------+----------------+
    4 rows in set (0.00 sec)

    mysql> DESC Courses;
    +--------+--------------+------+-----+---------+----------------+
    | Field  | Type         | Null | Key | Default | Extra          |
    +--------+--------------+------+-----+---------+----------------+
    | id     | int          | NO   | PRI | NULL    | auto_increment |
    | cid    | varchar(8)   | NO   | UNI | NULL    |                |
    | title  | varchar(255) | NO   |     | NULL    |                |
    | credit | int          | NO   |     | NULL    |                |
    | time   | varchar(255) | YES  |     | NULL    |                |
    +--------+--------------+------+-----+---------+----------------+
    5 rows in set (0.00 sec)

    mysql> DESC Association;
    +------------+---------+------+-----+---------+----------------+
    | Field      | Type    | Null | Key | Default | Extra          |
    +------------+---------+------+-----+---------+----------------+
    | id         | int     | NO   | PRI | NULL    | auto_increment |
    | student_id | int     | YES  | MUL | NULL    |                |
    | course_id  | int     | YES  | MUL | NULL    |                |
    | grade      | char(2) | YES  |     | NULL    |                |
    +------------+---------+------+-----+---------+----------------+
    4 rows in set (0.00 sec)
    ```

    此时我们只需为每名同学和每个课程建立一行信息，并在 Association 中添加其关系即可。这里的  Association 就是中间表，用于存储同学与课程的选课关系，此时数据存储量约为 $4 \times 10 + 5 \times 10 + 4 \times 10 \times 10 = 490$，相比使用 Excel 减少了很多。以上就是一个多对多关系的数据库设计的简单示例，接下来我们将学习如何在这些数据表上进行所需操作。

## 二、 SQL 入门

!!! note

    本节中所讲授的 SQL 语言及其实现基于 MySQL 数据库管理系统。MySQL 是一种流行的开源关系型数据库管理系统（RDBMS），广泛应用于各种应用场景。 在本节中，所有的示例代码和说明均适用于 MySQL。如果你使用的是其他数据库管理系统（如 PostgreSQL、SQL Server 或 Oracle），请注意其中可能存在的差异。

!!! tip

    如果想要更多地了解 SQL 命令的使用方法和作用，除了在[官方文档](https://dev.mysql.com/doc/refman/8.0/en/)中查看，你也可以在 MySQL 的交互式命令行界面使用 `help` 来获取相关信息，如下

    ```mysql
    mysql> help USE;
    Name: 'USE'
    Description:
    Syntax:
    USE db_name
 
    The USE statement tells MySQL to use the named database as the default
    (current) database for subsequent statements. This statement requires
    some privilege for the database or some object within it.
 
    The named database remains the default until the end of the session or
    another USE statement is issued:
 
    USE db1;
    SELECT COUNT(*) FROM mytable;   # selects from db1.mytable
    USE db2;
    SELECT COUNT(*) FROM mytable;   # selects from db2.mytable
 
    The database name must be specified on a single line. Newlines in
    database names are not supported.
 
    URL: https://dev.mysql.com/doc/refman/8.0/en/use.html
    ```

### 1. SQL 是什么？

<img src="/static/basic/sql/sql.jpg" style="zoom:67%;" />

SQL 有如下语言特性：

- 脚本语言（类似 Python）：可以交互执行也可以事先写好后一次性执行；
- 弱类型，如：

```mysql
mysql> SELECT '1' + 1;
+---------+
| '1' + 1 |
+---------+
|       2 |
+---------+
1 row in set (0.00 sec)
```

- 关键字不区分大小写；而数据库名，表名，字段名等区分大小写。如：

```mysql
mysql> CrEaTe DaTaBaSe CaseInsensitive;
Query OK, 1 row affected (0.00 sec)

mysql> cReAtE dAtAbAsE caseInsensitive;
Query OK, 1 row affected (0.01 sec)
```

这样分别创建了 `CaseInsensitive` 和 `caseInsensitive` 两个数据库而没有出现冲突。

当出现数据库名，表名，字段名与关键字重复时，需要使用反引号 ` 将其包裹。为了美观与可读性，建议关键字统一使用大写表示。

!!! note "SQL 环境配置"

    关于 SQL 的环境配置过程，请依据你所选择的数据库管理系统，参考对应的官方文档。

### 2. 数据类型

由于数据库的核心便是“数据”，不同的数据类型会影响数据的存储方式与检索方式，因此为每张表的字段选择最合适的数据类型是十分有必要的。MySQL 支持的类型大致可以分为数值、日期/时间和字符串三类，在此仅列举常用的类型：

- INT：4 字节，取值范围为 $[-2^{31}, 2^{31} - 1]$
- BIGINT：8 字节，取值范围为 $[-2^{63}, 2^{63} - 1]$
- FLOAT：4 字节
- DOUBLE：8 字节
- 可使用 `UNSIGEND` 关键字来表示无符号类型，以表示更大的范围，例如：

```mysql
CREATE TABLE your_table (
    your_column INT UNSIGNED
);
```

- CHAR：定长字符串，256 字节，长度取值范围为 $[0,255]$ 个字符；
- VARCHAR：变长字符串，长度取值范围为 $[0,65535]$ 个字符；

- TIMESTAMP：时间戳，形式为 `YYYY-MM-DD hh:mm:ss`，4 字节，范围为 $[1970−01−01\ 00:00:01\  \mathrm{UTC},2038−01−19\ 03:14:07\ \mathrm{UTC}]$，常见的写法有：

```mysql
CREATE TABLE your_table (
    # ...
    event_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP	# 插入记录时默认为该条记录插入的时间，更新记录时自动更新为该条记录更新的时间
);
```

特别的，`NULL` 表示空值（与 `0` 和 `''` 作区分）。但字段在设置时应尽量“避免允许为 `NULL`”，这样可以简化查询条件，加快查询速度，也利于应用程序读取数据后无需判断其是否为 `NULL`。

### 3. 数据库的约束与优化

#### 3.1 主键

主键（Primary Key）是关系型数据库表中的一个或多个字段，这些字段具有以下特性：

- **唯一性**：主键的值必须是唯一的，即每一行的主键值都不相同。

- **非空性**：主键字段不能包含 `NULL` 值，每一行必须有一个有效的主键值。

主键用于唯一标识表中的每一行记录，是数据库设计中确保数据完整性和唯一性的关键部分。常见的可以用作主键字段的类型有：

- 自增整数类型（使用 `AUTO_INCREMENT` 来声明自增）；
- 全局唯一 GUID 类型：使用 GUID 算法生成一个全局唯一的字符串。

注意，尽管只要我们保证某一字段不重复，便可以将其作为主键，但我们不推荐将业务相关的字段作为主键，这主要时防止当其需要变更时出现关系上的错误。

例如，若我们将课程的课程号 `cid` 作为主键，尽管其是唯一的，但假如课程的课程号发生改变，或者其位数需要增加，那么在修改时我们还必须同时修改 `Association` 表中的内容，并且要确保没有重复的值或者确保在迁移数据时处理了重复值的情况。

我们可以如下声明一个主键：

```mysql
CREATE TABLE Students (
	id INT AUTO_INCREMENT PRIMARY KEY,
    # ...
);
```

除去将单一字段作为主键外，我们还可以使用**联合主键**，即用多个字段同时唯一确定某一条记录，联合主键满足**复合唯一性**：这些字段可以出现重复值，但这些字段的组合必须是唯一的。我们可以如下声明联合主键：

```mysql
CREATE TABLE Students (
	id INT，
    sid INT,
    # ...
    PRIMARY KEY (id, sid)
);
```

#### 3.2 外键

外键（Foreign Key）是在关系型数据库中用于建立和强化表之间的联系的一种约束。它是一列或多列，这些列中的值必须匹配另一张表中的主键或唯一键。外键的主要作用是确保数据的一致性和参照完整性。在 `Association` 数据表中，`student_id` 和 `course_id` 分别外链到 `Students` 和 `Courses` 两张数据表中的 `id` 属性。通过定义外键约束，关系型数据库可以保证无法插入无效的数据，即无法插入在 `Students` 表或 `Courses` 表中不存在的 `id`号。

外键除了能够约束不同表之间的数据关系，还可以配置级联更新和删除操作（使用 `CASCADE` 声明）。例如，如果在 `Students` 表中删除了一个同学的记录，可以自动删除所有关联的 `Association` 记录（级联删除），或者更新其中的 `student_id` 为新的值（级联更新）。

然而，外键约束会降低数据库的性能，所以在应用程序可以保证逻辑正确性的前提下，我们可以不设置外键约束。此时 `student_id` 和 `course_id` 只是两个普通的字段，但是其在使用逻辑上起到了外键的作用。

我们可以如下声明一个外键：

```mysql
CREATE TABLE Association (
	student_id INT,
    # ...
    CONSTRAINT fk_student_id FOREIGN KEY (student_id) REFERENCES Students(id)
);
```

#### 3.3 其他约束

SQL 中的约束（Constraint）是用来限定表中的数据，以保证数据的准确性和完整性，上面提到的主键和外键都属于约束。约束可以在表级别或列级别定义，除了主键和外键以外，常见的约束有：

- `UNIQUE`：唯一约束用于确保一列或多列的值在表中唯一，除了 NULL 值。
- `NOT NULL`：非空约束用于确保列不能有 NULL 值。
- `CHECK`：检查约束用于确保列中的值满足指定的条件。需要注意的是，MySQL 对 CHECK 约束的支持有限，在 8.0.16 之前的版本可能不完全支持。以下是 `CHECK` 约束的一个例子：

```mysql
CREATE TABLE Association (
	# ...
    grade CHAR(2),
    CONSTRAINT check_grade CHECK(grade IN ('A+', 'A', 'A-'))	# 确保 grade 字段是三者之一
);
```

- `DEFAULT`：默认约束用于为列设置默认值，如果插入数据时没有指定该列的值，则使用默认值。

#### 3.4 索引

在汉语词典中，我们可以按照拼音、笔画、偏旁部首等方式进行索引，通过这些方式进行索引通常可以获得更快的检索速度。

“索引”这一概念对于数据表也是这样。倘若我们为某一个字段建立了索引（为某种方式在词典中建立了目录），那么在通过这一字段进行检索时便通常可以获得更快的查询速度。关系型数据库会自动为主键建立索引，由于主键会保证绝对唯一，因此采用主键索引往往是效率最高的。对于其他字段，当其重复项不多且常常依赖其进行查询时，我们也可以考虑为其创建索引，例如 `Studens` 表中的 `sid` 和 `Courses` 表中的 `cid`。

我们可以如下为一个字段建立索引：

```mysql
CREATE TABLE Students (
	sid CHAR(10) NOT NULL UNIQUE,
    # ...
    INDEX idx_sid (sid)
);
```

也可以在数据表创建完成后再为某个字段建立索引：

```mysql
CREATE INDEX idx_sid ON Students (sid);
```

然而，由于在插入、更新和删除记录时，需要同时修改索引，因此索引会减慢插入、更新和删除记录的速度。

### 4. 数据库与数据表操作

#### 4.1 CREATE

如下创建新的数据库，然后通过 `USE` 命令来一个数据库作为当前会话的默认数据库。

```mysql
CREATE DATABASE university;

USE university;	# 执行这个命令后，所有后续的数据库操作都会在 university 上进行，直到会话结束或者你选择另一个数据库。
```

然后创建上面 `note` 提到的三个数据表 `Students`、`Courses`、`Association`：

```mysql
CREATE TABLE Students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    sid CHAR(10) NOT NULL UNIQUE,
    name VARCHAR(255) NOT NULL,
    major VARCHAR(255) NOT NULL,
);

CREATE TABLE Courses (
    id INT AUTO_INCREMENT PRIMARY KEY,
    cid VARCHAR(8) NOT NULL UNIQUE,
    title VARCHAR(255) NOT NULL,
    credit INT NOT NULL,
    time VARCHAR(255)
);

CREATE TABLE Association (
    id INT AUTO_INCREMENT PRIMARY KEY,
    student_id INT,
    course_id INT,
    grade CHAR(2),
    CONSTRAINT fk_student_id FOREIGN KEY (student_id) REFERENCES Students(id),
    CONSTRAINT fk_course_id FOREIGN KEY (course_id) REFERENCES Courses(id)
);
```

你可以使用以下命令分别列出所有的数据库和当前会话的默认数据库中的所有数据表：

```mysql
SHOW DATABASES;

SHOW TABLES;
```

你可以使用 `DESC`（Describe）来查看表的字段情况：

```mysql
DESC Association;
```

#### 4.2 ALTER

`ALTER` 用于给数据表添加、删除字段，修改字段的类型或属性，例如：

```mysql
ALTER TABLE Students
ADD gender VARCHAR(10); -- 添加字段

ALTER TABLE Students
DROP COLUMN gender; -- 删除字段

ALTER TABLE Students
MODIFY COLUMN sid CHAR(20); -- 修改字段

ALTER TABLE Students
ADD INDEX idx_sid (sid); -- 建立索引

ALTER TABLE Association
DROP FOREIGN KEY fk_student_id; -- 删除外键
```

#### 4.3 DROP

`DROP` 可以用来删除数据库或数据库，如下：

```mysql
DROP TABLE Students;

DROP DATABASE university;
```

### 5. CRUD

> In computer programming, **create, read, update, and delete** (**CRUD**) are the four basic operations of [persistent storage](https://en.wikipedia.org/wiki/Persistent_storage).

每个字母代表一个操作：

|  CRUD  |  SQL   |
| :----: | :----: |
| Create | INSERT |
|  Read  | SELECT |
| Update | UPDATE |
| Delete | DELETE |

#### 5.1 INSERT

`INSERT` 用来向数据表中插入新记录，一个简单的用法示例如下：

```mysql
INSERT INTO Students VALUES (0, '2024000000', 'Alice', 'CS');
```

我们也能够指定字段来进行赋值，若某个字段没有被赋值，那么它会被赋予默认值（若 `DEFAULT` 未被声明，则为 NULL）：

```mysql
INSERT INTO Students (sid, name, major) VALUES
('2024000000', 'Alice', 'CS');
```

一次插入也可以插入多条记录：

```mysql
INSERT INTO Students (sid, name, major) VALUES
('2024000000', 'Alice', 'CS'),
('2024000001', 'Bob', 'EE');
```

#### 5.2 SELECT

`SELECT` 用来在数据库中查询记录，是 MySQL 中的核心操作，本节将就重要的查询选项来讲解。

##### 5.2.1 基本操作

暴力地取出整张表：

```mysql
SELECT * FROM Students;
```

在生产环境中使用该操作应谨慎，因为若记录特别多或某个字段特别长，这将消耗大量的网络传输和内存消耗，且可能出现数据暴露等问题。

我们也可以查看指定的列：

```mysql
SELECT sid, name, major FROM Students;
```

##### 5.2.2 WHERE

`WHERE` 可以用来筛选查询的记录，例如查找所有学号在 2024500000 之后的学生：

```mysql
SELECT * FROM Students WHERE sid > 2024500000;
```

`WHERE` 后的式子被称为条件表达式，除了如上面这个例子那样简单的写法，还有其他不同的写法。

###### 5.2.2.1 IN

在使用 `WHERE` 进行过滤时我们可以指定范围，如我们只想查询专业为物理或生物的学生：

```mysql
SELECT * FROM Students WHERE major IN ('Physics', 'Biology');
```

###### 5.2.2.2 LIKE

可以使用 `LIKE` 进行模糊匹配，例如查找所有姓名以 A 开头的学生：

```mysql
SELECT * FROM Students WHERE name LIKE 'A%';
```

更多例子可以参见 [SQL LIKE Operator (w3schools.com)](https://www.w3schools.com/sql/sql_like.asp), [SQL Wildcard Characters (w3schools.com)](https://www.w3schools.com/sql/sql_wildcards.asp)

###### 5.2.2.3 NULL

由前所述，没有被分配值且没有默认值得字段在插入时会被分配 `NULL` 这个空值来表示记录中的某个字段没有数据，可以使用 `ISNULL` 来进行判断。如找出所有没有登记分数得选课记录：

```mysql
SELECT * FROM Association WHERE ISNULL(grade);
```

注意不能使用 `grade = NULL` 来判断是否为 `NULL`：

```mysql
SELECT * FROM Association WHERE grade = NULL;
```

判断非空可以使用 `NOT ISNULL(<column_name>)`，如

```mysql
SELECT * FROM Association WHERE NOT ISNULL(grade);
```

而不是

```mysql
SELECT * FROM Association WHERE grade <> NULL;
```

###### 5.2.2.4 AND, OR, NOT

我们可以使用 `AND`、`OR`、`NOT` 来组合不同的逻辑表达式形成符合我们要求的条件表达式，例如筛选出所有姓名以 D 开头且专业不是计算机科学或者数学的学生：

```mysql
SELECT * FROM Students WHERE name LIKE 'D%' AND NOT major IN ('Computer Science', 'Mathematics');
```

##### 5.2.3 COUNT, MAX, MIN, SUM, AVG

除了直接展示我们查询出来的数据，我们也能通过这些聚合函数对数据进行简单地进一步处理，如查看学生中最小的学号：

```mysql
SELECT MIN(sid) FROM Students;
```

其他的几个函数：计数、最大值、求和、平均值，也可以像上面这个例子一样使用。

##### 5.2.4 GROUP BY

我们想看看在所有学生中不同的专业分别有多少人：

```mysql
SELECT major, COUNT(*) FROM Students GROUP BY major;
```

在查询中，我们可以给字段、表、和查询赋予一个临时的，仅在这次查询中使用的名称。

例如对于上面的查询，我们给 `COUNT(*)` 一个临时名称 `student_count`：

```mysql
SELECT major, COUNT(*) student_count FROM Students GROUP BY major;
```

在 `GROUP BY` 时，你通常不能选择没有受到 `GROUP BY` 修饰的字段，例如如下查询：

```mysql
mysql> SELECT sid, major, COUNT(*) student_count FROM Students GROUP BY major;
ERROR 1055 (42000): Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'university.Students.sid' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
```

这是因为同一个 `major` 可能包括多个 `sid`，因此其定义不良好。

当我们希望选择其他列时，可以考虑使用后面将要讲到的 `JOIN` 选项。

##### 5.2.5 HAVING

可能想要对聚合算子的结果进行过滤，比如试图选出人数大于 10 个的专业：

```mysql
mysql> SELECT major, COUNT(*) FROM Students GROUP BY major WHERE COUNT(*) > 10;
ERROR 1111 (HY000): Invalid use of group function
```

此时我们可以用 `HAVING` 来过滤聚合算子的结果：

```mysql
SELECT major, COUNT(*) FROM Students GROUP BY major HAVING COUNT(*) > 10;
```

##### 5.2.6 DISTINCT

当我们希望查询的结果中没有重复值时，可以使用 `DISTINCT`，例如专业的种类：

```mysql
SELECT DISTINCT major FROM Students;
```

##### 5.2.7 ORDER BY

当我们希望对查询结果排序时，可以使用 `ORDER BY`，例如对课程的学分进行排序：

```mysql
SELECT title, credit FROM Courses ORDER BY credit;
```

默认结果为升序，我们也可以规定降序：

```mysql
SELECT title, credit FROM Courses ORDER BY credit DESC;
```

我们也可以对多个字段排序，如优先按照学分降序，再按照课程名升序：

```mysql
SELECT title, credit FROM Courses ORDER BY credit DESC, title ASC;
```

也可以对 `GROUP BY` 的结果排序，例如按照获得某等级的学生个数排升序：

```mysql
SELECT grade, COUNT(*) student_count FROM Association GROUP BY grade ORDER BY student_count;
```

##### 5.2.8 LIMIT

`LIMIT` 可以对结果指定偏移与范围，比如我们查找学号最小的 10 位学生：

```mysql
SELECT sid, name FROM Students ORDER BY sid LIMIT 10;
```

假如这样的查询是要分给一个后端，每页10个，我们想要去查第 4 页的结果：

```mysql
SELECT sid, name FROM Students ORDER BY sid LIMIT 30, 10;
```

表示从第 30 位学生开始，查询 10 条记录。

##### 5.2.9 Sub Query

对于每次查询得到的结果集合，我们可以将其视为一个临时的数据表，可以（必须）对他起一个临时名称（别名）后继续进行 `SELECT` 等操作。而这样嵌套在查询中的查询称为 `Sub Query`，具有很强的表达能力，而且十分符合人类的思维直觉。

例如我们希望查询选课超过了平均值的学生的选课记录，可以将查询分为 3 步：

1. 查询学生选课的数量：

   ```mysql
   SELECT COUNT(*) cnt FROM Association GROUP BY student_id;
   ```

2. 对这些数量求平均：

   ```mysql
   SELECT AVG(q1.cnt) FROM (
       SELECT COUNT(*) cnt FROM Association GROUP BY student_id
   ) q1;
   ```

   其中 `q1` 是对第一个子查询的别名

3. 从 `Association` 表中筛选出相应的记录：

   ```mysql
   SELECT student_id, COUNT(*) cnt FROM Association GROUP BY student_id HAVING cnt > (
       SELECT AVG(q1.cnt) FROM (
           SELECT COUNT(*) cnt FROM Association GROUP BY student_id
       ) q1
   );
   ```

子查询也可以用在 `WHERE` 语句中，例如希望查询选课数量排前 10 名学生的全部信息，可以将查询分为 3 步：

1. 从 `Association` 表中查出选课数量排前 10 名的学生的 `id`：

   ```mysql
   SELECT student_id, COUNT(*) cnt
   	FROM Association
   	GROUP BY student_id
   	ORDER BY cnt DESC
   	LIMIT 10;
   ```

2. 从中选出 `id`：

   ```mysql
   SELECT student_id FROM (
   	SELECT student_id, COUNT(*) cnt
           FROM Association
           GROUP BY student_id
           ORDER BY cnt DESC
           LIMIT 10
   ) top_s;
   ```

3. 从 `Students` 表中查出这些同学的信息：

   ```mysql
   SELECT * FROM Students WHERE id in (
   	SELECT top_s.student_id FROM (
           SELECT student_id, COUNT(*) cnt
               FROM Association
               GROUP BY student_id
               ORDER BY cnt DESC
               LIMIT 10
       ) top_s
   );
   ```

##### 5.2.10 JOIN

JOIN 是我们可以对多个表的结果按照某些列的约束关系进行拼接，常见的模式有：

<img src="/static/basic/sql/join.png" style="zoom:75%;" />

例如我们希望查看在选课记录中查看成绩的同时显示学生的名字：

```mysql
SELECT Students.name, course_id, grade
	FROM Students
	INNER JOIN Association
	ON Students.id = student_id AND NOT ISNULL(grade);
```

利用 `Sub Query` 同时显示课程名和学分，并按学生姓名排序：

```mysql
SELECT StuWithCouseID.name student, Courses.title, Courses.credit, StuWithCouseID.grade
	FROM Courses
	INNER JOIN (
    	SELECT Students.name, course_id, grade
            FROM Students
            INNER JOIN Association
            ON Students.id = student_id AND NOT ISNULL(grade)
    ) StuWithCouseID
    ON Courses.id = StuWithCouseID.course_id
    ORDER BY student;
```

`INNER JOIN` 给出了两张表在指定字段交集上的笛卡尔积，用某些关键字将表衔接起来，你可以认为 `INNER JOIN` 等价于：

```python
results = []
for record1 in TABLE1:
    for record2 in TABLE2:
        if P(record1, record2):
        	results += [record1.concat(record2)]
return result
```

也就是选出两张表笛卡尔积中符合谓词 `P` 的记录集合，数据库在执行时并不是这样实现的。

因此上面的 `INNER JOIN` 可以隐式表达为：

```mysql
SELECT Students.name, course_id, grade
	FROM Students, Association
	WHERE Students.id = Association.student_id AND NOT ISNULL(Association.grade);
```

```mysql
SELECT Students.name student, Courses.title, Courses.credit, Association.grade
	FROM Students, Courses, Association
	WHERE Students.id = Association.student_id AND Courses.id = Association.course_id AND NOT ISNULL(Association.grade)
	ORDER BY student;
```

对于 `INNER JOIN`，我们可以认为其与 `WHERE =` 获得的结果一样，但实现原理却并不相同，`JOIN` 时使用 `hashtable` 进行比较，而 `WHERE =` 则是取笛卡尔积再过滤（即上述 `Python` 表示的代码）。因此前者效率为 $\mathrm{O}(\log \mathrm{N})$，后者的效率为 $\mathrm{O}(N^2)$

##### 5.2.11 Functions

作为一门（编程）语言，MySQL 提供了[极为丰富的函数库](https://dev.mysql.com/doc/refman/8.0/en/functions.html)，下面举几个字符串函数的例子。

###### 5.2.11.1 SUBSTRING

查看姓名从第 1 个字符开始的 3 个字符：

```mysql
SELECT SUBSTRING(name, 1, 3) FROM Students;
```

###### 5.2.11.2 CHAR_LENGTH

看看谁的名字比较长：

```mysql
SELECT CHAR_LENGTH(name) name_len, name
	FROM Students
	ORDER BY name_len DESC
	LIMIT 10;
```

###### 5.2.11.3 REPLACE

对字符串进行替换，比如我认为 `S` 是敏感词，在查询时将其替换为 `*`：

```mysql
SELECT cid, REPLACE(title, 'S', '*') FROM Courses;
```

#### 5.3 UPDATE

例如我要更新软件工程课程的学分：

```mysql
UPDATE Courses SET credit = 5 WHERE title = 'SE';
```

`UPDATE` 过程中可以直接使用表中的值：

```mysql
UPDATE Courses SET credit = credit + 1 WHERE title = 'SE';
```

#### 5.4 DELETE

删除软件工程课程的选课记录：

```mysql
DELETE Association
	FROM Association, Courses
	WHERE Association.course_id = Courses.id AND Courses.title = 'SE';
```

在多表删除中，只有 `DELETE` 和 `FROM` 之间列举的表中的记录会被删除，`FROM` 后出现但是不在 `DELETE` 和 `FROM` 之间的表仅用作查询的参考。

值得注意的是删除并不会立即释放磁盘空间：

```mysql
SHOW TABLE STATUS LIKE 'Association';
```

其中 `Data_length` 为数据文件的大小（以字节为单位），`Index_length` 为索引文件的大小（以字节为单位），`Data_length + Index_length` 表示表的总大小。

我们删除一些数据：

```mysql
DELETE FROM Association;
```

在此查询，发现即使 `Association` 空了，但是表的大小没有发生变化，为了释放空间，我们可以使用：

```mysql
OPTIMIZE TABLE Association;
# or
ANALYZE TABLE Association;
```

### 6. EXPLAIN

你可能关心查询是如何被数据库执行的，这是可以使用 `EXPLAIN ANALYZE`，让 MySQL 打印执行计划，例如：

```mysql
EXPLAIN ANALYZE
SELECT Students.sid, COUNT(Association.student_id)
	FROM Students
	LEFT JOIN Association
	ON Students.id = Association.student_id
	GROUP BY Students.id;
```

### 7. ORM

> 本节内容在暑培 Django 课程会讲到 ~~（应该吧）~~，这里只做简单的介绍

在我们实际开发中，通常会直接使用成熟的后端框架来操作数据库，例如 Django 等，在这些框架中，你无需手写 SQL 语句，而可以直接使用它的 ORM 框架（或者其他框架）。

ORM 全称为 Objected Relational Mapping，其构造了数据库对象与 Python 对象中的如下映射：

```
TABLE -> Class
Record -> Instance（实例）
Column -> Attribute（属性）
```

实际上，ORM 在执行操作时依然会将对应的操作转换成数据库原生语句，但是其拥有如下优点：

<img src="/static/basic/sql/orm.png" style="zoom:80%;" />

除了 Django 提供的 ORM 框架外，不同编程语言也有其他的 ORM 框架：

- Python：[SQLAlchemy](https://www.sqlalchemy.org/)
- Rust：[Diesel](https://diesel.rs/)、[SeaORM](https://www.sea-ql.org/SeaORM/)
- Node.js：[Sequelize](https://sequelize.org/)、[TypeORM](https://typeorm.io/)；
- Java：[Hibernate](https://hibernate.org/)；
- Go：[GORM](https://gorm.io/)。

### 8. SQL 注入攻击

SQL 注入攻击（SQL Injection）是一种网络攻击手段，攻击者通过在输入字段中插入恶意的 SQL 代码来操纵数据库，从而访问、修改或删除数据。攻击的目标通常是那些没有对用户输入进行适当验证和过滤的Web应用程序。

#### 7.1 基本原理

当一个 Web 应用程序直接将用户输入拼接到 SQL 查询中并执行时，攻击者可以通过特意构造的输入来改变 SQL 查询的语义。例如，假设有一个登录功能，其 SQL 查询如下：

```mysql
SELECT * FROM users WHERE username = 'user' AND password = 'pass';
```

如果用户输入的 `username` 为 `' OR '1'='1`，则生成的SQL查询变为：

```mysql
SELECT * FROM users WHERE username = '' OR '1'='1' AND password = 'pass';
```

由于 `'1'='1'` 总是为真，这个查询将绕过验证，导致攻击者可以登录系统。

#### 7.2 预防措施

- 对数据进行转义，永远不要信任用户的输入，要对用户的输入进行校验，可以通过正则表达式，或限制长度，对单引号和双”-“进行转换等；
- 永远不要使用动态拼装 SQL，可以使用参数化的 SQL 或者直接使用存储过程进行数据查询存取；
- 不要把机密信息明文存放，请加密或者 hash 掉密码和敏感的信息；
- 使用 ORM（对象关系映射）。

### 9. mycli

在使用 MySQL 的交互命令行的过程中，我们可以发现，它并不支持现代 IDE 拥有的功能：语法高亮和自动补全。mycli 是一个用于 MySQL 的增强型命令行客户端，它集成了语法高亮、自动补全以及其他便利功能（比如进行破坏性行为需要进行确认），使得在命令行环境中操作 MySQL 更加高效和方便。

mycli 的效果图如下，详情可以在 [dbcli/mycli: A Terminal Client for MySQL with AutoCompletion and Syntax Highlighting](https://github.com/dbcli/mycli) 中查看。

<img src="/static/basic/sql/mycli.png" style="zoom:80%;" />

## 三、 参考资料

- 2023 年酒井科协暑培陈奕韬学长的[讲义](https://summer24.net9.org/basic/sql/handout/)；
- 2023 年酒井科协暑培刘铠铭学长的[讲义](https://summer23.net9.org/basic/sql/)；
- [Web安全-SQL注入攻击](https://jkfhto.github.io/2019-10-19/Web%E5%89%8D%E5%90%8E%E7%AB%AF%E6%BC%8F%E6%B4%9E%E5%88%86%E6%9E%90%E4%B8%8E%E9%98%B2%E5%BE%A1/Web%E5%AE%89%E5%85%A8-SQL%E6%B3%A8%E5%85%A5%E6%94%BB%E5%87%BB/)；
- [数据库 - 维基百科](https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E5%BA%93)。
