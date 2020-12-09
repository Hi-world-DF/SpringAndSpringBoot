# MyBatis学习
* [page1:简介&入门](https://github.com/Hi-world-DF/SpringAndSpringBoot/blob/main/MyBatis%E5%AD%A6%E4%B9%A0.md#page1%E7%AE%80%E4%BB%8B%E5%85%A5%E9%97%A8)
* [page2:第一个MyBatis程序](https://github.com/Hi-world-DF/SpringAndSpringBoot/blob/main/MyBatis%E5%AD%A6%E4%B9%A0.md#page2-%E7%AC%AC%E4%B8%80%E4%B8%AAmybatis%E7%A8%8B%E5%BA%8F)
* [page3:MyBatis的XML配置](https://github.com/Hi-world-DF/SpringAndSpringBoot/blob/main/MyBatis%E5%AD%A6%E4%B9%A0.md#page3-mybatis%E7%9A%84xml%E9%85%8D%E7%BD%AE)
* [page4:XML 映射器](https://github.com/Hi-world-DF/SpringAndSpringBoot/blob/main/MyBatis%E5%AD%A6%E4%B9%A0.md#page4xml-%E6%98%A0%E5%B0%84%E5%99%A8)
* [page5:动态 SQL](https://github.com/Hi-world-DF/SpringAndSpringBoot/blob/main/MyBatis%E5%AD%A6%E4%B9%A0.md#page5%E5%8A%A8%E6%80%81-sql)
* [page6:Java API](https://github.com/Hi-world-DF/SpringAndSpringBoot/blob/main/MyBatis%E5%AD%A6%E4%B9%A0.md#page6java-api)
* [page7:SQL 语句构建器](https://github.com/Hi-world-DF/SpringAndSpringBoot/blob/main/MyBatis%E5%AD%A6%E4%B9%A0.md#page7sql-%E8%AF%AD%E5%8F%A5%E6%9E%84%E5%BB%BA%E5%99%A8)
* [page8:日志](https://github.com/Hi-world-DF/SpringAndSpringBoot/blob/main/MyBatis%E5%AD%A6%E4%B9%A0.md#page8%E6%97%A5%E5%BF%97)
## page1:简介&入门

#### 1.1 什么是MyBaties

* MyBatis 是一款**持久层框架**、它支持**自定义 SQL**、**存储过程**以及**高级映射**。
* MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
* MyBatis 可以通过简单的 **XML 或注解**来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

#### 1.2 安装使用

1. 要使用 MyBatis， 只需将 [mybatis-x.x.x.jar](https://github.com/mybatis/mybatis-3/releases) 文件置于类路径（classpath）中即可。

2. 如果使用 Maven 来构建项目，则需将下面的依赖代码置于 pom.xml 文件中：

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version>
</dependency>
```

#### 1.3 从 XML 中构建 SqlSessionFactory

* 每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。
* SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。
* SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。
* 从 XML 文件中构建 SqlSessionFactory 的实例非常简单，建议使用类路径下的资源文件进行配置。
* 也可以使用任意的输入流（InputStream）实例，比如用文件路径字符串或 file:// URL 构造的输入流。
* MyBatis 包含一个名叫 Resources 的工具类，它包含一些实用方法，使得从类路径或其它位置加载资源文件更加容易。

```java
//核心操作
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

XML 配置文件中包含了对 MyBatis 系统的核心设置，包括获取数据库连接实例的数据源（DataSource）以及决定事务作用域和控制方式的事务管理器（TransactionManager）。后面会再探讨 XML 配置文件的详细内容，这里先给出一个简单的示例：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

* 还有很多可以在 XML 文件中配置的选项，上面的示例仅罗列了最关键的部分。
* 注意 XML 头部的声明，它用来验证 XML 文档的正确性。
* environment 元素体中包含了事务管理和连接池的配置。
* mappers 元素则包含了一组映射器（mapper），这些映射器的 XML 映射文件包含了 SQL 代码和映射定义信息。

#### 1.4 不使用XML 中构建 SqlSessionFactory

如果你更愿意直接从 Java 代码而不是 XML 文件中创建配置，或者想要创建你自己的配置建造器，MyBatis 也提供了完整的配置类，提供了所有与 XML 文件等价的配置项。

```java
DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();
TransactionFactory transactionFactory = new JdbcTransactionFactory();
Environment environment = new Environment("development", transactionFactory, dataSource);
Configuration configuration = new Configuration(environment);
configuration.addMapper(BlogMapper.class);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(configuration);
```

注意该例中，configuration 添加了一个映射器类（mapper class）。映射器类是 Java 类，它们包含 SQL 映射注解从而避免依赖 XML 文件。不过，由于 Java 注解的一些限制以及某些 MyBatis 映射的复杂性，要使用大多数高级映射（比如：嵌套联合映射），仍然需要使用 XML 配置。有鉴于此，如果存在一个同名 XML 配置文件，MyBatis 会自动查找并加载它（在这个例子中，基于类路径和 BlogMapper.class 的类名，会加载 BlogMapper.xml）。

#### 1.5 从 SqlSessionFactory 中获取 SqlSession

既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。例如：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
}
```

现在有了一种更简洁的方式——使用和指定语句的参数和返回值相匹配的接口（比如 BlogMapper.class），现在你的代码不仅更清晰，更加类型安全，还不用担心可能出错的字符串字面值以及强制类型转换。例如：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
}
```

#### 1.6 探究已映射的 SQL 语句

SqlSession 和 Mapper 到底具体执行了些什么操作？在上面提到的例子中，一个语句既可以通过 XML 定义，也可以通过注解定义。我们先看看 XML 定义语句的方式，事实上 MyBatis 提供的所有特性都可以利用基于 XML 的映射语言来实现，这使得 MyBatis 在过去的数年间得以流行。这里给出一个基于 XML 映射语句的示例，它应该可以满足上个示例中 SqlSession 的调用。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
```

它在命名空间 “org.mybatis.example.BlogMapper” 中定义了一个名为 “selectBlog” 的映射语句，这样你就可以用全限定名 “org.mybatis.example.BlogMapper.selectBlog” 来调用映射语句了，就像上面例子中那样：

```java
Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
```

这种方式和用全限定名调用 Java 对象的方法类似。这样，该命名就可以直接映射到在命名空间中同名的映射器类，并将已映射的 select 语句匹配到对应名称、参数和返回类型的方法。因此你就可以像上面那样，不费吹灰之力地在对应的映射器接口调用方法，就像下面这样：

```java
BlogMapper mapper = session.getMapper(BlogMapper.class);
Blog blog = mapper.selectBlog(101);
```

第二种方法有很多优势，首先它不依赖于字符串字面值，会更安全一点；其次，如果你的 IDE 有代码补全功能，那么代码补全可以帮你快速选择到映射好的 SQL 语句。

对于像 BlogMapper 这样的映射器类来说，还有另一种方法来完成语句映射。 它们映射的语句可以不用 XML 来配置，而可以使用 Java 注解来配置。比如，上面的 XML 示例可以被替换成如下的配置：

```java
package org.mybatis.example;
public interface BlogMapper {
  @Select("SELECT * FROM blog WHERE id = #{id}")
  Blog selectBlog(int id);
}
```

使用注解来映射简单语句会使代码显得更加简洁，但对于稍微复杂一点的语句，Java 注解不仅力不从心，还会让你本就复杂的 SQL 语句更加混乱不堪。 因此，如果你需要做一些很复杂的操作，最好用 XML 来映射语句。

#### 1.7 作用域（Scope）和生命周期

**(1) SqlSessionFactoryBuilder**

这个类可以被实例化、使用和丢弃，一旦创建了 SqlSessionFactory，就不再需要它了。 因此 SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是局部方法变量）。 你可以重用 SqlSessionFactoryBuilder 来创建多个 SqlSessionFactory 实例，但最好还是不要一直保留着它，以保证所有的 XML 解析资源可以被释放给更重要的事情。

**(2) SqlSessionFactory**

SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。 使用 SqlSessionFactory 的最佳实践是在应用运行期间不要重复创建多次，多次重建 SqlSessionFactory 被视为一种代码“坏习惯”。因此 SqlSessionFactory 的最佳作用域是应用作用域。 有很多方法可以做到，最简单的就是使用单例模式或者静态单例模式。

**(3)SqlSession**

每个线程都应该有它自己的 SqlSession 实例。SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。 绝对不能将 SqlSession 实例的引用放在一个类的静态域，甚至一个类的实例变量也不行。 也绝不能将 SqlSession 实例的引用放在任何类型的托管作用域中，比如 Servlet 框架中的 HttpSession。 如果你现在正在使用一种 Web 框架，考虑将 SqlSession 放在一个和 HTTP 请求相似的作用域中。 换句话说，每次收到 HTTP 请求，就可以打开一个 SqlSession，返回一个响应后，就关闭它。 这个关闭操作很重要，为了确保每次都能执行关闭操作，你应该把这个关闭操作放到 finally 块中。 下面的示例就是一个确保 SqlSession 关闭的标准模式：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  // 你的应用逻辑代码
}
//在所有代码中都遵循这种使用模式，可以保证所有数据库资源都能被正确地关闭。
```

**(4)映射器实例**

映射器是一些绑定映射语句的接口。映射器接口的实例是从 SqlSession 中获得的。虽然从技术层面上来讲，任何映射器实例的最大作用域与请求它们的 SqlSession 相同。但方法作用域才是映射器实例的最合适的作用域。 也就是说，映射器实例应该在调用它们的方法中被获取，使用完毕之后即可丢弃。 映射器实例并不需要被显式地关闭。尽管在整个请求作用域保留映射器实例不会有什么问题，但是你很快会发现，在这个作用域上管理太多像 SqlSession 的资源会让你忙不过来。 因此，最好将映射器放在方法作用域内。就像下面的例子一样：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  // 你的应用逻辑代码
}
```

## page2: 第一个MyBatis程序

#### 2.1 构建一个项目MyBatisDemo

#### 2.2 pom.xml文件导入MyBatis依赖

```xml
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.5.6</version>
</dependency>
```

#### 2.3 配置MyBatis核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis核心配置文件-->
<configuration>
    <!--多个开发环境配置，默认development-->
    <environments default="development">
        <environment id="development">
            <!--事务管理JDBC-->
            <transactionManager type="JDBC"/>
            <!--数据库资源-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <!--每一个Mapper.xml都需要在Mybatis核心配置文件中注册！！-->
</configuration>
```

#### 2.4编写一个工具类MyBatisUtils.java来实现从SqlSession中获取SqlSession

```java
public class MybatisUtil {

    /**
     * 要获得sqlSession的步骤：
     * 1.SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。
     * 2.SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。
     *
     * */
    private static SqlSessionFactory sqlSessionFactory;

    static {
        try {
            //这里可以获取sqlSessionFactory
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //getSqlSession()返回sqlSession对象
    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }

}
```

#### 2.5 数据库建表User

```mysql
# 创建数据库表
CREATE TABLE user  (
  `id` int(20) NOT NULL,
  `username` varchar(20) ,
  `password` varchar(20) ,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB ;
# 插入数据
INSERT INTO `user` ( id, `username`, `password` ) VALUES( 1, 'Ernest', '123456' );
INSERT INTO `user` ( id, `username`, `password` ) VALUES( 2, 'Hello', '123456' );
INSERT INTO `user` ( id, `username`, `password` ) VALUES( 3, 'World', '123456' );
```

#### 2.6 创建POJO，User实体类

```java
public class User implements Serializable {

    private int id;
    private String username;
    private String password;

    public User() {
    }

    public User(int id, String username, String password) {
        this.id = id;
        this.username = username;
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

#### 2.7 编写UserMapper接口类，四个方法：增删改查

```java
public interface UserMapper {

    List<User> getUserList();
    
    /**
     * getUserById()：获得某一个user
     * */
    User getUserById(int id);

    /**
     * insert()：插入操作
     * */
    void insert();

    /**
     * updateUserById(int id)：根据id修改操作
     * */
    void updateUserById(int id);

    /**
     * deleteUserById(int id)：根据id删除操作
     * */
    void deleteUserById(int id);
}
```

#### 2.8 编写UserMapper.xml映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace = 绑定一个Dao/Mapper接口-->
<mapper namespace="com.mybatis.dao.UserMapper">
    <!--查询语句,获取所有对象-->
    <select id="getUserList" resultType="com.mybatis.pojo.User">
        select* from  mybatis.user;
    </select>

    <!--查询语句，根据id查询单一对象-->
    <select id="getUserById" resultType="com.mybatis.pojo.User" parameterType="int">
        select* from  mybatis.user where id = #{id};
    </select>

    <!--插入语句，-->
    <insert id="insert">
        insert into mybatis.user (`id`,`username`,`password`) values(4,'Timo','000000');
    </insert>

    <!--根据id修改语句-->
    <update id="updateUserById">
        update mybatis.user set `password` = '123456' where id = #{id};
    </update>

    <!--根据id删除语句-->
    <delete id="deleteUserById">
        delete from mybatis.user where `id` = #{id};
    </delete>
</mapper>
```

#### 2.9 测试

```java
public class UserDaoTest {
    private SqlSession sqlSession;
    //测试查询整个列表
    @Test
    public void test(){
        try{
            //获取sqlSession
            sqlSession = MybatisUtil.getSqlSession();
            //方式1：getMapper()
            UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
            List<User> userList= userMapper.getUserList();

            for (User user : userList) {
                System.out.println(user);
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            sqlSession.close();
        }

    }
}
```

## page3: MyBatis的XML配置

#### 3.1 属性(properties)

* 这些属性可以在外部进行配置，并可以进行动态替换。
* 既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置。

```xml
<property name="driver" value="com.mysql.jdbc.Driver"/>
<property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf8"/>
<property name="username" value="root"/>
<property name="password" value="123456"/>
```

其实我们完全可以将数据库的连接配置信息写在一个properties文件中，然后在conf.xml文件中引用properties文件：创建jdbc.properties

```properties
driver = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8
username = root
password = 123456
```

修改mybatis-config.xml文件 

```xml
<!-- 引用jdbc.properties配置文件 -->
<properties resource="jdbc.properties"/>
<!-- 修改property的值，用${}表示 -->
<property name="driver" value="${driver}"/>
<property name="url" value="${url}"/>
<property name="username" value="${username}"/>
<property name="password" value="${password}"/>
```

如果一个属性不止在一个地方进行了配置，那么MyBatis 将按照下面的顺序来加载：

* 首先读取在 properties 元素体内指定的属性。
* 然后根据 properties 元素中的 resource 属性读取类路径下属性文件，或根据 url 属性指定的路径读取属性文件，并覆盖之前读取过的同名属性。
* 最后读取作为方法参数传递的属性，并覆盖之前读取过的同名属性。

> 因此，**通过方法参数传递的属性具有最高优先级**，**resource/url 属性中指定的配置文件次之**，**最低优先级的则是 properties 元素中指定的属性**。

可以为占位符指定一个默认值，如果属性没有被配置，就取默认值：

```xml
<!-- 引用jdbc.properties配置文件 -->
<properties resource="jdbc.properties">
	<!-- 这个特性是默认关闭的，启用默认值特性 -->
	<property name="org.apache.ibatis.parsing.PropertyParser.enable-default-value" value="true"/> 
</properties>

<dataSource type="POOLED">
  <!-- 如果属性 'username' 没有被配置，'username' 属性的值将为 'root' -->
  <property name="username" value="${username:root}"/> 
</dataSource>
```

#### 3.2 设置(settings)

mybatis-config.xml：列举几个常用的设置，可以改变运行时行为

```xml
<settings>
    <!--显示的开启全局缓存，即二级缓存-->
    <setting name="cacheEnabled" value="true"/>
    
    <!--延迟加载的全局开关，当开启时，所有关联对象都会延迟加载-->
    <setting name="lazyLoadingEnabled" value="true"/>
    
    <!--是否允许单个语句返回多结果集（需要数据库驱动支持）-->
    <setting name="multipleResultSetsEnabled" value="true"/>
    
    <!--使用列标签代替列名-->
    <setting name="useColumnLabel" value="true"/>
    
    <!--允许 JDBC 支持自动生成主键，需要数据库驱动支持。如果设置为 true，将强制使用自动生成主键。-->
    <setting name="useGeneratedKeys" value="false"/>
    
    <!--指定 MyBatis 应如何自动映射列到字段或属性。 -->
    <!--NONE 表示关闭自动映射；PARTIAL 只会自动映射没有定义嵌套结果映射的字段。 FULL 会自动映射任何复杂的结果集（无论是否嵌套）-->
    <setting name="autoMappingBehavior" value="PARTIAL"/>
    
    <!--指定发现自动映射目标未知列（或未知属性类型）的行为-->
    <!--NONE: 不做任何反应
		WARNING: 输出警告日志（'org.apache.ibatis.session.AutoMappingUnknownColumnBehavior' 的日志等级必须设置为 WARN）
		FAILING: 映射失败 (抛出 SqlSessionException)-->
    <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
    
    <!--配置默认的执行器-->
    <!--SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（PreparedStatement）； BATCH 执行器不仅重用语句还会执行批量更新。-->
    <setting name="defaultExecutorType" value="SIMPLE"/>
    
    <!--设置超时时间，它决定数据库驱动等待数据库响应的秒数-->
    <setting name="defaultStatementTimeout" value="25"/>
    
    <!-- 为驱动的结果集获取数量（fetchSize）设置一个建议值 -->
    <setting name="defaultFetchSize" value="100"/>
    
    <!--是否允许在嵌套语句中使用分页（RowBounds）,如果允许使用则设置为 false-->
    <setting name="safeRowBoundsEnabled" value="false"/>
    
    <!--是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn-->
    <setting name="mapUnderscoreToCamelCase" value="false"/>
    
    <!--MyBatis 利用本地缓存机制（Local Cache）防止循环引用和加速重复的嵌套查询-->
    <!--默认值为 SESSION，会缓存一个会话中执行的所有查询。 若设置值为 STATEMENT，本地缓存将仅用于执行语句，对相同 SqlSession 的不同查询将不会进行缓存。-->
    <setting name="localCacheScope" value="SESSION"/>
    
    <!--显当没有为参数指定特定的 JDBC 类型时，空值的默认 JDBC 类型-->
    <setting name="jdbcTypeForNull" value="OTHER"/>
    
    <!--指定对象的哪些方法触发一次延迟加载-->
    <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```

#### 3.3 类型别名（typeAliases）

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写。例如：

**方法1：**我们的UserMapper.xml里的com.mybatis.pojo.User可以更改为User，降低冗余：

```xml
<!--com.mybatis.pojo.User使用了别名，改为了User-->
<select id="getUserList" resultType="com.mybatis.pojo.User">
    select* from  mybatis.user;
</select>

<!--查询语句，根据id查询单一对象-->
<select id="getUserById" resultType="com.mybatis.pojo.User" parameterType="int">
    select* from  mybatis.user where id = #{id};
</select>
```

接下来在mybatis-config.xml中设置别名（仅用于 XML 配置），这样就可以在之前的UserMapper.xml中使用User来代替com.mybatis.pojo.User

```xml
<typeAliases>
    <typeAlias type="com.mybatis.pojo.User" alias="User"></typeAlias>
</typeAliases>
```

**方法2：**也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，比如：

```xml
<!--    <typeAliases>-->
<!--        <typeAlias type="com.mybatis.pojo.User" alias="User"></typeAlias>-->
<!--    </typeAliases>-->
<typeAliases>
    <!--指定com.mybatis包下的别名-->
    <package name="com.mybatis"/>
</typeAliases>
```

其次在需要取别名的类上加上注解@Alias("xxx")，每一个在包 `com.mybatis` 中的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。 比如 `com.mybatis.User` 的别名为 `User`；若有注解，则别名为其注解值。

```java
@Alias("User")
public class User implements Serializable {
    ...
}
```

#### 3.4 类型处理器（typeHandlers）--------> 暂不了解

MyBatis 在设置预处理语句（PreparedStatement）中的参数或从结果集中取出一个值时， 都会用类型处理器将获取到的值以合适的方式转换成 Java 类型。下表描述了一些默认的类型处理器。

#### 3.5 处理枚举类型 --------> 暂不了解

#### 3.6 对象工厂（objectFactory）--------> 暂不了解

#### 3.7 插件（plugins）--------> 暂不了解

#### 3.8 环境配置（environments）

MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之中， 现实情况下有多种理由需要这么做。

例如，开发、测试和生产环境需要有不同的配置；或者想在具有相同 Schema 的多个生产数据库中使用相同的 SQL 映射。

> **不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**
>
> 所以，如果你想连接两个数据库，就需要创建两个 SqlSessionFactory 实例，每个数据库对应一个。而如果是三个数据库，就需要三个实例，依此类推，记起来很简单：
>
> - **每个数据库对应一个 SqlSessionFactory 实例**

为了指定创建哪种环境，只要将它作为可选的参数传递给 SqlSessionFactoryBuilder 即可。可以接受环境配置的两个方法签名是：

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, properties);
//如果忽略了环境参数，那么将会加载默认环境
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, properties);
```

mybatis-config.xml中定义了默认的环境，我们在这也可以配置环境：

```xml
<!--多个开发环境配置，默认development-->
<environments default="development">
    <environment id="development">
        <!--事务管理JDBC-->
        <transactionManager type="JDBC"/>
        <!--数据库资源-->
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username:root}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
</environments>
```

上面的配置说明：

- 默认使用的环境 ID（比如：default="development"）。
- 每个 environment 元素定义的环境 ID（比如：id="development"）。
- 事务管理器的配置（比如：type="JDBC"）。
- 数据源的配置（比如：type="POOLED"）。
-  环境可以随意命名，但务必保证默认的环境 ID 要匹配其中一个环境 ID。

##### 事务管理器（transactionManager）

在 MyBatis 中有两种类型的事务管理器：type="[JDBC|MANAGED]"

* JDBC：这个配置直接使用了 JDBC 的提交和回滚设施，它依赖从数据源获得的连接来管理事务作用域。
* MANAGED ： 这个配置几乎没做什么。它从不提交或回滚一个连接，而是让容器来管理事务的整个生命周期（比如 JEE 应用服务器的上下文）。 默认情况下它会关闭连接。然而一些容器并不希望连接被关闭，因此需要将 closeConnection 属性设置为 false 来阻止默认的关闭行为。例如:

```xml
<transactionManager type="MANAGED">
  <property name="closeConnection" value="false"/>
</transactionManager>
```

> 如果你正在使用 Spring + MyBatis，则没有必要配置事务管理器，因为 Spring 模块会使用自带的管理器来覆盖前面的配置。

##### 数据源（dataSource）

dataSource 元素使用标准的 JDBC 数据源接口来配置 JDBC 连接对象的资源。

* 大多数 MyBatis 应用程序会按示例中的例子来配置数据源。虽然数据源配置是可选的，但如果要启用延迟加载特性，就必须配置数据源。

有三种内建的数据源类型： type="[UNPOOLED|POOLED|JNDI]"

* **UNPOOLED**：这个数据源的实现会每次请求时打开和关闭连接。
* **POOLED**：这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来，避免了创建新的连接实例时所必需的初始化和认证时间。这种处理方式很流行，能使并发 Web 应用快速响应请求。
* **JNDI** ：这个数据源实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的数据源引用。

#### 3.9 数据库厂商标识（databaseIdProvider）

#### 3.10 映射器（mappers）

首先，我们需要告诉 MyBatis 到哪里去找到这些语句。

* 在自动查找资源方面，Java 并没有提供一个很好的解决方案，所以最好的办法是直接告诉 MyBatis 到哪里去找映射文件。
* 你可以使用相对于类路径的资源引用，或完全限定资源定位符（包括 `file:///` 形式的 URL），或类名和包名等。

```xml

<!--每一个Mapper.xml都需要在Mybatis核心配置文件中注册！！-->
<mappers>
    <!--方式1：使用相对于类路径的资源引用-->
    <mapper resource="com/mybatis/dao/mapper/UserMapper.xml"/>
    
    <!--方式2：使用完全限定资源定位符（URL）-->
    <mapper url="file:///var/mappers/AuthorMapper.xml"/>
    
    <!--方式3：使用映射器接口实现类的完全限定类名-->
    <mapper class="org.mybatis.builder.AuthorMapper"/>
    
    <!--方式4：将包内的映射器接口实现全部注册为映射器-->
    <package name="org.mybatis.builder"/>
    
</mappers>
```

以上这些配置会告诉 MyBatis 去哪里找映射文件，剩下的细节就应该是每个 SQL 映射文件了。



## page4：XML 映射器

#### 4.1 select

*  MyBatis 的基本原则之一是：在每个插入、更新或删除操作之间，通常会执行多个查询操作。
* MyBatis 在查询和结果映射做了相当多的改进。一个简单查询的 select 元素是非常简单的。

```xml
<!--查询语句，根据id查询单一对象-->
<select id="getUserById" resultType="User" parameterType="int">
    select* from  mybatis.user where id = #{id};
</select>
```

这个查询语句名为getUserById，返回类型resultType是一个User类型的对象，接受一个int（或Integer）类型的参数，

select中允许配置多个属性来配置每条语句的行为细节:

```xml
<select
  id="selectPerson"                 <!--在命名空间中唯一的标识符，可以被用来引用这条语句。-->
  parameterType="int"               <!--将会传入这条语句的参数的类全限定名或别名。-->
  parameterMap="deprecated"         <!--用于引用外部 parameterMap 的属性，目前已被废弃-->
  resultType="hashmap"              <!--期望从这条语句中返回结果的类全限定名或别名，resultType 和 resultMap 之间只能同时使用一个-->
  resultMap="personResultMap"       <!--对外部 resultMap 的命名引用-->
  flushCache="false"                <!--将其设置为 true 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：false-->
  useCache="true"                   <!--将其设置为 true 后，将会导致本条语句的结果被二级缓存缓存起来，默认值：对 select 元素为 true-->
  timeout="10"                      <!--这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数，默认值为未设置（unset）-->
  fetchSize="256"                   <!--这是一个给驱动的建议值，尝试让驱动程序每次批量返回的结果行数等于这个设置值-->
  statementType="PREPARED"          <!--可选 STATEMENT，PREPARED 或 CALLABLE-->
  resultSetType="FORWARD_ONLY">     <!--FORWARD_ONLY，SCROLL_SENSITIVE, SCROLL_INSENSITIVE 或 DEFAULT（等价于 unset） 中的一个-->
</select>
```

#### 4.2 insert, update 和 delete

```xml
<!--插入语句，-->
<insert id="insert">
    insert into mybatis.user (`id`,`username`,`password`) values(4,'Timo','000000');
</insert>

<!--根据id修改语句-->
<update id="updateUserById">
    update mybatis.user set `password` = '123456' where id = #{id};
</update>

<!--根据id删除语句-->
<delete id="deleteUserById">
    delete from mybatis.user where `id` = #{id};
</delete>
```

![Insert&Update&deleteParame](D:\Work\OfficeSoftware\Typora\files\imgs\Insert&Update&deleteParame.png)

如果你的数据库支持自动生成主键的字段（比如 MySQL 和 SQL Server），那么你可以设置 useGeneratedKeys=”true”，然后再把 keyProperty 设置为目标属性就 OK 了,那么上面的语句可以修改为：

```xml
<insert id="insert" useGeneratedKeys="true" keyProperty="id">
	insert into mybatis.user (`username`,`password`) values('Timo','000000');
</insert>
```

如果你的数据库还支持多行插入, 你也可以传入一个 `User` 数组或集合，并返回自动生成的主键。

```xml
<insert id="insert" useGeneratedKeys="true" keyProperty="id">
  insert into mybatis.user (username, password) values
  <foreach item="item" collection="list" separator=",">
    (#{item.username}, #{item.password})
  </foreach>
</insert>
```

#### 4.3 sql

这个元素可以用来定义可重用的 SQL 代码片段，以便在其它语句中使用。参数可以静态地（在加载的时候）确定下来，并且可以在不同的 include 元素中定义不同的参数值。比如：

```xml
<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>
```

这个 SQL 片段可以在其它语句中使用，例如：

```xml
<select id="selectUsers" resultType="map">
  select
    <include refid="userColumns"><property name="alias" value="t1"/></include>,
    <include refid="userColumns"><property name="alias" value="t2"/></include>
  from some_table t1
    cross join some_table t2
</select>
```

#### 4.4 参数

##### 4.4.1 字符串替换

#### 4.5 结果映射

* `resultMap` 元素是 MyBatis 中最重要最强大的元素。
* 它可以让你从 90% 的 JDBC `ResultSets` 数据提取代码中解放出来，并在一些情形下允许你进行一些 JDBC 不支持的操作。
* 实际上，在为一些比如连接的复杂语句编写映射代码的时候，一份 `resultMap` 能够代替实现同等功能的数千行代码。
* ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。

之前你已经见过简单映射语句的示例，它们没有显式指定 `resultMap`。比如：

```xml
<!--查询语句，根据id查询单一对象-->
<select id="getUserById" resultType="map" >
    select* from mybatis.user where id = #{id};
</select>
```

上述语句只是简单地将所有的列映射到 `HashMap` 的键上，这由 `resultType` 属性指定。虽然在大部分情况下都够用，但是 HashMap 并不是一个很好的领域模型。你的程序更可能会使用 JavaBean 或 POJO（Plain Old Java Objects，普通老式 Java 对象）作为领域模型。MyBatis 对两者都提供了支持。看看下面这个 JavaBean：

```java
@Alias("User")
public class User implements Serializable {

    private int id;
    private String username;
    private String password;
    ...
}
```

基于 JavaBean 的规范，上面这个类有 3 个属性：id，username 和 password。这些属性会对应到 select 语句中的列名。

在这些情况下，MyBatis 会在幕后自动创建一个 `ResultMap`，再根据属性名来映射列到 JavaBean 的属性上。如果列名和属性名不能匹配上，可以在 SELECT 语句中设置列别名（这是一个基本的 SQL 特性）来完成匹配。比如：

```xml
<select id="selectUsers" resultType="User">
  select
    user_id             as "id",
    user_name           as "userName",
    user_password       as "password"
  from mybatis.user
  where id = #{id}
</select>
```

```xml
<resultMap id="userResultMap" type="User">
  <id property="id" column="user_id" />
  <result property="username" column="user_name"/>
  <result property="password" column="user_password"/>
</resultMap>
```

然后在引用它的语句中设置 `resultMap` 属性就行了（注意我们去掉了 `resultType` 属性）。比如:

```xml
<!--查询语句，根据id查询单一对象-->
<!--这里使用resultMap = ""-->
<select id="getUserById" resultMap="userResultMap" >
    select user_id,user_name,user_password from mybatis.user where id = #{id};
</select>
```

#### 4.6 高级结果映射

如果这个世界总是这么简单就好了。

#### 4.7 自动映射

在简单的场景下，MyBatis 可以为你自动映射查询结果。但如果遇到复杂的场景，你需要构建一个结果映射。

当自动映射查询结果时，MyBatis 会获取结果中返回的列名并在 Java 类中查找相同名字的属性（忽略大小写）。 这意味着如果发现了 *ID* 列和 *id* 属性，MyBatis 会将列 *ID* 的值赋给 *id* 属性。

通常数据库列使用大写字母组成的单词命名，单词间用下划线分隔；而 Java 属性一般遵循驼峰命名法约定。为了在这两种命名方式之间启用自动映射，需要将 `mapUnderscoreToCamelCase` 设置为 true。

甚至在提供了结果映射后，自动映射也能工作。在这种情况下，对于每一个结果映射，在 ResultSet 出现的列，如果没有设置手动映射，将被自动映射。在自动映射处理完毕后，再处理手动映射。

#### 4.8 缓存

##### 4.8.1 什么是缓存

* 存在内存中的临时数据
* 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上（关系型数据库数据文件）查询，从缓存查询，从而提高查询效率，解决了高并发系统的性能问题

##### 4.8.2 为什么使用缓存

* 减少和数据库的交互次数，减少系统开销，提高系统效率

##### 4.8.3 什么样的数据可以使用缓存

* 经常查询并且不经常改变的数据

##### 4.8.4 MyBatis缓存

* MyBatis包含一个强大的查询缓存特性，可以方便的定制和配置缓存。缓存可以极大的提升查询效率。
* MyBatis系统默认定义了两级缓存：一级和二级缓存
  * 默认只有一级缓存开启。（SqlSession级别的缓存，也称本地缓存）
  * 二级缓存需要手动开启和配置，是基于namespace级别的缓存
  * 为了提高扩展性，MyBatis定义了缓存接口Cache，我们可以通过实现Cache接口来自定义二级缓存

##### 4.8.5 一级缓存

* 一级缓存也叫本地缓存
  * 与数据库同一次会话期间查询到的数据会放在本地缓存中
  * 以后如果需要获取相同的数据，直接从缓存中拿，没必要再去查询数据库；
* 一级缓存默认是开启的，只有在一次SqlSession中有效，也就是拿到连接到关闭连接这个区间段，一级缓存就是一个Map。

缓存失效的情况：

1. 查询不同的东西
2. 增删改操作，可能会改变原来的数据，所以必定会刷新缓存
3. 查询不同的Mapper.xml
4. 手动清除缓存

##### 4.8.6 二级缓存

* 二级缓存也叫全局缓存，一级缓存的作用域低
* 基于namespace级别的缓存，一个命名空间，对应一个二级缓存
* 工作机制：
  * 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中
  * 如果当前会话关闭了，这个会话的一级缓存就没了；但是我们如果想要会话关闭了，一级缓存中的数据保存到二级缓存中
  * 新的会话查询数据，就可以从二级缓存中获取内容
  * 不同的mapper查出的数据会放在自己对应的缓存（map）中

步骤：

1. 开启全局缓存

   ```xml
   <settings>
   	<!--显示的开启全局缓存-->
   	<setting name="cacheEnabled" value="true"/>
   </settings>
   ```

2. 在要使用二级缓存的Mapper中开启

   ```xml
   <!--在当前Mapper.xml中使用二级缓存-->
   <cache/>
   ```

   也可以在缓存中自定义参数

   ```xml
   <cache
     eviction="FIFO"
     flushInterval="60000"
     size="512"
     readOnly="true"/>
   ```

3. 测试

* 问题1：我们需要将实体类序列化

##### 4.8.6 缓存原理

1. 先看二级缓存有没有
2. 再看一级缓存有没有
3. 没有再查询数据库

##### 4.8.7 自定义缓存Ehcache

一般不用，实际会使用Redis

## page5：动态 SQL

动态 SQL 是 MyBatis 的强大特性之一。如果你使用过 JDBC 或其它类似的框架，你应该能理解根据不同条件拼接 SQL 语句有多痛苦，例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL，可以彻底摆脱这种痛苦。

* if
* choose (when, otherwise)
* trim (where, set)
* foreach

#### 5.1 if

使用动态 SQL 最常见情景是根据条件包含 where 子句的一部分。搜索条件可以多个的时候：

```xml
<select id="findActiveBlogWithTitleLike" resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

这条语句提供了可选的查找文本功能。如果不传入 “title”，那么所有处于 “ACTIVE” 状态的 BLOG 都会返回；如果传入了 “title” 参数，那么就会对 “title” 一列进行模糊查找并返回对应的 BLOG 结果（细心的读者可能会发现，“title” 的参数值需要包含查找掩码或通配符字符）。

多个条件的话，只需要加入另一个条件即可：

```xml
<select id="findActiveBlogWithTitleLike" resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

#### 5.2 choose、when、otherwise

我们不想使用所有的条件，而只是想从多个条件中选择一个使用。MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

传入了 “title” 就按 “title” 查找，传入了 “author” 就按 “author” 查找的情形。若两者都没有传入，就返回标记为 featured 的 BLOG（这可能是管理员认为，与其返回大量的无意义随机 Blog，还不如返回一些由管理员精选的 Blog）。

```xml
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

#### 5.3 trim、where、set

现在回到之前的 “if” 示例，这次我们将 “state = ‘ACTIVE’” 设置成动态条件，看看会发生什么。

```xml
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG WHERE
  <if test="state != null">
    state = #{state}
  </if>
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

如果没有匹配的条件会怎么样？最终这条 SQL 会变成这样：

```sql
SELECT * FROM BLOG WHERE
```

这会导致查询失败。如果匹配的只是第二个条件又会怎样？这条 SQL 会是这样:

```mysql
SELECT * FROM BLOG WHERE AND title like ‘someTitle’
```

这个查询也会失败。这个问题不能简单地用条件元素来解决。这个问题是如此的难以解决，以至于解决过的人不会再想碰到这种问题。

MyBatis 有一个简单且适合大多数场景的解决办法。而在其他场景中，可以对其进行自定义以符合需求。而这，只需要一处简单的改动：where用标签表示

```xml
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

*where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除。

如果 *where* 元素与你期望的不太一样，你也可以通过自定义 trim 元素来定制 *where* 元素的功能。比如，和 *where* 元素等价的自定义 trim 元素为：

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```

用于动态更新语句的类似解决方案叫做 *set*。*set* 元素可以用于动态包含需要更新的列，忽略其它不更新的列。比如：

```xml
<update id="updateAuthorIfNecessary">
  update User
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
    </set>
  where id=#{id}
</update>
```

这个例子中，*set* 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）。来看看与 *set* 元素等价的自定义 *trim* 元素吧：

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

#### 5.4 foreach

动态 SQL 的另一个常见使用场景是对集合进行遍历（尤其是在构建 IN 条件语句的时候）。比如：

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT * FROM POST P WHERE ID in
  <foreach item="item" index="index" collection="list" open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

* *foreach* 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。
* 允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符。这个元素也不会错误地添加多余的分隔符。

## page6：Java API

和 JDBC 相比，MyBatis 大幅简化你的代码并力图保持其简洁、容易理解和维护。

#### 6.1 目录结构

MyBatis 非常灵活，你可以随意安排你的文件。但和其它框架一样，目录结构有一种最佳实践。典型的应用目录结构：

```properties
/my_application
  /bin
  /devlib
  /lib                <-- MyBatis *.jar 文件在这里。
  /src
    /org/myapp/
      /action
      /data           <-- MyBatis 配置文件在这里，包括映射器类、XML 配置、XML 映射文件。
        /mybatis-config.xml
        /BlogMapper.java
        /BlogMapper.xml
      /model
      /service
      /view
    /properties       <-- 在 XML 配置中出现的属性值在这里。
  /test
    /org/myapp/
      /action
      /data
      /model
      /service
      /view
    /properties
  /web
    /WEB-INF
      /web.xml
```

#### 6.2 SqlSession

使用 MyBatis 的主要 Java 接口就是 SqlSession。你可以通过这个接口来执行命令，获取映射器示例和管理事务。

* SqlSessions 是由 SqlSessionFactory 实例创建的。
* SqlSessionFactory 对象包含创建 SqlSession 实例的各种方法。
* 而 SqlSessionFactory 本身是由 SqlSessionFactoryBuilder 创建的，它可以从 XML、注解或 Java 配置代码来创建 SqlSessionFactory。

##### 6.2.1 SqlSessionFactoryBuilder

SqlSessionFactoryBuilder 有五个 build() 方法，每一种都允许你从不同的资源中创建一个 SqlSessionFactory 实例。

```java
SqlSessionFactory build(InputStream inputStream)
SqlSessionFactory build(InputStream inputStream, String environment)
SqlSessionFactory build(InputStream inputStream, Properties properties)
SqlSessionFactory build(InputStream inputStream, String env, Properties props)
SqlSessionFactory build(Configuration config)
```

##### 6.2.2 SqlSessionFactory

SqlSessionFactory 有六个方法创建 SqlSession 实例。通常来说，当你选择其中一个方法时，你需要考虑以下几点：

- **事务处理**：你希望在 session 作用域中使用事务作用域，还是使用自动提交（auto-commit）？（对很多数据库和/或 JDBC 驱动来说，等同于关闭事务支持）
- **数据库连接**：你希望 MyBatis 帮你从已配置的数据源获取连接，还是使用自己提供的连接？
- **语句执行**：你希望 MyBatis 复用 PreparedStatement 和/或批量更新语句（包括插入语句和删除语句）吗？

基于以上需求，有下列已重载的多个 openSession() 方法供使用：

```java
SqlSession openSession()
SqlSession openSession(boolean autoCommit)
SqlSession openSession(Connection connection)
SqlSession openSession(TransactionIsolationLevel level)
SqlSession openSession(ExecutorType execType, TransactionIsolationLevel level)
SqlSession openSession(ExecutorType execType)
SqlSession openSession(ExecutorType execType, boolean autoCommit)
SqlSession openSession(ExecutorType execType, Connection connection)
Configuration getConfiguration();
```

默认的 openSession() 方法没有参数，它会创建具备如下特性的 SqlSession：

- 事务作用域将会开启（也就是不自动提交）。
- 将由当前环境配置的 DataSource 实例中获取 Connection 对象。
- 事务隔离级别将会使用驱动或数据源的默认设置。
- 预处理语句不会被复用，也不会批量处理更新。

##### 6.2.3 SqlSession

SqlSession 在 MyBatis 中是非常强大的一个类。它包含了所有执行语句、提交或回滚事务以及获取映射器实例的方法。

```java
/**
* 语句执行方法:这些方法被用来执行定义在 SQL 映射 XML 文件中的 SELECT、INSERT、UPDATE 和 DELETE 语句。
*/
<T> T selectOne(String statement, Object parameter)
<E> List<E> selectList(String statement, Object parameter)
<T> Cursor<T> selectCursor(String statement, Object parameter)
<K,V> Map<K,V> selectMap(String statement, Object parameter, String mapKey)
int insert(String statement, Object parameter)
int update(String statement, Object parameter)
int delete(String statement, Object parameter)
    
/**
* 立即批量更新方法:当你将 ExecutorType 设置为 ExecutorType.BATCH 时，可以使用这个方法清除（执行）缓存在 JDBC 驱动类中的批量更新语句。
*/
List<BatchResult> flushStatements()
    
/**
* 事务控制方法:有四个方法用来控制事务作用域。当然，如果你已经设置了自动提交或你使用了外部事务管理器，这些方法就没什么作用了。如果你正在使用由 
* Connection 实例控制的 JDBC 事务管理器，那么这四个方法就会派上用场：
*/
void commit()
void commit(boolean force)
void rollback()
void rollback(boolean force)
```

默认情况下 MyBatis 不会自动提交事务，除非它侦测到调用了插入、更新或删除方法改变了数据库。

如果你没有使用这些方法提交修改，那么你可以在 commit 和 rollback 方法参数中传入 true 值，来保证事务被正常提交（注意，在自动提交模式或者使用了外部事务管理器的情况下，设置 force 值对 session 无效）。

大部分情况下你无需调用 rollback()，因为 MyBatis 会在你没有调用 commit 时替你完成回滚操作。不过，当你要在一个可能多次提交或回滚的 session 中详细控制事务，回滚操作就派上用场了。

**本地缓存**

Mybatis 使用到了两种缓存：本地缓存（local cache）和二级缓存（second level cache）。

* 每当一个新 session 被创建，MyBatis 就会创建一个与之相关联的本地缓存。

* 任何在 session 执行过的查询结果都会被保存在本地缓存中，所以，当再次执行参数相同的相同查询时，就不需要实际查询数据库了。
* 本地缓存将会在做出修改、事务提交或回滚，以及关闭 session 时清空。
* 默认情况下，本地缓存数据的生命周期等同于整个 session 的周期。

你可以随时调用以下方法来清空本地缓存：

```java
void clearCache()
```

**确保 SqlSession 被关闭**

```java
void close()
```

对于你打开的任何 session，你都要保证它们被妥善关闭，这很重要。保证妥善关闭的最佳代码模式是这样的：

```java
SqlSession session = sqlSessionFactory.openSession();
try (SqlSession session = sqlSessionFactory.openSession()) {
    // 假设下面三行代码是你的业务逻辑
    session.insert(...);
    session.update(...);
    session.delete(...);
    session.commit();
}
```

## page7：SQL 语句构建器

## page8：日志

Mybatis 通过使用内置的日志工厂提供日志功能。内置日志工厂将会把日志工作委托给下面的实现之一：

- SLF4J
- Apache Commons Logging
- Log4j 2
- Log4j
- JDK logging

MyBatis 内置日志工厂会基于运行时检测信息选择日志委托实现。它会（按上面罗列的顺序）使用第一个查找到的实现。当没有找到这些实现时，将会禁用日志功能。

不少应用服务器（如 Tomcat 和 WebShpere）的类路径中已经包含 Commons Logging。注意，在这种配置环境下，MyBatis 会把 Commons Logging 作为日志工具。

如果你的应用部署在一个类路径已经包含 Commons Logging 的环境中，而你又想使用其它日志实现，你可以通过在 MyBatis 配置文件 mybatis-config.xml 里面添加一项 setting 来选择其它日志实现。

```xml
<configuration>
  <settings>
    ...
    <setting name="logImpl" value="LOG4J"/>
    ...
  </settings>
</configuration>
```

可选的值有：SLF4J、LOG4J、LOG4J2、JDK_LOGGING、COMMONS_LOGGING、STDOUT_LOGGING、NO_LOGGING或者是实现了`org.apache.ibatis.logging.Log` 接口，且构造方法以字符串为参数的类完全限定名。你也可以调用以下任一方法来选择日志实现：

```java
org.apache.ibatis.logging.LogFactory.useSlf4jLogging();
org.apache.ibatis.logging.LogFactory.useLog4JLogging();
org.apache.ibatis.logging.LogFactory.useJdkLogging();
org.apache.ibatis.logging.LogFactory.useCommonsLogging();
org.apache.ibatis.logging.LogFactory.useStdOutLogging();
```

#### 8.1 日志配置

可以通过在包、映射类的全限定名、命名空间或全限定语句名上开启日志功能，来查看 MyBatis 的日志语句。具体配置步骤取决于日志实现。

配置日志功能非常简单：添加一个或多个配置文件（如 log4j.properties），有时还需要添加 jar 包（如 log4j.jar）。下面的例子将使用 Log4J 来配置完整的日志服务。一共两个步骤：

##### 步骤 1：添加 Log4J 的 jar 包

* 由于我们使用的是 Log4J，我们要确保它的 jar 包可以被应用使用。
* 需要将 jar 包添加到应用的类路径中。Log4J 的 jar 包可以[点击下载](http://www.slf4j.org/)。
* 对于 web 应用或企业级应用，你可以将 `log4j.jar` 添加到 `WEB-INF/lib` 目录下；对于独立应用，可以将它添加到 JVM 的 `-classpath` 启动参数中。

##### 步骤 2：配置 Log4J

配置 Log4J 比较简单。假设你需要记录这个映射器的日志：

```java
package org.mybatis.example;
public interface BlogMapper {
  @Select("SELECT * FROM blog WHERE id = #{id}")
  Blog selectBlog(int id);
}
```

在应用的类路径中创建一个名为 `log4j.properties` 的文件，文件的具体内容如下：

```properties
# 全局日志配置
log4j.rootLogger=ERROR, stdout
# MyBatis 日志配置
log4j.logger.org.mybatis.example.BlogMapper=TRACE
# 控制台输出
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

上述配置将使 Log4J 详细打印 `org.mybatis.example.BlogMapper` 的日志，**对于应用的其它部分，只打印错误信息**。

为了实现更细粒度的日志输出，你也可以只打印特定语句的日志。以下配置将只打印语句 `selectBlog` 的日志：

```properties
log4j.logger.org.mybatis.example.BlogMapper.selectBlog=TRACE
```

或者，你也可以打印一组映射器的日志，只需要打开映射器所在的包的日志功能即可：

```properties
log4j.logger.org.mybatis.example=TRACE
```

某些查询可能会返回庞大的结果集。这时，你可能只想查看 SQL 语句，而忽略返回的结果集。为此，SQL 语句将会在 DEBUG 日志级别下记录（JDK 日志则为 FINE）。返回的结果集则会在 TRACE 日志级别下记录（JDK 日志则为 FINER)。因此，只要将日志级别调整为 DEBUG 即可：

```properties
log4j.logger.org.mybatis.example=DEBUG
```

但如果你要为下面的映射器 XML 文件打印日志，又该怎么办呢？

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
```

这时，你可以通过打开命名空间的日志功能来对整个 XML 记录日志：

```properties
log4j.logger.org.mybatis.example.BlogMapper=TRACE
```

而要记录具体语句的日志，可以这样做：

```properties
log4j.logger.org.mybatis.example.BlogMapper.selectBlog=TRACE
```

