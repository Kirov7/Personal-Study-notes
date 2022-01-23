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

| 名称       | `MyISAM` | ``MyISAM``  |
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

<font color=blue>示例</font>：假设表中有信管和大数据四个班(class)，查询信管和大数据各有多少人

```sql
SELECT LEFT(class, 2), COUNT(*) FROM student GROUP BY LEFT(class, 2);
```

<font color=blue>示例</font>：查询名字有4个字的学生信息

```sql
SELECT * FROM student WHERE CHAR_LENGTH(name)=4;
```

<font color=blue>示例</font>：查询成绩能够被10整除的考试信息

```sql
SELECT * FROM student WHERE MOD(score, 10)=0;
```



## 第三节 日期和时间函数

| 函数                                         | 说明                                    | 示例                                                 |
| :------------------------------------------- | :-------------------------------------- | :--------------------------------------------------- |
| CURDATE()<br/>CURRENT_DATE()                 | 返回当前日期：`YY-MM-dd `               | SELECT CURDATE();                                    |
| CURTIME()<br/>CURRENT_TIME()                 | 返回当前时间：`HH:mm:ss`                | SELECT CURTIME;                                      |
| NOW()<br/>CURRENT_TIMESTAMP()<br/>SYSDATE(); | 返回当前日期和时间：`YY-MM-dd HH:mm:ss` | SELECT NOW();                                        |
| YEAR(d)                                      | 返回日期 d 中的年份值                   | SELECT YEAR(NOW());                                  |
| MONTH(d)                                     | 返回日期 d 中的月份值，范围是1~12       | SELECT MONTH(NOW());                                 |
| WEEKDAY(d)                                   | 返回日期 d 是星期几                     | SELECT WEEKDAY(NOW());                               |
| DAYOFMONTH(d)                                | 返回给定日期 d 是当月的第几天           | SELECT DAYOFMONTH(NOW());                            |
| HOUR(d)                                      | 返回日期 d 的小时数                     | SELECT HOUR(NOW());                                  |
| MINUTE(d)                                    | 返回日期 d 的分钟数                     | SELECT MINUTE(NOW());                                |
| SECOND(d)                                    | 返回日期 d 的秒数                       | SELECT SECOND(NOW());                                |
| ADDDATE(d, n)                                | 返回起始日期 d 加上 n 天的日期          | SELECT ADDDATE(NOW(), 3);                            |
| TIMESTAMPDIFF(INTERVAL expr type, d1, d2)    | 返回给定日期 d1 和 d2 的时间差          | SELECT TIMESTAMPDIFF(YEAR, '2003-1-4', '2022-1-22'); |
| DATE_FORMAT(d, f)                            | 返回给定日期格式的字符串                | SELECT DATE_FORMAT(NOW(), '%Y-%m-%d %H:%i:%s');      |

<font color=blue>示例</font>：查询年龄在20岁以上的学生信息

```sql
SELECT * FROM student WHERE TIMESTAMPDIFF(YEAR, birthday, NOW()) > 20;
```

<font color=blue>示例</font>：查询今天过生日的学生信息

```sql
SELECT * FROM student WHERE MONTH(birthday)=MONTH(NOW()) AND DATEOFMONTH(birthday)=DATEOFMONTH(NOW());
```

<font color=blue>示例</font>：查询本周过生日的学生信息

```sql
SELECT * FROM student WHERE RIGHT(birthday, 5) > RIGHT(DATE_FORMAT(ADDDATE(NOW(), -DAYOFWEEK(NOW())), '%Y-%m-%d'), 5) AND RIGHT(birthday, 5) <= 
RIGHT(DATE_FORMAT(ADDDATE(NOW(),7-DAYOFMONTH(NOW())), '%Y-%m-%d'), 5);
```



## 第四节 条件判断函数

### 1. IF 函数

#### 1.1 IF

```sql
IF(条件, 表达式1, 表达式2)
```

如果条件满足，则使用表达式1，否则使用表达式2

<font color=blue>示例</font>：将学生成绩展示为及格和不及格

```sql
SELECT id, name, IF(score >= 60, '及格', '不及格') finalScore FROM scoreTable;
```

#### 1.2 IFNULL

```sql
IFNULL(字段, 表达式)
```

如果字段值为空，则使用表达式，否则，使用字段值

<font color=blue>示例</font>：将未参加考试的学生成绩展示为缺考

```sql
SELECT id, name, IFNULL(score, '缺考') finalScore FROM scoreTable;
```

### 2. CASE ... WHEN 语句

#### 2.1 CASE WEHN

```sql
CASE WHEN 条件1 THEN 表达式1 [WHEN 条件2 THEN 表达式2 ...] ELSE 表达式n END
```

如果条件1满足，则使用表达式1；【如果条件2满足，则使用表达式2， ... 】否则，使用表达式n。相当于Java中的多重if..else语句

<font color=blue>示例</font>：行转列，查看每人每科的分数

```sql
SELECT 
	name,
	course,
	MAX(CASE WHEN (course = 'Java') THEN score ELSE 0 END) JavaScore,
	MAX(CASE WHEN (course = 'Python') THEN score ELSE 0 END) PythonScore,  
	MAX(CASE WHEN (course = 'C/Cpp') THEN score ELSE 0 END) C/CppScore,  
	MAX(CASE WHEN (course = 'Golang') THEN score ELSE 0 END) GolangScore
FROM scoreTable
GROUP BY name;
```

