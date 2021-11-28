# Mybatis笔记

[狂神说Mybatis笔记](https://www.cnblogs.com/renxuw/p/13047424.html)

## 创建一个HelloWorld程序

1. 创建一个mybatis-config.xml的配置文件
2. 创建Mybatis的工具类，获得sqlSession

```java
package com.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.Configuration;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;

public class MyBatisUtils {
    /**
     * 定义SqlSessionFactory，全局可用
     */
    private static SqlSessionFactory sqlSessionFactory;

    /**
     * 写成静态代码块，执行加载一次
     */
    static {
        try {
            String resource = "mybatis-config.xml";
            // 将配置文件转成流对象，用于后面的加载
            InputStream inputStream = Resources.getResourceAsStream(resource);
            // 创建SqlSessionFactory
            Configuration config;
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取sqlSession连接
     */
    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }
}
```

3. 编写业务接口，以及实现接口的xml配置文件，例如：UserMapper.clss 以及UserMapper.xml

```java
package com.dao;

import com.pojo.User;
import org.apache.ibatis.annotations.Param;
import java.util.List;
import java.util.Map;

public interface UserMapper {

    /**
     * 获取查询的全部结果
     */
    List<User> getUserList();

    /**
     * 多个字段查询
     * 在xml文件中实际是根据Param里面的映射字段匹配的
     */
    User getUserByIdAndName(@Param("userName") String name,
                            @Param("userId") int id);

    /**
     * 使用Map映射，很灵活
     */
    User getUserByMap(Map<String, Object> map);

    /**
     * 修改
     */
    int updateUser(User user);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--映射要实现的接口所在的全类名-->
<mapper namespace="com.dao.UserMapper">
    <!-- id：实现方法名 -->
    <select id="getUserList" resultType="User">
        select * from user
    </select>

    <select id="getUserByIdAndName" resultType="com.pojo.User">
        select * from user where id = #{userId} and name = #{userName}
    </select>

    <select id="getUserByMap" parameterType="map" resultType="com.pojo.User">
        select * from user where id = #{userId} and name = #{userName}
    </select>

    <update id="updateUser" parameterType="com.pojo.User">
        update user set name=#{name}, pwd=#{pwd} where id = #{id}
    </update>
</mapper>
```

4. 测试

## 核心配置

### 配置顺序

```xml
configuration（配置）
properties（属性）
settings（设置）
typeAliases（类型别名）
typeHandlers（类型处理器）
objectFactory（对象工厂）
plugins（插件）
environments（环境配置）
environment（环境变量）
transactionManager（事务管理器）
dataSource（数据源）
databaseIdProvider（数据库厂商标识）
mappers（映射器）
<!-- 注意元素节点的顺序！顺序不对会报错 -->
```

## 生命周期和作用域

###　作用域（Scope）和生命周期

理解我们目前已经讨论过的不同作用域和生命周期类是至关重要的，因为错误的使用会导致非常严重的并发问题。

我们可以先画一个流程图，分析一下Mybatis的执行过程！



<img src="https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7JdnS939HH5TayIhQo5s0aJbReBExSQO1U23XeLAXlhTWUeL87mJZL0lDzPstpY3CSIwvW0dN9ccA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1" alt="图片" style="zoom: 67%;" />

**作用域理解**

- SqlSessionFactoryBuilder 的作用在于创建 SqlSessionFactory，创建成功后，SqlSessionFactoryBuilder 就失去了作用，所以它只能存在于创建 SqlSessionFactory 的方法中，而不要让其长期存在。因此 **SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域**（也就是局部方法变量）。
- SqlSessionFactory 可以被认为是一个数据库连接池，它的作用是创建 SqlSession 接口对象。因为 MyBatis 的本质就是 Java 对数据库的操作，所以 SqlSessionFactory 的生命周期存在于整个 MyBatis 的应用之中，所以一旦创建了 SqlSessionFactory，就要长期保存它，直至不再使用 MyBatis 应用，所以可以认为 SqlSessionFactory 的生命周期就等同于 MyBatis 的应用周期。
- 由于 SqlSessionFactory 是一个对数据库的连接池，所以它占据着数据库的连接资源。如果创建多个 SqlSessionFactory，那么就存在多个数据库连接池，这样不利于对数据库资源的控制，也会导致数据库连接资源被消耗光，出现系统宕机等情况，所以尽量避免发生这样的情况。
- 因此在一般的应用中我们往往希望 SqlSessionFactory 作为一个单例，让它在应用中被共享。所以说 **SqlSessionFactory 的最佳作用域是应用作用域。**
- 如果说 SqlSessionFactory 相当于数据库连接池，那么 SqlSession 就相当于一个数据库连接（Connection 对象），你可以在一个事务里面执行多条 SQL，然后通过它的 commit、rollback 等方法，提交或者回滚事务。所以它应该存活在一个业务请求中，处理完整个请求后，应该关闭这条连接，让它归还给 SqlSessionFactory，否则数据库资源就很快被耗费精光，系统就会瘫痪，所以用 try...catch...finally... 语句来保证其正确关闭。
- **所以 SqlSession 的最佳的作用域是请求或方法作用域。**

## 自动映射

mybatis 中支持自动映射配置，当开启自动映射之后，**当 sql 的列名和 Model（也就是JavaBean） 中的字段名称是一样的时候（不区分大小写）**，mybatis 内部会进行自动映射，**去对应的实体类中查找相应列名的set方法设值** ,不需要我们手动去写下面的 4 行映射规则。

```xml
<id column="id" property="id"/>
<result column="userId" property="userId" />
<result column="createTime" property="createTime" />
<result column="upTime" property="upTime" />
```

### 手动映射

```xml
<resultMap id="UserMap" type="User">
   <!-- id为主键 -->
   <id column="id" property="id"/>
   <!-- column是数据库表的列名 , property是对应实体类的属性名 -->
   <result column="name" property="name"/>
   <result column="pwd" property="password"/>
</resultMap>

<select id="selectUserById" resultMap="UserMap">
  select id , name , pwd from user where id = #{id}
</select>
```

## 分页

> 分页一共有3种实现方式，但是本质上都是底层调用Limit进行分页实现的。

### **使用Limit实现分页**

- 接口

```java
/**
 * 分页
 */
List<User> getUserByPage(Map<String, Object> map);
```

- 实现

```xml
<select id="getUserByPage" resultType="com.pojo.User" parameterType="map">
    select * from user limit #{start} , #{pageSize}
</select>
```

### RowBounds分页

### PageHelper

## 面向接口编程

### 面向接口

- 在一个面向对象的系统中，系统的各种功能是由许许多多的不同对象协作完成的。在这种情况下，各个对象内部是如何实现自己的,对系统设计人员来讲就不那么重要了；
- 而各个对象之间的协作关系则成为系统设计的关键。小到不同类之间的通信，大到各模块之间的交互，在系统设计之初都是要着重考虑的，这也是系统设计的主要工作内容。面向接口编程就是指按照这种思想来编程。
- **根本原因 :  解耦 , 可拓展 , 提高复用 , 分层开发中 , 上层不用管具体的实现 , 大家都遵守共同的标准 , 使得开发变得容易 , 规范性更好**

### 三个面向

- 面向对象是指，我们考虑问题时，以对象为单位，考虑它的属性及方法 .
- 面向过程是指，我们考虑问题时，以一个具体的流程（事务过程）为单位，考虑它的实现 .
- 接口设计与非接口设计是针对复用技术而言的，与面向对象（过程）不是一个问题.**更多的体现就是对系统整体的架构**

## 利用注解开发

**mybatis最初配置信息是基于 XML ,映射语句(SQL)也是定义在 XML 中的。而到MyBatis 3提供了新的基于注解的配置。不幸的是，Java 注解的的表达力和灵活性十分有限。最强大的 MyBatis 映射并不能用注解来构建**

> 使用注解进行增删改之前，需要修改Mybatis配置文件中的事务自动提交
>
> ```Java
>   //获取SqlSession连接
>   public static SqlSession getSession(){
>       return getSession(true); //事务自动提交
>   }
>  
>   public static SqlSession getSession(boolean flag){
>       return sqlSessionFactory.openSession(flag);
>   }
> ```

```java
//根据id查询用户
@Select("select * from user where id = #{id}")
User selectUserById(@Param("id") int id);

//添加一个用户
@Insert("insert into user (id,name,pwd) values (#{id},#{name},#{pwd})")
int addUser(User user);

//根据id删除用
@Delete("delete from user where id = #{id}")
int deleteUser(@Param("id")int id);

//修改一个用户
@Update("update user set name=#{name},pwd=#{pwd} where id = #{id}")
int updateUser(User user);
```

### 关于@Param

@Param注解用于给方法参数起一个名字。以下是总结的使用原则：

- 在方法只接受一个参数的情况下，可以不使用@Param。
- 在方法接受多个参数的情况下，建议一定要使用@Param注解给参数命名。
- 如果参数是 JavaBean ， 则不能使用@Param。
- **不使用@Param注解时，参数只能有一个，并且是Javabean。**

## 逆向工程

[使用Mybaits Generaotr 文档](http://mybatis.org/generator/running/runningWithJava.html)

