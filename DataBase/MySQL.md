# 第一章 初识`MySQL`数据库
## 第一节 数据库操作

### 1. 创建数据库的语法

```sql
CREATE DATABASE [IF NOT EXISTS] 数据库名称 DEFAULT CHARACTER SET 字符集 COLLATE 排序规则;
```

<font color="blue" >实例</font>：创建数据库lesson，并指定字符集为`GBK`，排序规则为`GBK_CHINESE_CI` 

```sql
CREATE DATABASE IF NOT EXISTS lesson DEFAULT CHARACTER SET GBK COLLATE GBK_CHINESE_CI;
```

### 2.修改数据库的语法

```sql
ALTER DATABASE 数据库名称 CHARACTER SET 字符集 COLLATE 排序规则;
```

<font color="blue" >实例</font>：修改数据库lesson，并指定字符集为`UTF8`，排序规则为`UTF8_GENERAL_CI` 

```sql
ALTER DATABASE lesson CHARACTER SET UTF8 COLLATE UTF8 GENERAL_CI;
```

### 3.删除数据库的语法

```sql
DROP DATABASE [IF EXISTS] 数据库名称;
```

<font color="blue" >实例</font>：删除数据库lesson

```sql
DROP DATABASE IF EXISTS lesson;
```

### 4.查看数据库语法

```sql
SHOW DATABASE;
```

### 5.使用数据库语法

```sql
USE 数据库名称;
```

<font color="blue" >实例</font>：使用数据库lesson

```sql
USE lesson;
```



## 第二节 列类型

在`MySQL`中，常用列类型主要为数值类型、日期时间类型、字符串类型

### 1. 数值类型

| `tinyint` | `smallint` | `mediumint` | `int` | `bigint` |   `float`   |  `double`   |  `decimal`  |
| :-------: | :--------: | :---------: | :---: | :------: | :---------: | :---------: | :---------: |
|   1字节   |   2字节    |    3字节    | 4字节 |  8字节   | 4字节(浮点) | 8字节(浮点) | m字节(浮点) |

> 注：`decimal(m, d)` 为字符串存储的浮点数，其中m表示总位数，d表示保留小数位数

### 2. 日期时间类型

| `DATE`(日期) | `TIME`(时间) |     `DATETIME`      | `TIMESTAMP(时间戳)` | `YEAR` |
| :----------: | :----------: | :-----------------: | :-----------------: | :----: |
| `YYYY-MM-dd` |  `HH:mm:ss`  | `YY-MM-dd HH:mm:ss` | `YY-MM-dd HH:mm:ss` | `YYYY` |

### 3. 字符串类型

| 类型           | 说明                                    | 最大长度    |
| :------------- | :-------------------------------------- | :---------- |
| `char`[(M)]    | 固定长字符串，索引快但费空间，0<=M<=255 | M字符       |
| `varchar`[(M)] | 可变字符串，0<=M<=65535                 | 变长度      |
| `text`         | 文本串                                  | 2^16^-1字节 |

> 注： `char(50)` ：不论插入值占用多少位空间，在数据库中都会占50个长度。比如"男"
>
> ​		`varchar(50)`：最大占用50个长度。比如"男"占用1个
>
> ​		`text`：用于存储长文本

### 4. 列类型修饰属性

| 类型             | 说明                                                         | 示例                                                         |
| :--------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `UNSIGNED`       | 无符号，只能修饰数值类型，表明该列数据不能出现负数           | `UNSIGNED` INT(4)，表示只能为4位大于等于0整数                |
| `ZEROFILL`       | 不足的位数使用0                                              | INT(4) `ZEROFILL`，如果给定的值位10，此时只有2位，而该列需要4位，不足的2位由0来填充，最终值为0010 |
| `NOT NULL`       | 表示该列类型的值不能为空                                     | `VARCHAR`(20) NOT NULL，表示该列不能为空值                   |
| `DEFAULT`        | 表示设置默认值                                               | INT(4) DEFAULT 0，表示该列不赋值时默认为0                    |
| `AUTO_INCREMENT` | 表示自增长，只能应用于数值列类型，该列类型必须为键，且不能为空 | INT(11) AUTO_INCREMENT NOT NULL PRIMARY KEY。第一次为该列中插入值时为1，第二次为2，以此类推 |