#### 2.2 CASE ... WHEN

```sql
CASE 表达式 WHEN 值1 THEN 表达式1 [WHEN 值2 THEN 表达式2 ...] ELSE 表达式n END 
```

如果表达式的执行结果为值1，则使用表达式1；【执行结果为值2，则使用表达式2， ... 】否则，使用表达式n。相当于Java中的switch语句

<font color=blue>示例</font>：行转列，查看每人每科的分数

```sql
SELECT 
	name,
	course,
	MAX(CASE course WHEN 'Java' THEN score ELSE 0 END) JavaScore,
	MAX(CASE course WHEN 'Python' THEN score ELSE 0 END) PythonScore,  
	MAX(CASE course WHEN 'C/Cpp' THEN score ELSE 0 END) C/CppScore,  
	MAX(CASE course WHEN 'Golang' THEN score ELSE 0 END) GolangScore
FROM scoreTable
GROUP BY name;
```

<font color=blue>练习</font>：查询各班级人数，查询结果格式为 班级 男 女 其他

```sql
SELECT
	class,
	SUM(CASE sex WHEN '男' THEN sex ELSE 0 END) '男',
	SUM(CASE sex WHEN '女' THEN sex ELSE 0 END) '女',
	SUM(CASE sex WHEN '其他' THEN sex ELSE 0 END) '其他'
FROM scoreTable
GROUP BY class;
```



## 第五节 其他函数

### 1. 数字格式化函数

```sql
FORMAT(X, D)
```

将数字X格式化，将X保留到小数点后D位，截断时要进行四舍五入

<font color=blue>示例</font>：

```sql
SELECT FORMAT(1.2353, 2);
```

### 2. 系统信息函数

| 函数                                        | 说明               | 示例                                                         |
| :------------------------------------------ | :----------------- | :----------------------------------------------------------- |
| VERSION()                                   | 获取服务器的版本号 | SELECT VERSION();                                            |
| CONNECTION_ID()                             | 获取服务器的连接数 | SELECT CONNECTION_ID();                                      |
| DATEBASE()<br/>SCHEMA()                     | 获取当前数据库名   | SELECT DATEBASE();<br/>SELECT SCHEMA();                      |
| USER()<br/>SYSTEM_USER()<br/>SESSION_USER() | 获取当前用户名     | SELECT USER();<br/>SELECT SYSTEM_USER();<br/>SELECT SESSION_USER(); |
| CURRENT_USER()<br/>CURRENT_USER             | 获取当前用户名     | SELECT CURRENT_USER;                                         |

##  第六节 综合练习

**1. 求字符串`'ABCdEfGHIJkLMn'`的字符数**

```sql
SELECT CHAR_LENGTH('ABCdEfGHIJkLMn');
```

**2. 将`'枫阿雨'`和`'带帅比'`拼接成新的字符串**

```;
SELECT CONCAT('枫阿雨', '带帅比');
```

**3. 求`'2003-01-04'`到现在总过有多少天**

```sql
SELECT TIMESTAMPDIFF(DAY, '2003-01-04', NOW());
```

**4. 如果字段score的值大于90，则展示为优秀，否则展示为良好**

```sql
SELECT IF(score > 90, '优秀', '良好') score;
```

****





# 第四章 联表查询



## 第一节 表与表之间的关系

### 1. 表与表之间的关系

数据表是用来描述实体信息的，比如可以使用数据表来描述学生信息，也可以用数据表来描述班级信息，这样就会存在学生表和班级表。而学生和班级显然存在着一种关系：

![](./img/表关系4-1.png)

这种关系在数据库中体现就称之为表与表之间的关系。数据库通过**主外键关联关系**来体现表与表之间的关联关系

### 2. 主外键关联关系

![](./img/表关系4-2.png)

如图所示，此时学生表和班级表并没有任何关系，然而实际上学生和班级是存在归属关系。可以在学生表中添加一个字段，表明该学生所属班级，该字段值使用的是班级表中的主键，在学生表中称之为外键。这样学生表中的所属班级（外键）与班级表中的编号（主键）就产生关联关系，这种关联关系称为主外键关联关系。

![](./img/表关系4-3.png)

### 3. 主外键关联关系的对应

```sql
CREATE TABLE IF NOT EXISTS clss(
    id INT(20) AUTO_INCREMENT NOT NULL PRIMARY KEY COMMENT '学号',
    name VARCHAR(30) NOT NULL COMMENT '班级名称',
    grade VARCHAR(30) NOT NULL COMMENT '年级',
)ENGINE=InnoDB CHARSET=UTF8 COMMENT '班级表';
    
CREATE TABLE IF NOT EXISTS stu(
    number BIGINT(20) AUTO_INCREMENT NOT NULL COMMENT '学号',
    name VARCHAR(30) NOT NULL COMMENT '姓名',
    sex VARCHAR(2) NOT NULL DEFAULT '男' COMMENT '性别',
	age TINYINT(3) UNSIGNED DEFAULT 0 COMMENT '年龄',
    class_id INT(11) NOT NULL COMMENT '所属班级',
    -- 指定number为主键
    PRIMARY KEY(number),
    -- 字段class_id与cls表中的number字段相关联
    FOREIGN KEY(class_id) REFERENCES class(id)
)ENGINE=InnoDB CHARSET=UTF8 COMMENT '学生表';
```



### 4. 约束



#### 4.1 主键约束

