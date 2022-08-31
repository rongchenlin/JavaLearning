# SpringBoot事务

## 基本使用

#### 一、说明介绍

①使用事务，我们只需要在需要事务的类或方法上使用**@Transactional 注解**即可，当注解在类上的时候意味着此类的所有**public方法**都是开启事务的。**被注解的方法都成为一个事务整体，同一个事务内共享一个数据库连接，所有操作同时发生。如果在事务内部执行过程中发生了异常，则事务整体会自动进行回滚。**

②任何的RuntimeExcetipn、Error将触发回滚，任何的checkedException不触发回滚，

@Transactional(rollbackFor=Exception.class)或者throw new RuntimeException()就可以解决checkedException不触发回滚。

③**当用作方法上时，该方法所在类上的注解将失效。**

④只有来自外部的方法调用才会引起事务行为，类内部方法调用本类内部的其他方法并不会引起事务行为。

⑤在入口类使用注解**@EnableTransactionManagement开启事务。**

#### 二、事务隔离级别

@Transactional(isolation = Isolation.DEFAULT)
DEFAULT ：默认值（也是SpringBoot的隔离级别默认值），表示使用底层数据库的默认隔离级别。大部分数据库为READ_COMMITTED(MySql默认隔离级别为REPEATABLE_READ)
READ_UNCOMMITTED ：一个事务可以读取另一个事务修改但还没有提交的数据。
READ_COMMITTED ：一个事务只能读取另一个事务已经提交的数据。
REPEATABLE_READ ：一个事务在整个过程中可以多次重复执行某个查询，并且每次返回的记录都相同。即使在多次查询之间有新增的数据满足该查询，这些新增的记录也会被忽略。
SERIALIZABLE ：所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰。

#### 三、**事务传播方式**

@Transactional(propagation = Propagation.REQUIRED)
<u>PROPAGATION_REQUIRED：支持当前事务，如果当前没有事务，就新建一个事务。</u>
<u>PROPAGATION_SUPPORTS：支持当前事务，如果当前没有事务，就以非事务方式执行。</u>
<u>PROPAGATION_MANDATORY：支持当前事务，如果当前没有事务，就抛出异常。</u>
PROPAGATION_REQUIRESNEW：新建事务，如果当前存在事务，把当前事务挂起。
PROPAGATION_NOT_SUPPORTED：以非事务方式执行，如果当前存在事务，就把当前事务挂起。
PROPAGATION_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。
PROPAGATION_NESTED：如果一个活动的事务存在，则运行在一个嵌套的事务中，如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作。

#### 四、SpringBoot中事务控制失效的原因

1. 检查你方法**是不是public的**（只有Public方法才能开启事务）
2. 检查你的异常类型是不是unchecked异常（默认Error和RuntimeException事务回滚）。
3. **检查异常是不是被catch住了（当异常被捕获后，并且没有再抛出，那么事务是不会回滚的。）**
4. 检查类有没有被Spring管理（**<u>方法被标注了@Transactional，但是类没有注解，没有被Spring管理也不会生效</u>**）
5. 代码写错了
6. **数据库引擎不支持事务（其 MyISAM 引擎是不支持事务操作的）**

#### 五、Transactional（）中的参数说明

1、 propagation：事务传播行为
2、 isolation：事务隔离级别
3、 timeout：超时时间，事务需要在一定的时间内提交，不提交则进行回滚；默认值是-1，表示无时间限制
4、 readOnly：是否只读
（1）.默认值为false，表示可以查询，可以增删改；
（2）.如果设置为true，只能查询操作。
5、rollbackFor：回滚，设置出现哪些异常进行事务回滚。
6、noRollbackFor：不回滚，设置出现哪些异常不进行回滚。

#### 六、思考：

1、事务传播方式交互使用，外层方法与内层方法事务传播方式不同：
①同一事物：PROPAGATION_REQUIRED，PROPAGATION_SUPPORTS，PROPAGATION_MANDATORY
②不同事物：PROPAGATION_REQUIRES_NEW，PROPAGATION_NOT_SUPPORTED，PROPAGATION_NEVER，PROPAGATION_NESTED，
**2、被catch后的异常处理：当 try catch 捕获了异常，并将异常处理掉了，事务不会回滚；如果非要写 try catch，需要 catch 后将异常throw出去，事物才能执行；**
3、PROPAGATION_NESTED嵌套事物：内层事物回滚，不影响外部事物执行，外部事物影响内部事务执行。

参考：https://www.cnblogs.com/l-liu/p/16169399.html

## 详细了解

#### 一、事务的作用：

**事务是为了解决数据安全问题而存在的。**

最经典的例子就是银行转账问题，A账户给B账户转账100元，A账户扣除100元后由于不可抗力因素导致程序中断，B账户没有收到那100元，A账户那100元凭空消失，肯定是不行的。A扣款和B收款操作要么同时成功，要么同时失败，这个时候就需要引入事务操作。

#### 二、事务管理方式

spring支持编程式**事务管理和声明式事务管理**两种方式。

**编程式事务管理**使用TransactionTemplate或者直接使用底层的PlatformTransactionManager。对于编程式事务管理，spring推荐使用TransactionTemplate。

**声明式事务管理**建立在AOP之上的。**其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。**声明式事务最大的优点就是不需要通过编程的方式管理事务，这样就不需要在业务逻辑代码中掺杂事务管理的代码，**只需在配置文件中做相关的事务规则声明(或通过基于@Transactional注解的方式)，便可以将事务规则应用到业务逻辑中。**

#### 三、@Transactional注解：

**它是声明式事务管理编程中使用的注解，放在接口实现类或接口实现方法上，并且只对public方法才起作用。只读的接口不需要事务管理，防止影响系统性能。**

@Transactional **实质是使用了 JDBC 的事务来进行事务控制的**，实现原理：

<u>事务开始时，通过AOP机制，生成一个代理connection对象，并将其放入 DataSource 实例的某个与 DataSourceTransactionManager 相关的某处容器中。在接下来的整个事务中，客户代码都应该使用该 connection 连接数据库，执行所有数据库命令。[不使用该 connection 连接数据库执行的数据库命令，在本事务回滚的时候得不到回滚]（物理连接 connection 逻辑上新建一个会话session；DataSource 与 TransactionManager 配置相同的数据源）</u>

事务结束时，回滚在第1步骤中得到的代理 connection 对象上执行的数据库命令，然后关闭该代理 connection 对象。（事务结束后，回滚操作不会对已执行完毕的SQL操作命令起作用）
————————————————
原文链接：https://blog.csdn.net/weixin_50626214/article/details/124947695

