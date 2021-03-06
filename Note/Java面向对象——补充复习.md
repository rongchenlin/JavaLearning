# Java面向对象——补充复习

## Java面向对象学习的三条主线：（第4-6章）

 * Java类及类的成员：属性、方法、构造器；代码块、内部类
 * 面向对象的三大特征：封装性、继承性、多态性、(抽象性)
 * 其它关键字：this、super、static、final、abstract、interface、package、import等

## 匿名对象的使用

 * 1.理解：我们创建的对象，<u>没有显式的赋给一个变量名</u>。即为匿名对象
 * 2.特征：匿名对象**只能调用一次**。

## 方法的形参的传递机制：值传递

 * 形参：方法定义时，声明的小括号内的参数
 * 实参：方法调用时，实际传递给形参的数据
 * 值传递机制：
    * 如果参数是基本数据类型，此时实参赋给形参的是实参真实存储的**数据值**。
    * 如果参数是引用数据类型，此时实参赋给形参的是实参存储**数据的地址值**。

## JavaBean是什么

JavaBean是一种Java语言写成的可重用组件，是指符合如下标准的Java类：

- 类是公共的
- 有一个无参的公共的构造器
- 有属性，且有对应的get、set方法

## 构造器

 * 1.如果没有显式的定义类的构造器的话，则<u>系统默认提供一个空参的构造器</u>
 * 2.定义构造器的格式：权限修饰符  类名(形参列表){}
 * 3.一个类中定义的多个构造器，彼此构成重载
 * 4.<u>一旦我们显式的定义了类的构造器之后，系统就不再提供默认的空参构造器</u>
 * 5.一个类中，至少会有一个构造器。

## import关键字的注意点

 * 可以使用"xxx.*"的方式，表示可以导入xxx包下的所有结构
 * 如果使用的类或接口是本包下定义的，则可以省略import结构
 * 如果在源文件中，使用了<u>不同包下的同名的类</u>，则必须<u>至少有一个类需要以全类名的方式显示</u>。
 * **使用"xxx.*"方式表明可以调用xxx包下的所有结构。但是如果使用的是xxx子包下的结构，则仍需要显式导入**
 * import static:导入指定类或接口中的静态结构:属性或方法。 

## 继承

体现：一旦子类A继承父类B以后，子类A中就获取了父类B中声明的所有的属性和方法。特别的，<u>父类中声明为private的属性或方法，子类继承父类以后，仍然认为获取了父类中私有的结构</u>。只是因为封装性的影响，使得子类不能直接调用父类的结构而已。

## Java中关于继承性的规定：

 * 	  一个类可以被多个子类继承。
 *    Java中类的单继承性：**一个类只能有一个父类**
 *    子父类是相对的概念。
 *    子类直接继承的父类，称为：直接父类。间接继承的父类称为：间接父类
 *    子类继承父类以后，就获取了直接父类以及所有间接父类中声明的属性和方法
 *    如果我们没有显式的声明一个类的父类的话，则此类继承java.lang.Object

## 重写 or 覆写

- 子类重写的方法的方法名和形参列表与父类被重写的方法的方法名和形参列表相同
- <u>子类重写的方法的权限修饰符不小于父类被重写的方法的权限修饰符特殊情况：子类不能重写父类中声明为private权限的方法</u>

 *      	父类被重写的方法的返回值类型是void，则子类重写的方法的返回值类型只能是void
 *      	父类被重写的方法的返回值类型是A类型，则子类重写的方法的返回值类型可以是A类或A类的子类
 *      	父类被重写的方法的返回值类型是基本数据类型(比如：double)，则子类重写的方法的返回值类型必须是相同的基本数据类型(必须也是double)
 *		<u>子类重写的方法抛出的异常类型不大于父类被重写的方法抛出的异常类型</u>

## 子类对象实例化的全过程

 * 从结果上来看：（继承性）
    * 		子类继承父类以后，就获取了父类中声明的属性或方法。
    * 创建子类的对象，在堆空间中，就会加载所有父类中声明的属性。
 * 从过程上来看：<u>当我们通过子类的构造器创建子类对象时，我们一定会直接或间接的调用其父类的构造器，进而调用父类的父类的构造器，... 直到调用了java.lang.Object类中空参的构造器为止。正因为加载过所有的父类的结构，所以才可以看到内存中有父类中的结构，子类对象才可以考虑进行调用。</u>
 * 明确：**虽然创建子类对象时，调用了父类的构造器，但是自始至终就创建过一个对象，即为new的子类对象。**

## super调用类中属性和方法

 * 我们可以在子类的方法或构造器中。通过使用"super.属性"或"super.方法"的方式，显式的调用父类中声明的属性或方法。**但是，通常情况下，我们习惯省略"super."**
 * 特殊情况：**当子类和父类中定义了同名的属性时，我们要想在子类中调用父类中声明的属性，则必须显式的使用"super.属性"的方式，表明调用的是父类中声明的属性。**
 * 特殊情况：**当子类重写了父类中的方法以后，我们想在子类的方法中调用父类中被重写的方法时，则必须显式的使用"super.方法"的方式，表明调用的是父类中被重写的方法。**