- 添加主键约束，保证数据的唯一性

  ```sql
  ALTER TABLE 表名 ADD PRIMARY KEY(字段名1,字段名2, ..., 字段名n);
  ```

  <font color=blue>示例</font>：

  ```sql
  ALTER TABLE stu ADD PRIMARY KEY(number);
  ```

- 删除主键约束

  ```sql
  ALTER TABLE 表名 DROP PRIMARY KEY;
  ```

  > 注：若主键自增则无法直接删除主键约束
  
  <font color=blue>示例</font>：
  
  ```sql
  ALTER TABLE stu DROP PRIMARY KEY;
  ```

#### 4.2 外键约束

- 添加外键约束

  ```sql
  ALTER TABLE1 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (表名1的字段名) REFERENCES 表名2(表名2的字段名);
  ```

  > 注：  `MyISAM`不支持外键索引

  <font color=blue>示例</font>：

  ```sql
  ALTER TABLE stu ADD CONSTRAINT fk_class_id FOREIGN KEY (class_id) REFERENCES class(id);
  ```

- 删除外键约束

  ```sql
  ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
  ```

  > 注：因外键可以多个，故需要声明外键名称

  <font color=blue>示例</font>：

  ```sql
  ALTER TABLE stu DROP FOREIGN KEY fk_class_id;
  ```

#### 4.3 唯一约束

- 为字段添加唯一约束

  ```sql
  ALTER TABLE 表名 ADD CONSTRAINT 约束名称 UNIQUE(字段名1, 字段名2, ..., 字段名n);
  ```

  <font color=blue>示例</font>：

  ```sql
  ALTER TABLE stu ADD CONSTRAINT un_name UNIQUE(name);
  ```
  
- 删除字段的唯一约束

  ```sql
  ALTER TABLE 表名 DROP KEY 约束名称;
  ```

  <font color=blue>示例</font>：

  ```sql
  ALTER TABLE stu ADD CONSTRAINT un_name UNIQUE(name);
  ```
#### 4.4 非空约束

- 为字段添加非空约束

  ```sql
  ALTER TABLE 表名 MODIFY 字段名 列类型 NOT NULL;
  ```

- 删除字段的非空约束

  ```sql
  ALTER TABLE 表名 MODIFY 字段名 列类型 NULL;
  ```

#### 4.5 默认值约束

- 为字段添加默认值

  ```sql
  ALTER TABLE 表名 ALTER 字段名 SET DEFAULT 默认值;
  ```

- 删除字段的非空约束

  ```sql
  ALTER TABLE 表名 ALTER 字段名 DROP DEFAULT;
  ```

#### 4.6 自增约束

- 为字段添加自增约束

  ```sql
  ALTER TABLE 表名 MODIFY 字段名 列类型 AUTO_INCREMENT;
  ```

- 删除字段的自增约束

  ```sql
  ALTER TABLE 表名 MODIFY 字段名 列类型;
  ```

## 第二节 索引

### 1. 什么是索引

在关系数据库中，索引是一种单独的、物理的对数据库表中一列或多列的值进行排序的一种存储结构，它是表中一列或多列值的集合和相应的指向表中物理标识这些值的数据页的逻辑指针清单。

![](./img/索引4-4.png)

对比书籍目录理解

### 2. 索引的作用

- 保证数据的准确性
- 提高检索速度
- 提高系统性能

### 3. 索引的类型

- 唯一索引（`UNIQUE`）：不可以出现相同的值，可以有NULL值
- 普通索引（`INDEX`）：允许出现相同的索引内容
- 主键索引（`PRIMARY KEY`）：不允许出现相同的值
- 全文索引（`FULLTEXT INDEX`）：可以针对值中的某个单词，但效率确实不敢恭维
- 组合索引：实质上是将多个字段建到一个索引里，列值的组合必须唯一

#### 4. 索引的创建、查看、删除

- 创建索引

  ```sql
  ALTER TABLE 表名 ADD INDEX 索引名称 (字段名1, 字段名2, ..., 字段名n);
  ```

- 创建全文索引

  ```sql
  ALTER TABLE 表名 ADD FULLTEXT 索引名称 (字段名1, 字段名2, ..., 字段名n);
  ```

  > 注：`InnoDB`不支持全文索引

- 查看索引

  ```sql
  SHOW INDEX FROM 表名;
  ```

- 删除索引

  ```sql
  ALTER TABLE 表名 DROP INNDEX 索引名称;
  ```

#### 5. 使用索引的注意事项

- 虽然索引大大提高了查询速度，但也会降低更新表的速度，比如对表进行`INSERT`、`UPDATE`和`DELETE`操作，此时，数据库不仅要保存数据，还要保存一下索引文件
- 建立索引会占用磁盘空间的索引文件。如果索引创建过多（尤其是在字段多、数据量大的表上创建索引），就会导致索引文件过大，这样反而会降低数据库性能。因此，索引要建立在经常进行查询操作的字段上
- 不要在列上进行运算（包括函数运算），这会忽略索引的使用
- 不建议使用`LIKE`操作，如果非使用不可，注意正确的使用方式。`LIKE '%查询内容%' `不会使用索引，而`LIKE '查询内容%'`可以使用索引
- 避免使用`IS NULL`、`NOT IN`、`<>`、`!=`、`OR`操作，这些操作都会忽略索引而进行全表扫描

## 第三节 多表查询

### 1. 笛卡尔积

笛卡尔积又称为笛卡尔乘积，由笛卡尔提出，表示两个集合相乘的结果。

