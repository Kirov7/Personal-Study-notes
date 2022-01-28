# 第一章 认识 MyBatis

在前面 JDBC 的学习中，虽然我们能够通过 JDBC 来连接和操作数据库，但是哪怕只是完成一个 SQL 语句的执行，都需要编写大量的代码，更不用说如果我还需要进行实体类映射，将数据转换为我们可以直接操作的实体类型， JDBC 很方便，但是还不够方便，我们需要一种更加简洁高效的方式来和数据库进行交互。



![](./img/MyBatis1-1.png)

MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs (Plain Ordinary Java Object，普通的 Java对象)映射成数据库中的记录。



## 第一节 初次使用 MyBatis

MyBatis 中文文档网站：https://mybatis.org/mybatis-3/zh/configuration.html

首先需要导入MyBatis的依赖，同样地放入到项目的根目录下，右键作为依赖即可！（依赖变多之后，我们可以将其放到一个单独的文件夹，不然会很繁杂）

依赖导入完成后，我们就可以编写 MyBatis 的配置文件了（现在不是在Java代码中配置了，而是通过一个XML文件去配置，这样就使得硬编码的部分大大减少，项目后期打包成Jar运行不方便修复，但是通过配置文件，我们随时都可以去修改，就变得很方便了，同时代码量也大幅度减少，配置文件填写完成后，我们只需要关心项目的业务逻辑而不是如何去读取配置文件）我们按照官方文档给定的提示，在项目根目录下新建名为`MyBatis-config.xml`的文件，并填写以下内容：

```dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${驱动类（含包名）}"/>
        <property name="url" value="${数据库连接URL}"/>
        <property name="username" value="${用户名}"/>
        <property name="password" value="${密码}"/>
      </dataSource>
    </environment>
  </environments>
</configuration>
```

引入的 `.dtd` 文档提前帮助我们规定了一些标签，我们就需要使用Mybatis提前帮助我们规定好的标签来进行配置（因为只有这样Mybatis才能正确识别我们配置的内容）

通过进行配置，我们就告诉了Mybatis我们链接数据库的一些信息，包括URL、用户名、密码等，这样Mybatis就知道该链接哪个数据库、使用哪个账号进行登陆了（也可以不使用配置文件，这里不做讲解，还请各位小伙伴自行阅读官方文档）

配置文件完成后，我们需要在Java程序启动时，让Mybatis对配置文件进行读取并得到一个`SqlSessionFactory`对象：

```java
public static void main(String[] args) throws FileNotFoundException {
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(new FileInputStream("mybatis-config.xml"));
    try (SqlSession sqlSession = sqlSessionFactory.openSession(true)){
			//暂时还没有业务
    }
}
```

那么`SqlSessionFactory`对象是什么东西：

![](./img/SqlSessionFactory对象1-2.png)

每个基于 MyBatis 的应用都是以一个 `SqlSessionFactory` 的实例为核心的，可以通过 `SqlSessionFactory` 来创建多个新的会话， `SqlSession` 对象，每个会话就相当于不同的地方登陆一个账号去访问数据库，也可以认为这就是之前JDBC中的`Statement`对象，会话之间相互隔离，没有任何关联。

而通过 `SqlSession` 就可以完成几乎所有的数据库操作，可以发现这个接口中定义了大量数据库操作的方法，因此，现在只需要通过一个对象就能完成数据库交互了，极大简化了之前的流程。

我们来尝试一下直接读取实体类，读取实体类肯定需要一个映射规则，比如类中的哪个字段对应数据库中的哪个字段，在查询语句返回结果后，Mybatis就会自动将对应的结果填入到对象的对应字段上。首先编写实体类，可以直接使用 Lombok 加快开发速度：

```java
import lombok.Data;

@Data
public class Student {
    int sid;   //名称最好和数据库字段名称保持一致，不然可能会映射失败导致查询结果丢失
    String name;
    String sex;
}
```

在根目录下重新创建一个mapper文件夹，新建名为`TestMapper.xml`的文件作为我们的映射器，并填写以下内容：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="TestMapper">
    <select id="selectStudent" resultType="com.test.entity.Student">
        select * from student
    </select>