## 第三节 数据表操作

### 1. 数据表类型

`MySQL`中的数据表类型由许多，如`MyISAM`、`InnoDB`、`HEAP`、`BOB`、`CSV`等。其中最常用的就是`MyISAM`和`InnoDB`

#### `MyISAM`和`InnoDB`的区别

| 名称       | `MyISAM` | `InnoDB`    |
| :--------- | :------- | :---------- |
| 事务处理   | 不支持   | 支持        |
| 数据行锁定 | 不支持   | 支持        |
| 外键约束   | 不支持   | 支持        |
| 全文索引   | 支持     | 不支持      |
| 表空间大小 | 较小     | 较大，约2倍 |

事务：涉及的所有操作是一个整体，要么都执行，要么都不执行。

数据行锁定：一行数据，当一个用户在修改该数据时，可以将该条数据锁定。



> 注：如何选择数据表的类型?

```te
当涉及的业务操作以查询居多，修改和删除较少时，可以使用 MyISAM 。当涉及的业务操作经常会有修改和删除操作时使用 InnoDB
```

### 2. 创建数据表

```sql
CREATE TABLE [IF NOT EXISTS] 数据表名称(
	字段名1 列类型(长度) [修饰属性] [键/索引] [注释],
    字段名2 列类型(长度) [修饰属性] [键/索引] [注释],
    字段名3 列类型(长度) [修饰属性] [键/索引] [注释],
    ......
    字段名n 列类型(长度) [修饰属性] [键/索引] [注释]
) [ENGINE = 数据表类型][CHARSET = 字符集编码] [COMMENT = 注释];
```

<font color=blue>示例</font>：创建学生表，表中有字段学号、姓名、性别、年龄和成绩

```sql
CREATE TABLE IF NOT EXISTS student(
	`number` VARCHAR(30) NOT NULL PRIMARY KEY COMMENT '学号，主键',
	name VARCHAR(30) NOT NULL COMMENT '姓名',
	sex TINYINT(1) UNSIGNED DEFAULT 0 COMMENT '性别：0-男 1-女 2-其他',
	age TINYINT(3) UNSIGNED DEFAULT 0 COMMENT '年龄',
	score DOUBLE(5, 2) UNSIGNED DEFAULT NULL COMMENT '成绩'
)ENGINE=InnoDB CHARSET=UTF8 COMMENT='学生表'; 
```

### 3. 修改数据表

- 修改表名

  ```sql
  ALTER TABLE 表名 RENAME AS 新表名;
  ```

  <font color=blue>示例</font>：将`student`表名修改为`stu`

  ```sql
  ALTER TABLE student RENAME AS stu;
  ```

- 增加字段

  ```sql
  ALTER TABLE 表名 ADD 字段名 列类型(长度) [修饰属性] [键/索引] [注释];
  ```

  <font color=blue>示例</font>：在`stu`中添加字段联系电话(phone)，类型为字符串，长度为11，非空

  ```sql
  ALTER TABLE stu ADD phone VARCHAR(11) NOT NULL COMMENT '联系电话';
  ```

- 查看表结构

  ```sql
  DESC 表名; --查看表结构
  ```