## super调用构造器

 *   我们可以在子类的构造器中显式的使用"super(形参列表)"的方式，调用父类中声明的指定的构造器
 *   "super(形参列表)"的使用，**必须声明在子类构造器的首行！**
 *   我们在类的构造器中，**针对于"this(形参列表)"或"super(形参列表)"只能二选一，不能同时出现**
 *   在构造器的首行，没有显式的声明"this(形参列表)"或"super(形参列表)"，则<u>默认调用的是父类中空参的构造器：super()</u>

## 多态性 （也叫 向上转型）

 *   对象的多态性：<u>父类的引用指向子类的对象（或子类的对象赋给父类的引用）</u>   
 *   有了对象的多态性以后，我们**在编译期，只能调用父类中声明的方法，但在运行期，我们实际执行的是子类重写父类的方法。**
 * 对象的多态性，只适用于方法，不适用于属性（编译和运行都看左边）。

## == VS equal()

**==：**

 * 可以使用在基本数据类型变量和引用数据类型变量中
 * 如果比较的是**基本数据类型变量**：比较两个变量保存的**数据**是否相等。（**不一定类型要相同**）
 * 如果比较的是**引用数据类型变量**：比较两个对象的**地址值**是否相同，即两个引用是否指向同一个对象实体
 *  == 符号使用时，必须保证符号左右两边的变量类型一致。

**equals()方法的使用：**

 * 是一个方法，而非运算符

 * **只能适用于引用数据类型**

 * Object类中equals()的定义：

   ```
   public boolean equals(Object obj) {
           return (this == obj);
     }
   ```

 * 说明：Object类中定义的equals()和==的作用是相同的：比较两个对象的地址值是否相同.即两个引用是否指向同一个对象实体

 * 像**String、Date、File、包装类等都重写了Object类中的equals()方法**。重写以后，比较的不是两个引用的地址是否相同，而是**比较两个对象的"实体内容"是否相同**。

## JUnit的使用

 * 步骤：
 * 1.选中当前工程 - 右键选择：build path - add libraries - JUnit 4 - 下一步
 * 2.创建Java类，进行单元测试。
 *   此时的Java类要求：① 此类是public的  ②此类提供公共的无参的构造器
 * 3.此类中声明单元测试方法。
 * 此时的单元测试方法：方法的权限是public,没有返回值，没有形参
 * 4.此单元测试方法上需要声明注解：@Test,并在单元测试类中导入：import org.junit.Test;
 * 5.声明好单元测试方法以后，就可以在方法体内测试相关的代码。
 * 6.写完代码以后，左键双击单元测试方法名，右键：run as - JUnit Test

## static

static可以用来修饰：属性、方法、代码块、内部类。

使用static修饰属性：静态变量（或类变量）

静态变量：我们创建了类的多个对象，多个对象共享同一个静态变量。当通过某一个对象修改静态变量时，会导致其他对象调用此静态变量时，是修改过了的。

:star:static修饰属性的其他说明：
 * 			**静态变量随着类的加载而加载**。可以通过"类.静态变量"的方式进行调用
 *          **静态变量的加载要早于对象的创建**。
 *          **由于类只会加载一次，则静态变量在内存中也只会存在一份：存在方法区的静态域中。**

:star:static注意点：
 *    在静态的方法内，不能使用this关键字、super关键字
 *    关于静态属性和静态方法的使用，大家都从生命周期的角度去理解。**由于static修饰的属性/方法在类加载的时候就加载，是先于对象的加载，所以静态的不能去访问非静态的（因为非静态的加载得慢），而非静态的可以访问静态的。**

## abstract

abstract修饰类：抽象类
 * 		**此类不能实例化**
 *      抽象类中一定有构造器，便于子类实例化时调用（涉及：子类对象实例化的全过程）
 * 		开发中，都会提供抽象类的子类，让子类对象实例化，完成相关的操作
 * 		抽象方法只有方法的声明，没有方法体
 * 		包含抽象方法的类，一定是一个抽象类。反之，抽象类中可以没有抽象方法的。
 *      <u>若子类重写了父类中的所有的抽象方法后，此子类方可实例化</u>
 *        <u>若子类没有重写父类中的所有的抽象方法，则此子类也是一个抽象类，需要使用abstract修饰</u>

abstract不能用来修饰：属性、构造器等结构。

abstract不能用来修饰私有方法、静态方法、final的方法、final的类。**（因为abstract的出现就是要别人来继承它的，然而final，私有这些都是不能被继承的）**

## 内存分析图

![image-20211012210648560](../../../gitbook/markdownImages/image-20211012210648560.png)

## 向下转型

Java的向下转型简单地理解就是对Java对象进行强制类型转换（也叫做**造型**）。

:star:**为什么要有向下转型？**

在Java多态性（向上转型）中，实际上就是将子类对象转成父类对象，此时可以访问父类的属性/方法，但是**不能访问子类添加的属性/方法**。为了实现上面的目标，就引入了向下转型。

向下转型：使用强制类型转换符。使用强转时，可能出现ClassCastException的异常。**为了避免上面的问题，引入instanceof关键字的使用**。

 使用情境：为了避免在向下转型时出现ClassCastException的异常，我们在向下转型之前，先进行instanceof的判断，一旦返回true，就进行向下转型。如果返回false，不进行向下转型。

```Java
if(p2 instanceof Woman){
			Woman w1 = (Woman)p2;
			w1.goShopping();
			System.out.println("******Woman******");
		}
```