![](./img/笛卡尔积4-5.png)

笛卡尔积与多表查询有什么关系呢？每一张表可以看做是一个数据的集合，多表关联串时，这些表中的数据就会形成笛卡尔积。

### 2. 内连接

内连接相当于在笛卡尔积的基础上加上了连接条件。当没有连接条件时，内连接上升为笛卡尔积。

```sql
SELECT 字段名1, 字段名2, ..., 字段名n FROM 表1 [INNER] JOIN 表2 [ON 链接条件];
-- 等价于
SELECT 字段名1, 字段名2, ..., 字段名n FROM 表1, 表2 [WHERE 关联条件 AND 查询条件];
```

<font color=blue>示例</font>：

```sql
SELECT COUNT(*) FROM stu INNER JOIN score ON stu.id=score.stu_id;
-- 等价于
SELECT COUNT(*) FROM stu, score WHERE stu.id=score.stu.id;
```

> 注：下方写法效率更高，下方写法为`ORACLE`的写法，`MySQL`仍支持

### 3. 外连接

外连接涉及到两张表：主表和从表，要查询的信息主要来自于哪张表，哪张表就是主表。
**<font color=red>外连接查询的结果为主表中所有的记录。如果从表中有和它匹配的，则显示匹配的值，这部分相当于内连接查询出来的结果；如果从表中没有和它匹配的，则显示null。</font>**
**<font color=red>外连接查询的结果 = 内连接的结果 + 主表中有的而内连接结果中没有的记录</font>**
外连接分为左外连接和右外连接两种。左外连接使用`LEFT JOIN`关键字，`LEFT JOIN`左边的是主表；右外连接使用`RIGHT JOIN`关键字，`RIGHT JOIN`右边的是主表。

#### 3.1 左外连接

```sql
SELECT 字段名1, 字段名2, ..., 字段名n FROM 主表 LEFT JOIN 从表 [ON 链接条件]
```

<font color=blue>示例</font>：

```sql
SELECT * FROM stu a LEFT JOIN score b ON a.id=b.stu_id;
```



#### 3.2 右外连接

```sql
SELECT 字段名1, 字段名2, ..., 字段名n FROM 从表 RIGHT JOIN 主表 [ON 连接条件];
```

<font color=blue>示例</font>：

```sql
SELECT * FROM stu a RIGHT JOIN score b ON a.id=b.stu_id;
```



## 第四节 子查询

### 1. 什么是子查询

子查询就是嵌套在其他查询中的查询。因此，子查询出现的位置只有3种情况：在`SELECT ... FROM` 之间、在`FROM ... WHERE`之间、在`WHERE`之后

> 注：下面示例中`stu`表中有学生信息，`score`表中有成绩对应信息

### 2. SELECT ... FROM 之间

<font color=blue>示例</font>：查询`stu`表所有学生信息，并将性别按男、女、其他展示

```sql
-- 先创建字典表
CREATE TABLE IF NOT EXISTS dict(
    id 	INT AUTO_INCREMENT NOT NULL PRIMARY KEY COMMENT '学生id',
    type VARCHAR(50) NOT NULL COMMENT '类型',
    `value` VARCHAR(50) NOT NULL COMMENT '原始值',
    `text` VARCHAR(50) NOT NULL COMMENT '转换值',
)ENGINE=InnoDB CHARSET=UTF8 COMMENT '字典表';

-- 向字典表插入字典转换内容
INSERT INTO dict (1, sex, 0, '男'), (2, sex, 1, '女'), (3, sex, 2, '其他');

-- 执行查询
SELECT
	id,
    `name`,
    (SELECT text FROM dict WHERE tYPE='sex' ANDE value=sex) sex,
    birthday,
    class
FROM
	stu;
```

> 注：执行时机是在查询结果出来之后

### 3. FROM ... WHERE 之间

<font color=blue>示例</font>：查询年龄与Java成绩都与枫阿雨的年龄与Java成绩都相同的学生信息

```sql
SELECT c.*, d.* FROM stu c
INNER JOIN 
	score d
    ON c.id=d.stu_id
INNER JOIN
	(SELECT
		TIMESTAMPDIFF(YEAR, a.birthday, NOW()) age,
    	b.score 
	FROM stu a INNER JOIN score b ON a.id=b.stu_id 
	WHERE a.name='枫阿雨' AND b.score='Java') e
ON TIMESTAMPDIFF(YEAR, a.birthday, NOW())=e.age AND d.score=e.score
WHERE d.course='Java';
```

> 注：执行时机是一开始就执行

### 4. WHERE 之后

<font color=blue>示例</font>：查询Java成绩最高的所有学生信息

```sql
SELECT a.*, b.* FROM stu a INNER JOIN score b ON a.id=b.stu_id 
WHERE b.score=(SELECT MAX(score) FROM score WHERE course='Java')
AND b.course='Java';
```

### 第五节 综合练习

**1. 查询有哪些分类**

```sql
内连接：衔接的多表必须保证数据一一对应才可能展示结果
左外连接：衔接的主表(LEFT JOIN 左边的表为主表) 为准，从表中没忽悠的数据以NULL形式展示
右外连接：衔接的主表(RIGHT JOIN 右边的表为主表) 为准，从表中没忽悠的数据以NULL形式展示
```

**2. 子查询有哪些分类**