- 修改字段

  ```sql
  -- MODIFY 只能修改字段的修饰属性
  ALTER TABLE 表名 MODIFY 字段名 列类型(长度) [修饰属性] [键/索引] [注释];
  -- CHANGE 可以修改字段的名字以及修饰属性
  ALTER TABLE 表名 CHANGE 字段名 新字段名 列类型(长度) [修饰属性] [键/索引] [注释];
  ```

  <font color=blue>示例</font>：将`stu`表中的sex字般的类型设置为`VARCHAR`，长度为2，默认值为男，注释为"性别，男，女，其他"

  ```sql
  ALTER TABLE stu MODIFY sex VARCHAR(2) DEFAULT '男' COMMENT '性别：男，女，其他'; 
  ```
  
  <font color=blue>示例</font>：将`stu`表中的`phone`字段修改为`moblie`，属性保持不变

  ```sql
  ALTER TABLE stu CHANGE phone mobile VARCHAR(11) NOT NULL COMMENT '联系电话';
  ```
  
  

- 删除字段

  ```sql
  ALTER TABLE 表名 DROP 字段名;
  ```

  <font color=blue>示例</font>：将`stu`表中的`mobile`字段删除

  ```sql
  ALTER TABLE stu DROP mobile;
  ```


- 删除数据表

  ```sql
  DROP TABLE [IF EXISTS] 表名;
  ```

  <font color=blue>示例</font>：删除数据表`stu`

  ```sql
  DROP TABLE IF EXISTS stu;
  ```

  

## 第四节 综合练习

**1. 在数据库exercise中创建课程表`stu_course`，包含字段课程编号(number)，类型为整数，长度为11，是主键，自增长，非空；课程名称(name)，类型为字符串，长度为20，非空；学分(score)， 类型为浮点数，小数点后面保留2位有效数字，长度为5， 非空**

   ```sql
	--如果数据库不存在则创建数据库
	CREATE DATABASE IF NOT EXISTS exercise DEFAULT CHARACTER SET UTF8 COLLATE UTF8_GENERAL_CI;
	--使用数据库
	USE exercise;
	--在数据库中创建数据表stu_course
	CREATE TABLE IF NOT EXISTS stu_course(
		`number` INT(11) AUTO_INCREMENT NOT NULL PRIMARY KEY COMMENT '课程编号',
	    name VARCHAR(20) NOT NULL COMMENT '课程名称',
	    score DOUBLE(5, 2) NOT NULL COMMENT '学分'
	)ENGINE=InnoDB CHARSET=UTF8 COMMENT '课程表';
   ```

**2. 将课程表重命名为course**

```sql
ALTER TABLE stu_course RENAME AS course;
```

**3. 在课程表中添加字段学时(time)，类型为整数，长度为3，非空**

```sql
ALTER TABLE course ADD `time` INT(3) NOT NULL COMMENT '学时';
```

**4. 修改课程表学分类型为浮点数，小数点后面保留1位有效数字，长度为3，非空**

```sql
ALTER TABLE course MODIFY score DOUBLE(3, 1) NOT NULL COMMENT '学时';
```

**5. 删除课程表**

```sql
DROP TABLE IF EXISTS course;
```

**6. 删除数据库exercise**

```sql
DROP DATABASE IF EXISTS exercise;
```

---



# 第二章 `MySQL`数据库的增删改查



## 第一节 `DML`语句

### 1. 什么是`DML`

`DML`为Data Manipulation Language，表示数据操作语言。主要体现于对表数据的增删改操作。因此`DML`仅包括`INSERT`、`UPDATE`和`DELEETE`语句。

### 2. INSERT语句

```sql
-- 需要注意，VALUES后的字段值必须与表名后的字段名一一对应
INSERT INTO 表名(字段名1, 字段名2, ..., 字段名n) VALUES(字段值1, 字段值2, ..., 字段值n);
-- 需要注意，VALUES后的字段值必须与创建表时的字段顺序保持一一对应
INSERT INTO 表名 VALUES(字段值1, 字段值2, ..., 字段值n);
-- 一次性插入多条数据
INSERT INTO 表名(字段名1, 字段名2, ..., 字段名n) VALUES(字段值1, 字段值2, ..., 字段值n),(字段值1, 字段值2, ..., 字段值n), ... , (字段值1, 字段值2, ..., 字段值n);
INSERT INTO 表名 VALUES(字段值1, 字段值2, ..., 字段值n), (字段值1, 字段值2, ..., 字段值n), ..., (字段值1, 字段值2, ..., 字段值n);
```

