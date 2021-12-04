# SpringBoot2

## 1.自动配置原理

### 1.1 starter场景启动器

1. 某种常见场景如开发web项目的就是 spring-boot-starter-web，开发数据库jdbc的就是spring-boot-starter-data-jdbc，开发redis的就是 spring-boot-starter-data-redis
2. 见到很多 spring-boot-starter-* ： *就某种场景
3. 只要引入starter，这个场景的所有常规需要的依赖我们都自动引入。
4. SpringBoot所有支持的场景: [springboot文档](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter)

![image-20211130114539735](../../gitbook/markdownImages/image-20211130114539735.png)

5. 见到的  *-spring-boot-starter： 第三方为我们提供的简化开发的场景启动器。

6. 所有场景启动器最底层的依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
  <version>2.3.4.RELEASE</version>
  <scope>compile</scope>
</dependency>
```

### 1.2自动配置是什么？

为了能够使用容器中的对象，我们往往会在xml通过<bean>标签或者在类定义上使用@Component、@Configuration等注解，来实现其被spring容器管理，而对于jar包中的类，则稍微复杂一点，要根据jar包中类的实现进行相应引入。

<u>所谓自动配置</u>，spring boot的自动配置功能，会对我们配置的一些类，==**自动注入**==到spring容器中。特别是对于依赖的jar包中的一些类，当我们的工程用到这些类实例时，直接@Autowired或@Resource注解注入使用就可以了。

具体而言，自动配置可以实现下面的功能：

- 自动配好SpringMVC（视图解析器、文件上传解析器等等）
- 自动配好Web常见功能，如：字符编码问题
- 自动包扫描配置，默认的包结构
- - 主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来
- - 想要改变扫描路径，
    - @SpringBootApplication(scanBasePackages=**"com.atguigu"**)
- - - 或者@ComponentScan 指定扫描路径
- 各种配置拥有默认值

### 1.3自动配置原理

```java 
@springbootapplication相当于
    1.@SpringBootConfiguration  
    2.@EnableAutoConfiguration  
    3.@ComponentScan这三个注解。
```

- @SpringBootConfiguration只是Spring标准*@Configuration*批注的替代方法。**两者之间的唯一区别是@SpringBootConfiguration允许自动找到配置。**

- @ComponentScan 指定扫描哪些注解

- @EnableAutoConfiguration
  - @AutoConfigurationPackage
  - @import 将AutoConfigurationPackages包下的Registrar类作为组件导入到容器中，然后使用Registrar中的方法批量完成组件的注册。

总结：

- SpringBoot先加载所有的自动配置类  xxxxxAutoConfiguration
- **每个自动配置类按照条件进行生效**，默认都会绑定配置文件指定的值。xxxxProperties里面拿。xxxProperties和配置文件进行了绑定

- 生效的配置类就会给容器中装配很多组件
- 只要容器中有这些组件，相当于这些功能就有了

- 定制化配置

- - 用户直接自己@Bean替换底层的组件
  - 用户去看这个组件是获取的配置文件什么值就去修改。

**xxxxxAutoConfiguration ---> 组件  --->** **xxxxProperties里面拿值  ----> application.properties**

## 2.容器功能

### 2.1组件添加

#### @Configuration

**基本使用**

@Configuration告诉SpringBoot<u>这是一个配置类</u> ， 类似与之前的bean.xml配置文件，可以在这个类的方法里使用@Bean注解来注册组件（默认是单实例的组件），同时配置类本身也是组件。

@Configuration(proxyBeanMethods = false) 

 * Full(proxyBeanMethods = true) 【默认为true，保证每个@Bean方法被调用多少次返回的组件都是<u>单实例</u>的】
 * Lite(proxyBeanMethods = false)【每个@Bean方法被调用多少次返回的组件都是新创建的，<u>多实例</u>】

- **Full模式与Lite模式最佳实战：**
- - 配置 类组件之间**无依赖关系用Lite模式**加速容器启动过程，减少判断
  - 配置类组件之间**有依赖关系，方法会被调用得到之前单实例组件，用Full模式**

#### @Bean、@Component、@Controller、@Service、@Repository

在Stereotype（旧规矩）模式下，Spring为Controller-Service-Dao的分层模型分别提供了@Controller、@Service、@Repository注解，除此之外的组件使用@Component注解。

根据它们的源码可以看到，Controller、Service、Repository其本质就是Component。它存在的本质只是给开发者看的，对Spring而言它们就都是Component。

#### @ComponentScan、@Import

**@Import**

【注意】@Import所创建的实例在 IOC 容器中**默认的id名为类的全限定名**，如 User 类就是：com.atguigu.bean.User

**@ComponentScan**

@ComponentScan告诉Spring 哪个packages 的用注解标识的类 会被spring自动扫描并且装入bean容器。

例如，如果你有个类用@Controller注解标识了，那么，如果不加上@ComponentScan，自动扫描该controller，那么该Controller就不会被spring扫描到，更不会装入spring容器中，因此你配置的这个Controller也没有意义。

#### @Conditional

条件装配：满足Conditional指定的条件，则进行组件注入。

@Conditional，中派生了很多的子注解，它们<u>可以添加在@Bean注解的方法上也可以放在配置类</u>上，在方法上满足所需条件时则执行方法中内容并注册到 IOC 容器中如果不满足条件则不注册，<u>在配置类中满足需求时则执行配置类中所有的@Bean方法并注册到 IOC 容器中如果不满足条件则不注册，</u>以@ConditionalOnBean(name="tom")为例，当 IOC 容器中拥有id为tom的组件时才会满足条件，否则不满足条件。

![image.png](../../gitbook/markdownImages/1602835786727-28b6f936-62f5-4fd6-a6c5-ae690bd1e31d-16382560936462.png)

#### @ImportResource

原生配置文件引入，用法：@ImportResource("classpath:beans.xml")

### 配置绑定

读取properties文件中的内容，并且把它封装到JavaBean。要么加在容器中，要是开启绑定

#### @ConfigurationProperties

<u>@ConfigurationProperties注解+@Component(或@Controller或@Service或@Repository)注解只有在容器中的组</u>

<u>件，才会拥有SpringBoot提供的强大功能</u>，也就是如果我们需要使用到@ConfigurationProperties注解，那么我们首<u>先要</u>

<u>保证该对JavaBean对象在IoC容器中</u>，所以需要用到Component注解来添加组件到容器中。例如：

```java
@Component
@ConfigurationProperties(prefix = "mycar")
public class Car{

}
```

#### @EnableConfigurationProperties

<u>这种方式@EnableConfigurationProperties注解一定要在==配置类上==写</u>，@EnableConfigurationProperties的意思是

开启属性配置功能。

<u>这种方式主要用在引用第三方包时</u>，比如第三方包中有一个Person类，该类中没有使用@Component，我们也不能够给第三方包

中的类加上@Component，这个时候就可以使用在配置类上加上@EnableConfigurationProperties注解的方法。

例如：

```Java
@EnableConfigurationProperties(Person.class) //1、开启Pseron配置绑定功能 2、把这个Person组件自动注册到容器中
public class MyConfig {
}
```