```sql
在 SELECT ... FROM 之间：执行时机就是查询出结果后执行
在 FROM ... WHERE 之间：执行时机是一开始就先执行
在 WHERE 之后：执行时机是筛选数据时执行
```

---





# 第五章 存储过程、函数、触发器和试图



## 第一节 变量

在 `MySQL`中，变量分为四种类型，即局部变量、用户变量、会话变量和全局变量。其中局部变量和用户变量在实际应用中使用较多，会话变量和全局变量使用较少，因此作为了解即可。

### 1. 全局变量

`MySQL`全局变量会影响服务器整体操作，当服务启动时，它将所有全局变量初始化为默认值。要想更改全局变量，必须具有管理员权限。其作用域为服务器的整个生命周期。

```sql
-- 显示所有的全局变量
SHOW GLOBAL VARIABLES;

-- 设置全局变量的值的两种方式
SET GLOBAL sql_warnings = ON;
SET @@GLOBAL.sql_warnings = OFF;

-- 查询全局变量的值的两种方式
SELECT @@GLOBAL.sql_warnings;
SHOW GLOBAL VARIABLES LIKE '%sql_warnings%';
```

### 2. 会话变量

`MySQL`会话变量是服务器为每个连接的客户端维护的一系列变量。其作用域仅限于当前连接，因此，会话变量是独立的。

```sql
-- 显示所有的会话变量
SHOW SESSION VARIABLES;

-- 设置会话变量的值的三种方式
SET SESSION auto_increment_increment = 1;
SET @@SESSION.auto_increment_increment = 2;

-- 当省略SESSION关键字时，默认缺省为SESSION，即设置会话变量的值
SET auto_increment_increment = 3;

-- 查询会话变量的值的三种方式
SELECT @@auto_increment_increment;
SELECT @@SESSION.auto_increment_increment;
-- SESSION关键字可以省略，也可以用关键字LOCAL替代
SHOW SESSION VARIABLES LIKE '%auto_increment_increment%';

SET @@LOCAL.auto_increment_increment = 1;
SELECT @@LOCAL.auto_increment_increment;
```



### 3. 用户变量

`MySQL`用户变量， `MySQL`中用户变量不用提前申明，在用的时候直接用“@变量名”使用就可以了。其作用域为当前连接。

```sql
-- 第一种用法，使用SET时可以用“=”或“:=”两种赋值符号赋值
SET @age = 18;

-- 第二种用法，使用SELECT时必须用“:=”赋值符号赋值
SELECT @age := 19;
SELECT @age := age FROM stu WHERE `name` = '枫阿雨';

-- 第三种用法，使用SELECT...INTO语句赋值
SELECT age INTO @age FROM stu WHERE `name` = '枫阿雨';
SELECT @age;
```

<font color=blue>示例</font>：筛选所有id大于100的成绩信息，并引入用户变量作为递增序号`num`

```sql
SELECT
	(SELECT @index := @index + 1) num,
    a.*
FROM
	score a,
	(SELECT @index := 0) b
WHERE id > 100;
```



### 4. 局部变量

`MySQL`局部变量，只能用在`BEGIN/END`语句块中，比如存储过程中的BEGIN/END语句块。

```sql
-- 定义局部变量
DECLARE age INT(3) DEFAULT 0;
-- 为局部变量赋值
SET age = 10;
SELECT age := 10;
SELECT 10 INTO age;
SELECT age;
```



## 第二节 存储过程



### 1. 存储过程的概念

在大型数据库系统中，存储过程是一组为了完成特定功能而存储在数据库中的 `SQL`语句集，一次编译后永久有效

### 2. 为什么要使用存储过程

- **运行速度快**：
  在存储过程创建的时候，数据库已经对其进行了一次解析和优化。存储过程一旦执行，在内存中就会保留一份这个存储过程，下次再执行同样的存储过程时，可以从内存中直接调用，所以执行速度会比普通 `SQL`快。  
- **减少网络传输：**
  存储过程直接就在数据库服务器上跑，所有的数据访问都在数据库服务器内部进行，不需要传输数据到其它服务器，所以会减少一定的网络传输。 
- **增强安全性**：
  提高代码安全，防止 `SQL`被截获、篡改。

### 3. 如何使用存储过程

<font color=blue>语法</font>

```sql
-- 声明分隔符
[DELIMITER $$]
CREATE PROCEDURE 存储过程名称 ([IN | OUT | INOUT] 参数名1 数据类型, [[IN | OUT | INOUT] 参数名2 数据类型, ..., [IN | OUT | INOUT] 参数名n 数据类型]) 
-- 语句块开始
BEGIN
-- SQL语句集
END[$$]
-- 还原分隔符
[DELIMITER ; ]

-- 调用存储过程
CALL 存储过程名(参数1, 参数2, ...);
```

<font color=blue>语法</font>：使用存储过程完成银行业务转账

```sql
-- 创建存储过程
DELIMITER //
CREATE PROCEDURE transferMoney(IN transferFROM BIGINT, IN transferTo BIGINT, IN money DOUBLE(20, 3))
BEGIN
	UPDATE account SET balance = balance - money WHERE account = transferFrom;
	UPDATE account SET balance = balance + money WHERE account = transferTo;
END //
DELIMITER ;

-- 调用存储过程
CALL transferMoney(123456, 123456, 2000);
```

如果转账账户余额不足，上面的 `SQL`代码依然可以正常执行，只是执行完后，转账账户的余额变为了负数。这显然不符合常理。因此需要修正。