<font color=blue>示例</font>：向课程表中插入数据

```sql
INSERT INTO course (`number`, name, score, `time`) VALUES (1, 'Java', 8, 80);
INSERT INTO course VALUE (2, 'Golang', 6, 60);
INSERT INTO course (`number`, score, name, `time`) VALUES (3, 5, 'Python', 50);
INSERT INTO course (`number`, name, score, `time`) VALUES (4, 'C/Cpp', 3, 30),(5, 'Spring', 4, 40);
INSERT INTO course VALUE (6, 'SpringMVC', 4 ,40),(7, 'SpringBoot', 5, 50);
```

### 3. UPDATE语句

```sql
UPDATE 表名 SET 字段名1=字段值1[,字段名2=字段值2, ..., 字段名n=字段值n] [WHERE 修改条件];
```

#### 3.1 WHERE条件子句

3.1 `WHERE`条件子句
在`Java`中，条件的表示通常都是使用关系运算符来表示，在`SQL`语句中也是一样，使用 >, <, >=, <=, != 来表示。不同的是，除此之外，`SQL`中还可以使用`SQL`专用的关键字来表示条件。这些将在后面的`DQL`语句中详细讲解。
在`Java`中，条件之间的衔接通常都是使用逻辑运算符来表示，在`SQL`语句中也是一样，但通常使用`AND`来表示逻辑与(`&&`)，使用`OR`来表示逻辑或(`||`)
<font color=blue>示例</font>：

```sql
WHERE time > 20 AND time < 60; 
--等价于
WHERE time > 20 && time < 60;
```

#### 3.2 UPDATE语句使用

将`C/Cpp`的学分更改为2，学时更改为20

```sql
UPDATE course SET score=2, `time`=20 WHERE name='C/Cpp';
```

### 4. DELETE语句

```sql
DELETE FROM 表名 [WHERE 删除条件];
```

<font color=blue>示例</font>：删除课程表中课程编号为1的数据

```sql
DELETE FROM course WHERE 'number'=1;
```

### 5. TRUNCATE语句

```sql
--清空表中的数据
TRUNCATE [TABLE] 表名;
```

<font color=blue>示例</font>：清空表course中的数据

```sql
TRUNCATE course;
```

### 6. DELETE与TRUNCATE区别

- DELETE语句根据条件删除表中数据，而TRUNCATE语句则是将表中数据全部清空；如果DELETE语句要删除表中所有数据，那么在效率上要低于TRUNCATE语句。
- 如果表中有自增长列，TRUNCATE语句会重置自增长的计数器，但DELETE语句不会。
- TRUNCATE语句执行后，数据无法恢复，而DELETE语句执行后，可以使用事务回滚进行恢复。



## 第二节 `DQL`语句

### 1. 什么是`DQL`

`DQL`全称是Data Query Language，表示数据查询语言。体现在数据的查询操作上，因此，`DQL`仅包括`SELECT`语句。

### 2. SELECT语句

```sql
SELECT ALL/DISTINCT * | 字段名1 AS 别名1[,字段名1 AS 别名1, ..., 字段名n AS 别名n] FROM 表名 WHERE 查询条件
```

> 注：`ALL`表示查询所有满足条件的记录，可以省略；`DISTINCT`表示去掉查询结果中重复的记录AS可以给数据列、数据表取一个别名

<font color=blue>示例</font>：从课程表中查询课程编号小于5的课程名称，从课程表中查询Java课程的学分(score)和学时(time)，从课程表中查询Java课程的学分和学时并重命名