</mapper>
```

其中namespace就是命名空间，每个Mapper都是唯一的，因此需要用一个命名空间来区分，它还可以用来绑定一个接口。我们在里面写入了一个select标签，表示添加一个select操作，同时id作为操作的名称，resultType指定为我们刚刚定义的实体类，表示将数据库结果映射为`Student`类，然后就在标签中写入我们的查询语句即可。

编写好后，我们在配置文件中添加这个Mapper映射器：

```xml
<mappers>
    <mapper url="file:mappers/TestMapper.xml"/>
    <!--    这里用的是url，也可以使用其他类型    -->
</mappers>
```

最后在程序中使用我们定义好的Mapper即可：

```java
public static void main(String[] args) throws FileNotFoundException {
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(new FileInputStream("mybatis-config.xml"));
    try (SqlSession sqlSession = sqlSessionFactory.openSession(true)){
        List<Student> student = sqlSession.selectList("selectStudent");
        student.forEach(System.out::println);
    }
}
```

可以发现，Mybatis非常智能，只需要告诉一个映射关系，就能够直接将查询结果转化为一个实体类！

## 第二节 配置 MyBatis

由于`SqlSessionFactory`一般只需要创建一次，因此可以创建一个工具类来集中创建`SqlSession`，这样会更加方便一些：

```java
public class MybatisUtil {