```sql
-- 创建存储过程
DELIMITER //
CREATE PROCEDURE transferMoney(IN transferFROM BIGINT, IN transferTo BIGINT, IN money DOUBLE(20, 3))
BEGIN
	-- 定义变量表示执行结果：0-失败，1-成功
	DECLARE result TINYINT(1) DEFAULT 0;
	-- 转账账户必须保证余额大于等于转账金额
	UPDATE account SET balance = balance - money WHERE account = transferFrom AND balance >= money;
	-- 检测受影响的行数是否为1，为1表示更新成功
	IF ROW_COUNT() = 1 THEN
		UPDATE account SET balance = balance + money WHERE account = transferTo;
		-- 目标账号余额增加
		IF ROW_COUNT() = 1 THEN
			-- 更新执行结果为1
			SET result = 1;
		END IF;
	END IF;
	-- 查询结果
	SELECT result;
END //
DELIMITER ;

-- 调用存储过程
CALL transferMoney(123456, 123456, 2000);
```

如果转账账户已经将钱转出去，而在执行目标账户增加余额的时候出现了异常或者目标账户输入错误，此时应该怎么办呢？
`MySQL`对数据的操作提供了事务的支持，用来保证数据的一致性,可以有效的解决此类问题。

### 4. 事务

#### 4.1 什么是事务

事务( Transaction)是访问并可能操作各种数据项的一个数据库操作序列，这些操作要么全部执行,要么全部不执行，是一个不可分割的工作单位。事务由事务开始与事务结束之间执行的全部数据库操作组成。

#### 4.2 事务的特性(ACID)

- **原子性（Atomicity）**
  事务的各元素是不可分的（原子的）,它们是一个整体。要么都执行，要么都不执行。
- **一致性（Consistency）**
  当事务完成时，必须保证所有数据保持一致状态。当转账操作完成时，所有账户的总金额应该保持不变，此时数据处于一致性状态；如果总金额发生了改变，说明数据处于非一致性状态。
- **隔离性（Isolation）**
  对数据操作的多个并发事务彼此独立，互不影响。比如张三和李四同时都在进行转账操作，但彼此都不影响对方。
- **持久性（Durability）**
  对于已提交事务，系统必须保证该事务对数据库的改变不被丢失，即使数据库出现故障

#### 4.3 事务解决银行转账问题

```sql
-- 创建存储过程
DELIMITER //
CREATE PROCEDURE transferMoney(IN transferFROM BIGINT, IN transferTo BIGINT, IN money DOUBLE(20, 3))
BEGIN
	-- 定义变量表示执行结果：0-失败，1-成功
	DECLARE result TINYINT(1) DEFAULT 0;
	-- 声明SQLEXCEPTION处理器，当有SQLEXCEPTION发生时，错误标识符的值设为0
 	-- 发生SQLEXCEPTION时的处理方式：CONTINUE，EXIT
 	-- CONTINUE表示即使有异常发生，也会执行后面的语句
 	-- EXIT表示，有异常发生时，直接退出当前存储过程
	DECLARE CONTINUE HANDLER FOR SQLEXCEPTION SET result = 0;
	-- 开启事务
	START TRANSACTION;
	UPDATE account SET balance = balance - money WHERE account = transferFrom AND balance >= money;
	IF ROW_COUNT() = 1 THEN
		UPDATE account SET balance = balance + money WHERE account = transferTo;
		IF ROW_COUNT() = 1 THEN
			SET result = 1;
		END IF;
	END IF;
	-- 如果result的值为0，表示操作存在失败的情况，事务回滚，数据恢复到更改之前的状态
	IF result = 0 THEN ROLLBACK;
	-- 否则，表示所有操作都成功，提交事务
	ELSE COMMIT;
	END IF;
	-- 查询结果
	SELECT result;
END //
DELIMITER ;
```



#### 5. 存储过程输出

```sql
-- 创建存储过程
DELIMITER //
CREATE PROCEDURE transferMoney(IN transferFROM BIGINT, IN transferTo BIGINT, IN money DOUBLE(20, 3), OUT TINYINT(1))
BEGIN
	-- 为SQL异常声明一个持续处理的处理器，一旦出现异常，则将result的值更改为0
	DECLARE CONTINUE HANDLER FOR SQLEXCEPTION SET result = 0;
	-- 开启事务
	START TRANSACTION;
	UPDATE account SET balance = balance - money WHERE account = transferFrom AND balance >= money;
	-- 检测受影响的行数是否为1，为1表示更新成功
	IF ROW_COUNT() = 1 THEN
		UPDATE account SET balance = balance + money WHERE account = transferTo;
		-- 执行成功则设置result值为1
		IF ROW_COUNT() = 1 THEN
			-- 更新执行结果为1
			SET result = 1;
		ELSE SET result = 0;
		END IF;
	ELSE SET result = 0;
	END IF;
	-- 如果result的值为0，表示操作存在失败的情况，事务回滚，数据恢复到更改之前的状态
	IF result = 0 THEN ROLLBACK;
	-- 否则，表示所有操作都成功，提交事务
	ELSE COMMIT;
	END IF;
END //
DELIMITER ;

CALL transferMoney(123456, 123458, 2000 , @result)

SELECT @result;
```



## 第三节 自定义函数

### 1.自定义函数的概念

函数就是在大型数据库系统中，一组为了完成特定功能而存储在数据库中的 `SQL `语句集，一次编译后永久有效