```sql
SELECT name FROM course WHERE `number` < 5;
SELECT score, `time` FROM course name='Java';
SELECT score AS '学分', `time` AS '学时' FROM course name='Java';
--AS可以省略
SELECT score '学分', `time` '学时' FROM course name='Python';
--给表起别名
SELECT c.name, c.score, c.time FROM course c WHERE c.name='Java';
--给表起别名的同时给字段重命名
SELECT c.name '课程名称', c.score '学分', c.time '学时' FROM course c WHERE c.name='Python';
```

### 3. 比较操作符

| 操作符              | 语法                             | 说明                                                         |
| :------------------ | :------------------------------- | :----------------------------------------------------------- |
| IS NULL             | 字段名 IS NULL                   | 如果字段的值为NULL，则条件满足                               |
| IS NOT NULL         | 字段名 IS NOT NULL               | 如果字段的值不为NULL，则条件满足                             |
| BETWEEN ... AND ... | 字段名 BETWEEN 最小值 AND 最大值 | 如果字段的值在最小值与最大值之间（能够取到最小值和最大值），则条件满足 |
| LIKE                | 字段名 LIKE '%匹配内容%'         | 如果字段值包含有匹配内容，则条件满足                         |
| IN                  | 字段名 IN(值1，值2，...， 值n)   | 如果字段值在值1,值2, ...，值n中，则条件满足                  |

<font color=blue>示例</font>：从课程表查询课程名为NULL的课程信息

```sql
SELECT * FROM course WHERE name IS NULL;
```

<font color=blue>示例</font>：从课程表查询课程名不为NULL的课程信息

```sql
SELECT * FROM coures WHERE name IS NOT NULL;
```

<font color=blue>示例</font>：从课程表查询学分在2~4之间的课程信息

```sql
SELECT * FROM course WHERE score BETWEEN 2 AND 4;
--等价于
SELECT * FROM course WHERE score >= 2 ANDE score <= 4;
```

<font color=blue>示例</font>：从课程表查询课程名包含"V"的课程信息

```sql
SELECT * FROM course WHERE name LIKE '%V%';
```

<font color=blue>示例</font>：从课程表查询课程名以"J"开头的课程信息

```sql
SELECT * FROM course WHERE name LIKE 'J%';
```

<font color=blue>示例</font>：从课程表查询课程名以"p"结尾的课程信息

```sql
SELECT * FROM course WHERE name LIKE '%p';
```

<font color=blue>示例</font>：从课程表查询课程名只有三个字符的课程信息

```sql
SELECT * FROM course WHERE name LIKE '___';
```

<font color=blue>示例</font>：从课程表查询课程编号为1,3,5的课程信息

```sql
SELECT * FROM course WHERE `number` IN (1, 3, 5);
```

### 4. 分组

数据表准备：新建学生表student，包含字段学号（no），类型为长整数，长度为20，是主键，自增长，非空；姓名（name），类型为字符串，长度为20，非空；性别（sex），类型为字符串，长度为2，默认值为"男"；年龄（age），类型为整数，长度为3，默认值为0；成绩（score），类型为浮点数，长度为5，小数点后面保留2位有效数字

```sql
CREATE TABLES IF NOT EXISTS student(
    `no` BIGINT(20) AUTO_INCREMENT NOT NULL PRIMARY KEY COMMENT '学号',
	name VARCHAR(20) NOT NULL COMMENT '姓名',
    sex VARCHAR(2) DEFAULT '男' COMMENT '性别',
    age VARCHAR(3) INT(3) DEFAULT 0 COMMENT '年龄',
    score DOUBLE(5, 2) COMMENT '成绩'
)ENGINE=InnoDB CHARSET=UTF8 COMMENT '学生表'
```

插入测试数据：

```sql
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '枫阿雨', '男', 19, 89);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '雨阿枫', '男' 19, 90);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '阿枫雨', '男', 19, 62);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '枫雨阿', '男', 22, 75);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '雨枫阿', '女', 18, 59);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '阿雨枫', '其他', 27, 88);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '亚烟雨', '男', 19, 88);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '烟亚雨', '女', 28, 81);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '雨亚烟', '其他', 32, 62);
```