    //在类加载时就进行创建
    private static SqlSessionFactory sqlSessionFactory;
    static {
        try {
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(new FileInputStream("mybatis-config.xml"));
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取一个新的会话
     * @param autoCommit 是否开启自动提交（跟JDBC是一样的，如果不自动提交，则会变成事务操作）
     * @return SqlSession对象
     */
    public static SqlSession getSession(boolean autoCommit){
        return sqlSessionFactory.openSession(autoCommit);
    }
}
```

现在我们只需要在main方法中这样写即可查询结果了：

```java
public static void main(String[] args) {
    try (SqlSession sqlSession = MybatisUtil.getSession(true)){
        List<Student> student = sqlSession.selectList("selectStudent");
        student.forEach(System.out::println);
    }
}
```

之前我们演示了，如何创建一个映射器来将结果快速转换为实体类，但是这样可能还是不够方便，我们每次都需要去找映射器对应操作的名称，而且还要知道对应的返回类型，再通过`SqlSession`来执行对应的方法，能不能再方便一点呢？

现在，我们可以通过`namespace`来绑定到一个接口上，利用接口的特性，我们可以直接指明方法的行为，而实际实现则是由Mybatis来完成。



```java
public interface TestMapper {
    List<Student> selectStudent();
}
```

将Mapper文件的命名空间修改为我们的接口，建议同时将其放到同名包中，作为内部资源：

```xml
<mapper namespace="com.test.mapper.TestMapper">
    <select id="selectStudent" resultType="com.test.entity.Student">
        select * from student
    </select>
</mapper>
```

作为内部资源后，我们需要修改一下配置文件中的mapper定义，不使用url而是resource表示是Jar内部的文件：

```xml
<mappers>
    <mapper resource="com/test/mapper/TestMapper.xml"/>
</mappers>
```

现在我们就可以直接通过`SqlSession`获取对应的实现类，通过接口中定义的行为来直接获取结果：

```java
public static void main(String[] args) {
    try (SqlSession sqlSession = MybatisUtil.getSession(true)){
        TestMapper testMapper = sqlSession.getMapper(TestMapper.class);
        List<Student> student = testMapper.selectStudent();
        student.forEach(System.out::println);
    }
}
```

接下来，我们再来看配置文件，之前我们并没有对配置文件进行一个详细的介绍：

```java
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/study"/>
                <property name="username" value="test"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/test/mapper/TestMapper.xml"/>
    </mappers>
</configuration>
```

首先就从`environments`标签说起，一般情况下，我们在开发中，都需要指定一个数据库的配置信息，包含连接URL、用户、密码等信息，而`environment`就是用于进行这些配置的！实际情况下可能会不止有一个数据库连接信息，比如开发过程中我们一般会使用本地的数据库，而如果需要将项目上传到服务器或是防止其他人的电脑上运行时，我们可能就需要配置另一个数据库的信息，因此，我们可以提前定义好所有的数据库信息，该什么时候用什么即可！

在`environments`标签上有一个default属性，来指定默认的环境，当然如果我们希望使用其他环境，可以修改这个默认环境，也可以在创建工厂时选择环境：

```java
sqlSessionFactory = new SqlSessionFactoryBuilder()
        .build(new FileInputStream("mybatis-config.xml"), "环境ID");
```

我们还可以给类型起一个别名，以简化Mapper的编写：

```java
<!-- 需要在environments的上方 -->
<typeAliases>
    <typeAlias type="com.test.entity.Student" alias="Student"/>
</typeAliases>
```

现在Mapper就可以直接使用别名了：

```xml
<mapper namespace="com.test.mapper.TestMapper">
    <select id="selectStudent" resultType="Student">
        select * from student
    </select>
</mapper>
```

如果这样还是很麻烦，我们也可以直接让Mybatis去扫描一个包，并将包下的所有类自动起别名（别名为首字母小写的类名）

```java
<typeAliases>
    <package name="com.test.entity"/>
</typeAliases>
```

也可以为指定实体类添加一个注解，来指定别名：

```java
@Data
@Alias("ohhhhhhhh")
public class Student {
    private int sid;
    private String name;
    private String sex;
}
```

当然，Mybatis也包含许多的基础配置，通过使用：

```xml
<settings>
    <setting name="" value=""/>
</settings>
```

所有的配置项可以在中文文档处查询，本文不会进行详细介绍，在后面我们会提出一些比较重要的配置项。

有关配置文件的介绍就暂时到这里为止，我们讨论的重心应该是Mybatis的应用，而不是配置文件，所以省略了一部分内容的讲解。

## 第三节 增删改查

在了解了Mybatis的一些基本配置之后，我们就可以正式来使用Mybatis来进行数据库操作了！

在前面我们演示了如何快速进行查询，我们只需要编写一个对应的映射器既可以了：

```xml
<mapper namespace="com.test.mapper.TestMapper">
    <select id="studentList" resultType="Student">
        select * from student
    </select>
</mapper>
```

当然，如果你不喜欢使用实体类，那么这些属性还可以被映射到一个Map上：

```xml
<select id="selectStudent" resultType="Map">
    select * from student
</select>
```

```java
public interface TestMapper {
    List<Map> selectStudent();
}
```

Map中就会以键值对的形式来存放这些结果了。

通过设定一个`resultType`属性，让Mybatis知道查询结果需要映射为哪个实体类，要求字段名称保持一致。那么如果我们不希望按照这样的规则来映射呢？我们可以自定义`resultMap`来设定映射规则：

```xml
<resultMap id="Test" type="Student">
    <result column="sid" property="sid"/>
    <result column="sex" property="name"/>
    <result column="name" property="sex"/>
</resultMap>
```

通过指定映射规则，我们现在名称和性别一栏就发生了交换，因为我们将其映射字段进行了交换。

如果一个类中存在多个构造方法，那么很有可能会出现这样的错误：

```java
### Exception in thread "main" org.apache.ibatis.exceptions.PersistenceException: 
### Error querying database.  Cause: org.apache.ibatis.executor.ExecutorException: No constructor found in com.test.entity.Student matching [java.lang.Integer, java.lang.String, java.lang.String]
### The error may exist in com/test/mapper/TestMapper.xml
### The error may involve com.test.mapper.TestMapper.getStudentBySid
### The error occurred while handling results
### SQL: select * from student where sid = ?
### Cause: org.apache.ibatis.executor.ExecutorException: No constructor found in com.test.entity.Student matching [java.lang.Integer, java.lang.String, java.lang.String]
	at org.apache.ibatis.exceptions.ExceptionFactory.wrapException(ExceptionFactory.java:30)
	...
```

这时就需要使用`constructor`标签来指定构造方法：

```xml
<resultMap id="test" type="Student">
    <constructor>
        <arg column="sid" javaType="Integer"/>
        <arg column="name" javaType="String"/>
    </constructor>
</resultMap>
```

值得注意的是，指定构造方法后，若此字段被填入了构造方法作为参数，**将不会通过反射给字段单独赋值**，而构造方法中没有传入的字段，依然会被反射赋值，有关`resultMap`的内容，后面还会继续讲解。

如果数据库中存在一个带下划线的字段，我们可以通过设置让其映射为以驼峰命名的字段，比如`my_test`映射为`myTest`

```xml
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

如果不设置，默认为不开启，也就是默认需要名称保持一致。

我们接着来看看条件查询，既然是条件查询，那么肯定需要我们传入查询条件，比如现在我们想通过sid字段来通过学号查找信息：

```java
Student getStudentBySid(int sid);
```

```xml
<select id="getStudentBySid" parameterType="int" resultType="Student">
    select * from student where sid = #{sid}
</select>
```

我们通过使用`#{xxx}`或是`${xxx}`来填入我们给定的属性，实际上Mybatis本质也是通过`PreparedStatement`首先进行一次预编译，有效地防止SQL注入问题，但是如果使用`${xxx}`就不再是通过预编译，而是直接传值，因此我们一般都使用`#{xxx}`来进行操作。

使用`parameterType`属性来指定参数类型（非必须，可以不用，推荐不用）

接着我们来看插入、更新和删除操作，其实与查询操作差不多，不过需要使用对应的标签，比如插入操作：

```xml
<insert id="addStudent" parameterType="Student">
    insert into student(name, sex) values(#{name}, #{sex})
</insert>
```

```java
int addStudent(Student student);
```

我们这里使用的是一个实体类，我们可以直接使用实体类里面对应属性替换到SQL语句中，只需要填写属性名称即可，和条件查询是一样的。

## 第四节 复杂查询

一个老师可以教授多个学生，那么能否一次性将老师的学生全部映射给此老师的对象呢，比如：

```java
@Data
public class Teacher {
    int tid;
    String name;
    List<Student> studentList;
}
```

映射为Teacher对象时，同时将其教授的所有学生一并映射为List列表，显然这是一种一对多的查询，那么这时就需要进行复杂查询了。而我们之前编写的都非常简单，直接就能完成映射，因此我们现在需要使用`resultMap`来自定义映射规则：

```xml
<select id="getTeacherByTid" resultMap="asTeacher">
        select *, teacher.name as tname from student inner join teach on student.sid = teach.sid
                              inner join teacher on teach.tid = teacher.tid where teach.tid = #{tid}
</select>

<resultMap id="asTeacher" type="Teacher">
    <id column="tid" property="tid"/>
    <result column="tname" property="name"/>
    <collection property="studentList" ofType="Student">
        <id property="sid" column="sid"/>
        <result column="name" property="name"/>
        <result column="sex" property="sex"/>
    </collection>
</resultMap>
```

可以看到，我们的查询结果是一个多表联查的结果，而联查的数据就是我们需要映射的数据（比如这里是一个老师有N个学生，联查的结果也是这一个老师对应N个学生的N条记录），其中`id`标签用于在多条记录中辨别是否为同一个对象的数据，比如上面的查询语句得到的结果中，`tid`这一行始终为`1`，因此所有的记录都应该是`tid=1`的教师的数据，而不应该变为多个教师的数据，如果不加id进行约束，那么会被识别成多个教师的数据！

通过使用collection来表示将得到的所有结果合并为一个集合，比如上面的数据中每个学生都有单独的一条记录，因此tid相同的全部学生的记录就可以最后合并为一个List，得到最终的映射结果，当然，为了区分，最好也设置一个id，只不过这个例子中可以当做普通的`result`使用。

了解了一对多，那么多对一又该如何查询呢，比如每个学生都有一个对应的老师，现在Student新增了一个Teacher对象，那么现在又该如何去处理呢？

```java
@Data
@Accessors(chain = true)
public class Student {
    private int sid;
    private String name;
    private String sex;
    private Teacher teacher;
}

@Data
public class Teacher {
    int tid;
    String name;
}
```

现在我们希望的是，每次查询到一个Student对象时都带上它的老师，同样的，我们也可以使用`resultMap`来实现（先修改一下老师的类定义，不然会很麻烦）：

```xml
<resultMap id="test2" type="Student">
    <id column="sid" property="sid"/>
    <result column="name" property="name"/>
    <result column="sex" property="sex"/>
    <association property="teacher" javaType="Teacher">
        <id column="tid" property="tid"/>
        <result column="tname" property="name"/>
    </association>
</resultMap>
<select id="selectStudent" resultMap="test2">
    select *, teacher.name as tname from student left join teach on student.sid = teach.sid
                                                 left join teacher on teach.tid = teacher.tid
</select>
```

通过使用`association`进行关联，形成多对一的关系，实际上和一对多是同理的，都是对查询结果的一种处理方式罢了。

## 第五节 动态 SQL



## 第六节 缓存机制



## 第七节 使用注解开发



## 第八节 探究 MyBatis 的动态代理机制



# 第二章 使用Maven管理项目

## 第一节 Maven 项目结构



## 第二节 Maven 依赖导入



## 第三节 Maven 依赖作用域



## 第四节 Maven 可选依赖



## 第五节 Maven 排除依赖



## 第六节 Maven 继承关系



## 第七节 Maven 常用命令



## 第八节 Maven 测试项目



## 第九节 Maven 打包项目



# 第三章 拓展插件与框架

## 第一节 Lombok

我们发现，在以往编写项目时，尤其是在类进行类内部成员字段封装时，需要编写大量的get/set方法，这不仅使得我们类定义中充满了get和set方法，同时如果字段名称发生改变，又要挨个进行修改，甚至当字段变得很多时，构造方法的编写会非常麻烦！

通过使用Lombok（小辣椒）就可以解决这样的问题！

![img](./img/Lombok3-1.png)

我们来看看，使用原生方式和小辣椒方式编写类的区别，首先是传统方式：

```java
public class Student {
    private Integer sid;
    private String name;
    private String sex;

    public Student(Integer sid, String name, String sex) {
        this.sid = sid;
        this.name = name;
        this.sex = sex;
    }

    public Integer getSid() {             //长！
        return sid;
    }

    public void setSid(Integer sid) {     //到！
        this.sid = sid;
    }

    public String getName() {             //爆！
        return name;
    }

    public void setName(String name) {    //炸！
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }
}
```

而使用Lombok之后：

```java
@Getter
@Setter
@AllArgsConstructor
public class Student {
    private Integer sid;
    private String name;
    private String sex;
}
```

我们发现，使用Lombok之后，只需要添加几个注解，就能够解决掉我们之前长长的一串代码！

### 1. 配置 Lombok

* 首先我们需要导入Lombok的jar依赖，和jdbc依赖是一样的，放在项目目录下直接导入就行了。可以在这里进行下载：https://projectlombok.org/download
* 然后我们要安装一下 Lombok 插件，由于IDEA默认都安装了 Lombok 的插件，因此直接导入依赖后就可以使用了。
* 重启 IDEA

Lombok 是一种插件化注解 API ，是通过添加注解来实现的，然后在 javac 进行编译的时候，进行处理。

Java 的编译过程可以分成三个阶段：

![img](./img/Lombok3-2.png)

1. 所有源文件会被解析成语法树。
2. 调用注解处理器。如果注解处理器产生了新的源文件，新文件也要进行编译。
3. 最后，语法树会被分析并转化成类文件。

实际上在上述的第二阶段，会执行*[lombok.core.AnnotationProcessor](https://github.com/rzwitserloot/lombok/blob/master/src/core/lombok/core/AnnotationProcessor.java)*，它所做的工作就是我们上面所说的，修改语法树。

### 2. 使用 Lombok

通过实战来演示一下 Lombok 的实用注解：

* 通过添加`@Getter`和`@Setter`来为当前类的所有字段生成get/set方法，他们可以添加到类或是字段上，注意静态字段不会生成，静态字段可以手动强制在字段上添加注解实现自动生成，final字段无法生成set方法。
  * 还可以使用@Accessors来控制生成Getter和Setter的样式。 
* 通过添加`@ToString`来为当前类生成预设的toString方法。
* 可以通过添加`@EqualsAndHashCode`来快速生成比较和哈希值方法，如果使用此注解的类有继承父类则在注解后面添加参数(callSuper = true)。
* 可以通过添加`@AllArgsConstructor`和`@NoArgsConstructor`来快速生成全参构造和无参构造。
* 可以添加`@RequiredArgsConstructor`来快速生成参数只包含`final`或被标记为`@NonNull`的成员字段。
* 使用`@Data`能代表`@Setter`、`@Getter`、`@RequiredArgsConstructor`、`@ToString`、`@EqualsAndHashCode`全部注解。
  * **一旦使用`@Data`就不建议此类有继承关系，因为`equal`方法可能不符合预期结果（尤其是仅比较子类属性）。**
* 使用`@Value`与`@Data`类似，但是并不会生成setter并且成员属性都是final的。
* 使用`@SneakyThrows`来自动生成try-catch代码块。
* 使用`@Cleanup`作用与局部变量，在最后自动调用其`close()`方法（可以自由更换）
* 使用`@Builder`来快速生成建造者模式。
  * 通过使用`@Builder.Default`来指定默认值。
  * 通过使用`@Builder.ObtainVia`来指定默认值的获取方式。

## 第二节 JUnit

### 1. 尝试 JUnit



## 第三节 JUL 日志系统

### 1. JUL 日志讲解

### 2. Properties 配置文件

### 3. 编写日志配置文件

### 4. 使用 Lombok 快速开启日志

### 5. MyBatis 日志系统

### 