### 2. 自定义函数

`MySQL`本身提供了一些内置函数，这些函数给我们日常的开发和数据操作带来了很大的便利，比如聚合函数`SUM()`、`AVG()`以及日期时间函数等。但这并不能完全满足开发的需要，有时我们需要一个函数来完成一些复杂功能的实现，而 `MySQL`中又没有这样的函数，因此，我们需要自定义函数来实现。

#### 3. 如何使用自定义函数

```sql
CREATE FUNCTION 函数名称 (参数名1 数据类型, 参数名2 数据类型, ..., 参数名n 数据类型]) RETURNS 数据类型
-- 函数特征：
-- DETERMINISTIC: 不确定的
-- NO SQL:没有SQL语句，当然也不会修改数据
-- READS SQL DATA: 只是读取数据，不会修改数据
-- MODIFIES SQL DATA:要修改数据
-- CONTAINS SQL：包含了SQL语句
DETERMINISTIC | NO SQL | READS SQL DATA | MODIFIES SQL DATA | CONTAINS SQL 
-- 语句块开始
BEGIN
	-- SQL语句
	RETURN 结果;
-- 语句块结束
END
```

<font color=blue>示例</font>：使用函数实现求score表中的成绩最大差值

```sql
CREATE FUNCTION getMaxDiff()
RETURNS DOUBLE(5, 2)
DETERMINISTIC
BEGIN
	RETURN (SELECT MAX(score) - MIN(score) FROM score);
END

-- 调用函数
SELECT getMaxDiff();
```

#### 4. 循环结构

```sql
WHILE 循环条件 DO
	-- SQL语句集
END WHILE:

REPEAT
	-- SQL语句集
UNTIL 循环终止条件 END REPEAT;

标号: LOOP
	-- SQL语句集
			IF 循环终止条件 THEN LEAVE 标号;
			END IF;
		END LOOP;
```

<font color=blue>示例</font>：使用函数实现求0~给定的任意整数的累加和

```sql
-- 使用WHILE 循环实现
CREATE FUNCTION getTotal(maxNum INT(11))
RETURNS INT(11)
NO SQL
BEGIN
	DECLARE total INT(11) DEFAULT 0;
	DECLARE i INT(11) DEFAULT 0;
	WHILE i <= maxNum DO
		SET total = total + i;
		SET i = i + 1;
	END WHILE;
	RETURN total;
END

-- 使用REPEAT UNTIL实现
CREATE FUNCTION getTotal1(maxNum INT(11))
RETURNS INT(11)
NO SQL
BEGIN
	DECLARE total INT(11) DEFAULT 0;
	DECLARE i INT(11) DEFAULT 0;
	REPEAT
		SET total = total + i;
		SET i = i + 1;
	UNTIL i > maxNum END REPEAT;
	RETURN total;
END

-- 使用LOOP实现
CREATE FUNCTION getTotal2(maxNum INT(11))
RETURNS INT(11)
NO SQL
BEGIN
	DECLARE total INT(11) DEFAULT 0;
	DECLARE i INT(11) DEFAULT 0;
	a: LOOP
		SET total = total + i;
		SET i = i + 1;
		IF i > maxNum THEN LEAVE a;
		END IF;
	END LOOP;
	RETURN total;
END

-- 调用函数
SELECT getTotal(100);
SELECT getTotal1(100);
SELECT getTotal2(100);
```

<font color=blue>练习</font>：使用函数实现生成一个指定长度的随机字符串

<font color=blue>思路</font>：

1. 定义变量保存字符和数字组成字符串
2. 定义变量保存生成的字符串
3. 循环获取随机数，使用字符串截取的方式获得随机字符，并使用字符串拼接函数完成组装

```sql
CREATE FUNCTION rodomString(stringLength INT(11))
RETURNS VARCHAR(255);
NO SQL
BEGIN
	DECLARE str VARCHAR(64) DEFAULT 'ABCDEFGHIJKLMOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
	DECLARE result VARCHAR(255) DEFAULT NULL;
	DECLARE position INT(11);
	DECLARE i INT(11) DEFAULT 0;
	WHILE i < stringLength DO
		SELECT ROUND(RAND() * 62) INTO position;
		SET result = CONCAT(result, SUBSTRING(str, position, 1));
		SET i = i + 1;
	END WHILE;
	RETURN result;
END
```



## 第四节 触发器

### 1. 触发器概念

触发器（trigger）是用来保证数据完整性的一种方法，由事件来触发，比如当对一个表进行增删改操作时就会被激活执行。经常用于加强数据的完整性约束和业务规则

### 2. 如何定义触发器

```sql
-- 删除触发器
DROP TRIGGER [IF EXISTS] 触发器名称;
-- 创建触发器
-- 触发时机为BEFORE或者AFTER
-- 触发事件，为INSERT 、 UPDATE或者DELETE 
CREATE TRIGGER 触发器名称 {BEFORE|AFTER} {INSERT|UPDATE|DELETE} ON 表名 FOR EACH ROW 
BEGIN
-- 执行的SQL操作
END
```

### 3. 触发器类型

| 触发器类型    | NEW 和 OLD 的使用                                          |
| ------------- | ---------------------------------------------------------- |
| INSERT 触发器 | `NEW` 表示将要或者已经新增的数据                           |
| UPDATE 触发器 | `OLD` 表示将要或者已经修改的数据，`NEW` 表示将要修改的数据 |
| DELETE 触发器 | `OLD` 表示将要或者已经删除的数据                           |