#### 4.1 分组查询

```sql
SELECT ALL/DISTINCT * | 字段名1 AS	别名1[,字段名1 AS 别名1, ..., 字段名n AS 别名n] FROM 表名 WHERE 查询条件 GROUP BY 字段名1, 字段名2, ...,字段名n;
```

> 注：分组查询所得结果只会展示组内的第一条数据

<font color=blue>示例</font>：从学生表查询成绩在80分以上的学生信息并按性别分组

```sql
SELECT * FROM student WHERE score > 80 GROUP BY sex;
```

<font color=blue>示例</font>：从学生表查询成绩在60~80之间的学生信息并按性别和年龄分组

```sql
SELECT * FROM student WHERE score >= 60 AND score <= 80 GROUP BY sex, age;
```

#### 4.2 聚合函数

- <font color=red>**COUNT()：**</font>统计满足条件的数据总条数

  <font color=blue>示例</font>：从学生表查询成绩在80分以上的学生人数

  ```sql
  SELECT COUNT(*) total FROM student WHERE score > 80;
  ```
  
- <font color=red>**SUM()：**</font>只能用于数值类型的字段或表达式，九三该满足条件的字段值的总和

  <font color=blue>示例</font>：从学生表查询不及格的学生人数和总成绩

  ```sql
  SELECT COUNT(*) totalCount, SUM(score) totalScore FROM student WHERE score < 60;  
  ```

- <font color=red>**AVG()：**</font>只能用于数值类型的字段或者表达式，计算该满足条件的字段值的平均值

  <font color=blue>示例</font>：从学生表查询男生、女生、其他类型的学生的平均成绩

  ```sql
  SELECT sex, AVG(score) avgScore FROM student GROUP BY sex; 
  ```

- <font color=red>**MAX()：**</font>只能用于数值类型的字段或者表达式，计算该满足条件的字段值的最大值

  <font color=blue>示例</font>：从学生表查询学生的最大年龄

  ```sql
  SELECT MAX(age) FROM student;
  ```

- <font color=red>**MIN()：**</font>只能用于数值类型的字段或者表达式，计算该满足条件的字段值的最小值

  <font color=blue>示例</font>：从学生表查询学生的最低分

  ```sql
  SELECT MIN(score) FROM student;
  ```

#### 4.3 分组查询结果筛选

分组后如果还需要满足其他条件，则需要使用HAVING子句来完成。

```sql
SELECT ALL/DISTINCT * | 字段名1 AS	别名1[,字段名1 AS 别名1, ..., 字段名n AS 别名n] FROM 表名 WHERE 查询条件 GROUP BY 字段名1, 字段名2, ...,字段名n HAVING 筛选条件;
```

<font color=blue>示例</font>：从学生表查询年龄在18~22之间的学生信息并按性别分组，找出组内平均分在75分以上的组

```sql
SELECT * FROM student WHERE age BETWEEN 18 AND 22 GROUP BY sex HAVING AVG(score) > 75;
```



### 5. 排序

```sql
SELECT ALL/DISTINCT * | 字段名1 AS 别名1[,字段名1 AS 别名1, ..., 字段名n AS 别名n] FROM 表名 WHERE 查询条件 ORDER BY 字段名1 ASC|DESC，字段名2 ASC|DESC,..., 字段名n ASC|DESC;
--DESC : 降序排序
--ASC : 升序排序
```

> 注：`ORDER BY`必须位于`WHERE` 条件之后。

<font color=blue>示例</font>：从学生表查询年龄在18~30岁之间的学生信息并按成绩从高到低排列，如果成绩相同，则按年龄从小到大排列

```sql
SELECT * FROM student WHERE age BETWEEN 18 AND 30 ORDER BY score DESC, age ASC;
```

### 6. 分页

```sql
SELECT ALL/DISTINCT * | 字段名1 AS 别名1[,字段名1 AS 别名1, ..., 字段名n AS 别名n] FROM 表名 WHERE 查询条件 LIMIT 偏移量, 查询条数
```

