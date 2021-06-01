官方文档：https://mybatis.org/mybatis-3/zh/index.html

简明教程：https://www.cnblogs.com/benjieqiang/category/1506031.html

# MyBatis 简介

MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。Mybatis通过**xml或注解**的方式将要执行的各种statement（statement、preparedStatemnt、CallableStatement）配置起来，并通过java对象和statement中的sql进行**映射生成最终执行的sql语句**，最后**由mybatis框架执行sql**并将结果映射成java对象并返回。

> MyBatis简化了原生JDBC操作，将SQL与Java程序分开，易于管理，且简单易学。

![image-20210511150615626](https://tva1.sinaimg.cn/large/008i3skNly1gqei4hbor8j312m0u0dkd.jpg)

mybatis 配置

- SqlMapConfig.xml，此文件作为mybatis的全局配置文件，配置了mybatis的运行环境等信息。

- mapper.xml文件即sql映射文件，文件中配置了操作数据库的sql语句。此文件需要在SqlMapConfig.xml中加载。

输入映射：相当于在jdbc中对preparedStatement设置参数

输出映射：相当于在jdbc中对取出来的resultSet一一对应到pojo中

> Executor 和 MappedStatement都是Mybatis封装好的底层对象

# 使用

**SqlMapConfig.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
<!--   配置环境-->
    <environments default="mysql">
 	    <!--配置mysql的环境-->
        <environment id="mysql">
  		   <!--配置事务的类型-->
            <transactionManager type="JDBC"></transactionManager>
		   <!--配置连接池-->
            <dataSource type="POOLED">
				<!--配置连接数据库的4个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://192.168.214.128:3306/mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="ben123"/>
            </dataSource>
        </environment>
    </environments>

<!--指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
        <mapper resource="com/ben/dao/IUserDao.xml"/>
    </mappers>
</configuration>
```

Mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace：用来区别不同的类的名字 -->
<mapper namespace="test">

    <!-- 通过Id查询一个用户   -->
    <select id="findPersonById" parameterType="Integer" resultType="Person">
        select * from person where id = #{v}
    </select>

</mapper>
```


> #{}:一个占位符。preparedStatement向占位符中设置值，**自动进行java类型和jdbc类型转换**。#{}可以有效防止sql注入。 #{}可以接收简单类型值或pojo属性值。 如果parameterType传输单个简单类型值，#{}括号中可以是value或其它名称。
>
> \${}:表示拼接sql串，直接将传入的值拼接在sql语句中，例如是字符串，就直接加在里面，不会加引号。



```java
import java.io.InputStream;
import java.util.List;

public interface PersonMapper {
     void insertPerson(Person person);
     void deletePersonById(Integer id);
     void updatePersonById(Person person);
     Person selectAll();
     Person findPersonById(Integer id);
}

```



MyBatis可以使用`Bean+Mapper接口+Mapper.xml+SqlMapConfig.xml`的方式，也可以不写接口，但采用接口的方式，在使用时，不用指定映射的方法，不用写字符串字面量，全部通过调用方法来实现CRUD。

> 使用接口时，接口名要与mapper.xml里面的`namespace`相同，方法名要与每一个sql映射的`id`对应

不用接口的使用方法如下：

```java
// 没有实现接口
InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(in);
try (SqlSession sqlSession = sqlSessionFactory.openSession()){
  // 主要是这一句，需要手工指定映射的sql语句
  List<Person> persons = sqlSession.selectList("test.selectAll");
  for (Person person : persons) {
    System.out.println(person);
  }
}
```

使用接口的使用方法如下：

```java
public void testAll() throws IOException{
  InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
  SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
  SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(in);
  try (SqlSession sqlSession = sqlSessionFactory.openSession()){
    // 主要是这两句，mybatis实现了映射接口，再通过这个对象来调用方法即可
    PersonMapper mapper = sqlSession.getMapper(PersonMapper.class);
    List<Person> people = mapper.selectAll();
    for (Person person : people) {
      System.out.println(person);
    }
  }
}
```



# 输入映射（papameterType）

1. 传递基本类型或String

   直接写类型名称。

2. POJO类

   类名或全限定名。在使用时通过`#{filedName}`或者#{objectName.filedName}即可。

3. 传递POJO包装类

   POJO包装类即含有一个POJO作为属性的类。使用方法同上，可以通过`#{filedName.filedName}`直接访问被包装类的某个属性。

4. 传入hashmap

   将hashmap作为参数，可以通过#{}或${}引用key作为参数。

# 输出映射（resultType）

1. 基本类型或String

   直接写类型名称。

2. POJO对象或者POJO对象集合

   写类名即可。返回集合时，也只需要写类名即可，但在接口处返回值要写成`List<Object>`。

   使用resultType需要POJO的属性名和数据库表中的列名相同，如果pojo的属性名和SQL中的列名不一致，可以通过resultMap将列名和POJO属性名作一个对应关系（底层再将查询结果注入到POJO中） 。

   resultMap标签使用方式：在`<mapper> </mapper>`标签里，定义一个`<resultMap id="..." type="...">`，其中id是一个标识符，一般取用xxxMap，type是指定POJO。在resultMap内部，使用`<id property="..." column="..."></id>` 来映射主键名，使用`<result property="..." column="..."></result>`来映射其它字段。最后在使用时，通过resultMap来指定先前定义的resultMap的id名即可。

   示例如下：

   ```xml
   <mapper namespace="com.ben.dao.IUserDao">
       <!-- 配置 查询结果的列名和实体类的属性名的对应关系 -->
       <resultMap id="userMap" type="com.ben.domain.User">
           <!-- 主键字段的对应 -->
           <id property="userId" column="id"></id>
           <!--非主键字段的对应-->
           <result property="userName" column="username"></result>
           <result property="userAddress" column="address"></result>
           <result property="userSex" column="sex"></result>
           <result property="userBirthday" column="birthday"></result>
       </resultMap>
       <!-- 查询所有 -->
       <select id="findAll" resultMap="userMap">
           select * from user;
       </select>
   </mapper>
   ```

   > 当pojo的属性名和SQL中的列名不一致时，还有一种解决方案是在SQL语句里，给每一个列用`AS`取别名，使对应于POJO中的列名

   

# 动态SQL

动态sql就是根据不同的条件生成不同的sql语句。

- if

  用来将sql中的where子句根据条件动态组合查询条件。

  用法：

  ```xml
  <select id="findActiveBlogWithTitleLike" resultType="Blog">
    SELECT * FROM BLOG
    WHERE 1=1
    <if test="title != null">
      and title = "peter"
    </if>
    <if test="sex != null">
      and sex = "man"
  </select>
  ```

   

- choose、when、otherwise

  相当于switch-case，when对应每一个case，otherwise相当于defalut

  ```xml
  <select id="findActiveBlogLike"
       resultType="Blog">
    SELECT * FROM BLOG WHERE 1=1
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

- trim（where，set）

  用来定制拼接的sql语句格式。

  第一个if里面的例子，如果去掉1=1，变成如下形式

  ```xml
  <select id="findActiveBlogWithTitleLike" resultType="Blog">
    SELECT * FROM BLOG
    WHERE
    <if test="title != null">
      and title = "peter"
    </if>
    <if test="sex != null">
      and sex = "man"
  </select>
  ```

  将会出现两类问题：

  1. title与sex都为空时，where缺少后置条件。

     ```sql
     SELECT * FROM BLOG
     WHERE
     ```

  2. 当第一个if语句没有匹配上，直接匹配上第二个语句时，where条件前面多了and

     ```sql
     SELECT * FROM BLOG
     WHERE
     AND title like ‘someTitle’
     ```

  为了解决如上问题，引入了where标签，替代sql中的where关键字，并包裹住所有的if条件语句。

  **where**的特点：

  - 只会在包裹的if子元素返回了内容的时候，才插入“where”子句
  - 若插入的where子句开头是`and`或者`or`就删去

  **set**同理，不过是用在update的时候，包裹住多个if子句

  - 自动插入set关键字
  - 会自动删除根据if语句生成的sql子句的最后一个逗号

  两者的实现都是基于trim标签，在里面设置**自动添加的前缀**（prefix/suffix），**自动删除的前/后缀**（prefixOverrides / suffixOverides）

  具体参考：https://mybatis.org/mybatis-3/zh/dynamic-sql.html

- foreach

  foreach可以根据参数传入的一个集合（List，数组等），遍历每一个元素，然后对每个元素进行引用、比较等操作，最后依据自定义的规则生成一个sql子句。常常用在构建IN子句上

  ```xml
  <select id="selectPostIn" resultType="domain.blog.Post">
    SELECT *
    FROM POST P
    WHERE ID in
    <foreach item="item" index="index" collection="list"
        open="(" separator="," close=")">
          #{item}
    </foreach>
  </select>
  ```

  > collection 指传入参数中集合的名称
  >
  > item表示集合中每个元素的值
  >
  > index表示集合中每个元素的索引
  >
  > open指生成的sql子句，怎样打头
  >
  > separator指每个元素之间应该用什么作为分割符
  >
  > close指生成的sql子句，怎样结尾

  > 使用hashmap时，index是key，item是value

  

# SQL片段

可以将xml里面的多个重复的sql语句片段抽取出来，放在`<sql id=""></sql>`里面，后续可以通过`<include refid="">...</include>`引用。常用来简化多次重复的公共sql语句。

- 不要放where在里面

# 日志

通过setting标签指定日志实现。

```xml
<setting name="logImpl" value="STDOUT_LOGGING"/>
```

其中value常用的取值：LOG4J、STDOUT_LOGGING。STDOUT_LOGGING填入无需其它配置就可直接使用

# 缓存

将多次重复查询的数据，存放在内存中，下次查询就可以直接读取。

- 一级缓存：默认开启，sqlsession级别的缓存。采用LRU策略。

> 即打开到关闭一个sqlsession，中间的操作都会启用缓存，关闭之后，缓存清除。

缓存失效的情况：

1. 增删改将会刷新缓存，即会删去缓存的值，下次查询重新走数据库
2. 手动调用clearCache方法，清理缓存

- 二级缓存：需要手动配置，namespace级别(对应一个mapper类)的**全局缓存**。一个会话关闭了，一级缓存中的数据将会保存到二级缓存中，启用新的会话，对同一类mapper进行查询，就可以从二级缓存中里面读。

要启用全局的二级缓存，只需要在SQL 映射文件中添加一行：

```xml
<cache/>
```

当然也可以通过cache的标签元素来设置一些属性

```xml
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

flushInterval（刷新间隔）属性可以被设置为任意的正整数，设置的值应该是一个以毫秒为单位的合理时间量。 默认情况是不设置，也就是没有刷新间隔，缓存仅仅会在调用语句时刷新。

size（引用数目）属性可以被设置为任意正整数，要注意欲缓存对象的大小和运行环境中可用的内存资源。默认值是 1024。

readOnly（只读）属性可以被设置为 true 或 false。只读的缓存会给所有调用者返回缓存对象的相同实例。 因此这些对象不能被修改。这就提供了可观的性能提升。而可读写的缓存会（通过序列化）返回缓存对象的拷贝。 速度上会慢一些，但是更安全，因此默认值是 false。

还可以使用type 指定自定义的cache

- **启用了二级缓存时，会先查看二级缓存有没有所需要的数据，再看一级缓存有没有所需要的数据，最后才对数据库进行sql语句查询**

# 事务

声明式事物：通过aop实现

编程式事物