### 4. 触发器使用场景

<font color=blue>场景一</font>：现有商品表goods和订单表order，每一个订单的生成都意味着商品数量的减少，请使用触发器完成这一过程。

```sql
-- 表数据关系
-- order 订单 id goods_id sales_id sale_count created_time state
-- goods 商品 id name number price agent_id

-- 创建触发器
CREATE TRIGGER addOrder AFTER INSERT ON `order` FOR EACH ROW
BEGIN
	UPDATE goods SET number = number - NEW.sale_count WHERE id=NEW.goods_id; END
	
-- 测试代码
INSERT INTO `order` (`goods_id`, `sales_id`, `sale_count`, `created_time`, `state`)
VALUES(1, 1, 6, '2021-08-16', 1);
```

<font color=blue>场景二</font>：现有商品表goods和订单order，每一个订单的取消都意味着商品数量的增加，请使用触发器完成这一过程。

```sql
-- 创建触发器
CREATE TRIGGER deleteOrder AFTER DELETE ON `order` FOR EACH ROW
BEGIN
	UPDATE goods SET number = number + OLD.sale_count WHERE id=OLD.goods_id;
END

-- 测试代码
DELETE FROM `order` WHERE id =350001;
```

<font color=blue>场景三</font>：现有商品表goods和订单表order，每一个订单购买数量的更新都意味着商品数量的变动，请使用触发器完成这一过程。

```sql
-- 创建触发器
CREATE TRIGGER updateOrder AFTER UPDATE ON `order` FOR EACH ROW
BEGIN
	DECLARE changeNum INT(11) DEFAULT 0;
	SET changeNum = NEW.sale_count - OLD.sale_count;
	UPDATE goods SET number = number - changeNum WHERE id = old.goods_id;
END

-- 测试代码
UPDATE `order` SET sale_count = sale_count + 2 WHERE id=20;
UPDATE `order` SET sale_count = sale_count - 4 WHERE id=20;
```



## 第五节 视图

### 1. 视图的概念

视图是一张虚拟表，本身并不存储数据，当 `SQL`操作视图时所有数据都是从其他表中查出来，运用了封装的思想，实质类似与 子查询

### 2. 如何使用视图

- 创建视图

  ```sql
  CREATE VIEW 视图名称 AS SELECT 列1[, 列2, ...] FROM 表名 WHERE 条件;
  ```

- 更新视图

  ```sql
  CREATE OR REPLACE VIEW 视图名称 AS SELECT 列1[, 列2, ...] FROM 表名 WHERE 条件; 
  ```

- 删除视图

  ```sql
  DROP VIEW IF EXISTS 视图名称;
  ```

### 3. 为什么使用视图

#### 3.1 定制用户数据，聚焦特定的数据。

<font color=blue>示例</font>：如果频繁获取销售人员编号、姓名和代理商名称，可以创建视图

```sql
CREATE OR REPLACE VIEW salesInfo AS
SELECT
	a.id,
	a.`name` saleName,
	b.`name` agentName
FROM
	sales a,
	agent b
WHERE
	a.agent_id = b.id;
	
-- 测试代码
SELECT id, saleName FROM salesInfo;
```

#### 3.2 简化数据操作。

<font color=blue>示例</font>：进行关联查询时，涉及到的表可能会很多，这时写的 `SQL`语句可能会很长，如果这个动作频繁发生的话，可以创建视图

```sql
CREATE OR REPLACE VIEW searchOrderDetail AS
SELECT
    a.id regionId,
    a.`name` regionName,
    b.id agentId,
    b.`name` agentName,
    c.id saleId,
    c.`name` saleName,
    d.sale_count saleCount,
    d.created_time createdTime,
    e.`name` goodsName
FROM
	region a,
	agent b,
	sales c,
	`order` d,
	goods e
WHERE
	a.id = b.region_id
AND b.id = c.agent_id
AND c.id = d.sales_id
AND d.goods_id = e.id;

-- 测试代码
SELECT * FROM searchOrderDetail;
```

#### 3.3 提高安全性能。

<font color=blue>示例</font>：例如：用户密码属于隐私数据，用户不能直接查看密码。可以使用视图过滤掉这一字段

```sql
CREATE OR REPLACE VIEW userInfo AS
SELECT
	 username, 
     salt,
     failure_times,
     last_log_time
FROM
	`user`;
	
SELECT username, salt FROM userInfo;
```

> 注：<font color=red>**视图并不能提升查询速度，只是方便了业务开发，但同时也加大了数据库服务器的压力，因此，需要合理的使用视图**</font>

---





# 第六章 数据库设计

## 第一节 设计数据库

### 1. 什么是实体



### 2. 什么是数据库设计



### 3. 为什么要对数据库进行设计



### 4. 如何设计数据库



## 第二节 ER 图

### 1. 什么是 ER 图



### 2. 如何绘制 ER 图

![](./img/ER图5-1.png)

<font color=blue>示例</font>：

![](./img/表关系4-1.png)

## 第三节 数据库模型图

### 1. 什么是关系模式

### 2. 关系模式转为数据库模型图

![](./img/数据库模型图5-2.png)

## 第四节 数据库三大范式

### 1. 数据库第一范式

![](./img/第一范式5-3.png)

### 2. 数据库第二范式

![](./img/第二范式5-4.png)

### 3. 数据库第三范式

![](./img/第三范式5-5.png)