LIMIT的第一个参数表示偏移量，也就是跳过的行数。
LIMIT的第二个参数表示查询返回的最大行数，可能没有给定的数量那么多行。

<font color=blue>示例</font>：从学生表分页查询成绩及格的学生信息，每页显示3条，查询第2页学生信息

```sql
SELECT * FROM student WHERE score >= 60 LIMIT 3, 3;
```

> 注：如果一个查询中包含分组、排序和分页，那么它们之间必须按照<font color=red>**分组->排序->分页**</font>的先后顺序排列。



## 第三节 综合练习

**1. 创建员工表 `emp`，包含字段员工编号（no），类型为整数，长度为20，是主键，自增长，非空；姓名（name），类型为字符串，长度为20，非空；性别（sex），类型为字符串，长度为2，默认值为"男"；年龄（age），类型为整数，长度为3，非空；所属部门（dept），类型为字符串，长度为20，非空；薪资（salary），类型为浮点数，长度为10，小数点后面保留2位有效数字，非空。**

```sql
CREATE TABLES IF NOT EXISTS emp(
	`no` BIGINT(20) AUTO_INCREMENT NOT NULL PRIMARY KEY COMMENT '员工编号',
    name VARCHAR(20) NOT NULL COMMENT '姓名',
    sex VARCHAR(2) DEFAULT '男' COMMENT '性别',
    age TINYINT(3) UNSIGNED NOT NULL COMMENT '年龄',
    dept VARCHAR(20) NOT NULL COMMENT '所属部门',
    salary DOUBLE(10, 2) NOT NULL COMMENT '薪资'
)ENGINE=InnoDB CHARSET=UTF8 COMMENT '员工表';
```

**2. 向员工表插入如下数据：**

| 姓名   | 性别 | 年龄 | 部门   | 薪资  |
| :----- | :--- | :--- | ------ | ----- |
| 枫阿雨 | 男   | 19   | 研发部 | 27000 |
| 雨阿枫 | 男   | 24   | 研发部 | 22000 |
| 阿枫雨 | 女   | 23   | 财务部 | 16000 |
| 枫雨阿 | 女   | 26   | 财务部 | 17000 |
| 阿雨枫 | 男   | 28   | 研发部 | 25000 |
| 亚烟雨 | 女   | 24   | 研发部 | 23000 |
| 烟亚雨 | 女   | 24   | 测试部 | 12000 |
| 雨亚烟 | 女   | 26   | 测试部 | 14000 |

```sql
INSERT INTO emp (name, sex, age, dept, salary) VALUES ('枫阿雨', ''男'', 19, '研发部', 27000),('雨阿枫', 男, 24, '研发部', 22000),('阿枫雨', '女', 23, '财务部', 16000),('枫雨阿', '女', 26, '财务部', 17000),('阿雨枫', '男', 28, '研发部', 25000),('亚烟雨', '女', 24, '研发部', 23000),('烟亚雨', '女', 24, '测试部', 12000),('雨亚烟', '男', 26, '研发部', 14000);
```

**3. 烟亚雨 因工作出色而被提升为测试主管，薪资调整为16000**

```sql
UPDATE emp SET salary=16000 WHERE name='烟亚雨';
```

**4. 研发部 阿雨枫 离职**

```sql
DELETE FROM emp WHERE name='阿雨枫';
```

**5. 从员工表中查询出平均年龄小于25的部门**

```sql
SELECT dept FROM emp GROUP BY dept HAVING AVG(age) < 25;
```

**6. 从员工表中统计研发部的最高薪资、最低薪资、平均薪资和总薪资**

```sql
SELECT MAX(salary), MIN(salary), AVG(salary) FROM emp WHERE dept='研发部';
```

**7. 从员工表中统计各个部门的员工数量**

```sql
SELECT dept, COUNT(*) FROM emp GROUP BY dept; 
```

**8. 从员工表中查询薪资在14000以上的员工信息并按薪资从高到低排列**

```sql
SELECT * FROM emp WHERE salary > 14000 ORDER BY salary DESC;
```

**9. 从员工表中分页查询员工信息，每页显示5条员工信息，按薪资从高到低排列，查询第2页员工信息**

```sql
SELECT * FROM emp ORDER BY salary DESC LIMIT 5, 5;
```

---



# 第三章 `MySQL`常用函数

## 第一节 常用数学函数

| 函数           | 说明                                               | 示例                        |
| :------------- | :------------------------------------------------- | :-------------------------- |
| ABS(X)         | 返回X的绝对值                                      | SELECT ABS(-8);             |
| FLOOR(X)       | 返回不大于X的最大整数                              | SELECT FLOOR(1.3);          |
| CEIL(X)        | 返回不小于X的最小整数                              | SELECT CEIL(1.3);           |
| TRUNCATE(X, D) | 返回值X保留到小数点后D位的值，截断时不进行四舍五入 | SELECT TRUNCATE(1.2328, 3); |
| ROUND(X)       | 返回离X最近的整数，截断时要进行四舍五入            | SELECT ROUND(1.8);          |
| ROUND(X, D)    | 返回X小数点后D位的值，截断时要进行四舍五入         | SELECT ROUND(1.2323, 3);    |
| RAND()         | 返回0-1的随机数                                    | SELECT RAND();              |
| MOD(N, M)      | 返回N除以M以后的余数                               | SELECT MOD(2, 9);           |



## 第二节 常用字符串函数

| 函数                      | 说明                                                     | 示例                                                   |
| :------------------------ | :------------------------------------------------------- | :----------------------------------------------------- |
| CHAR_LENGTH(str)          | 计算字符串字符个数                                       | SELECT CHAR_LENGTH('枫阿雨'); --3                      |
| LENGTH(str)               | 返回值位字符串str的长度，单位为字节                      | SELECT LENGTH('枫阿雨'); --9                           |
| CONCAT(s1, s2, ...)       | 将多个字符串拼接在一起，其中任意一个为NULL则返回值为NULL | SELECT CONCAT('枫', '阿', '雨'); --枫阿雨              |
| LOWER(str)<br/>LCASE(str) | 将字符串中的字母全部转换成小写                           | SELECT LOWER('JAVA');<br/>SELECT LCASE('JAVA'); --java |
| UPPER(str)<br/>UCASE(str) | 将字符串中的字母全部转换成大写                           | SELECT UPPER('java');<br/>SELECT LCASE('java'); --JAVA |
| LEFT(s, n)                | 返回字符串s从最左边开始的n个字符                         | SELECT LEFT('枫阿雨带帅比', 3); --枫阿雨               |
| RIGHT(s, N)               | 返回字符串s从最右边开始的n个字符                         | SELECT RIGHT('枫阿雨带帅比', 3); --带帅比              |
| LTRIM(str)                | 返回字符串s，其左边的所有空格被删除                      | SELECT LTRIM('Java'); --Java                           |
| RTRIM(str)                | 返回字符串s，其右边的所有空格被删除                      | SELECT RTRIM('Java '); --Java                          |
| TRIM(str)                 | 返回字符串str删除了两边空格之后的字符串                  | SELECT TRIM('  Java '); --Java                         |
| REPLACE(s, s1, s2)        | 返回一个字符串，用字符串s2替代字符串s中的所有字符串s1    | SELECT REPLACE('疯阿雨', '疯', '枫'); --枫阿雨         |
| SUBSTRING(s, n, len)      | 从字符串s中返回一个第n个字符开始 长度为len的字符串       | SELECT SUBSTRING('枫阿雨带帅比', 2, 2); --阿雨         |

<font color=blue>示例</font>：

## 第三节 日期和时间函数



## 第四节 条件判断函数



## 第五节 其他函数

