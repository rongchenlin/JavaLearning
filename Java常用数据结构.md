# 算法刷题常用的Java数据结构

- [算法刷题常用的Java数据结构](#算法刷题常用的java数据结构)
	- [import](#import)
	- [format](#format)
	- [Math](#math)
	- [HashSet](#hashset)
	- [HashMap](#hashmap)
	- [Stack](#stack)
	- [Queue](#queue)
	- [Array](#array)
	- [Character](#character)
	- [String](#string)
	- [StringBuffer](#stringbuffer)
	- [ArrayList](#arraylist)
	- [Others](#others)

## import

```Java
import java.util.*;
```

## format.

```Java
import java.util.Locale;   
public class TestNumber {
    public static void main(String[] args) {
        int year = 2020;
        //总长度，左对齐，补0，千位分隔符，小数点位数，本地化表达       
        //直接打印数字
        System.out.format("%d%n",year);
        //总长度是8,默认右对齐
        System.out.format("%8d%n",year);
        //总长度是8,左对齐
        System.out.format("%-8d%n",year);
        //总长度是8,不够补0
        System.out.format("%08d%n",year);
        //千位分隔符
        System.out.format("%,8d%n",year*10000);
  
        //小数点位数
        System.out.format("%.2f%n",Math.PI);
          
        //不同国家的千位分隔符
        System.out.format(Locale.FRANCE,"%,.2f%n",Math.PI*10000);     System.out.format(Locale.US,"%,.2f%n",Math.PI*10000);   System.out.format(Locale.UK,"%,.2f%n",Math.PI*10000);          
    }
}
```

## Math

```Java
		// 绝对值
		Math.abs(-12.3);			
						
		//e的x次幂
		Math.exp(2);		
		
		//e的x次幂 - 1
		Math.expm1(3);				
		// 返回最近的且大于这个数的整数
		Math.ceil(12.3);				// 13.0
		Math.ceil(-12.3);				//-12.0
		
		// 返回最近的且小于这个数的整数
		Math.floor(12.3);				//12.0
		Math.floor(-12.3);				//-13.0
		
		//返回概述的二次方根
		Math.sqrt(4);					
		
		//对数函数
		Math.log(1);					//1 以e为底的对数
		Math.log10(100);				//10 以10为底的对数
	
		//返回较大值和较小值
		Math.max(1,2);					
		Math.min(3,9);					
		
		//返回 x的y次幂			
		Math.pow(2, 3);					//即2³ 即返回：8
		
		//随机返回[0,1)之间的无符号double值
		Math.random();					
				
		//三角函数
		Math.sin(α);					//sin（α）的值
		Math.cos(α);					//cos（α）的值
		Math.tan(α);					//tan（α）的值
		
		//求角
		Math.asin(x/z);					//返回角度值[-π/2，π/2]  arc sin（x/z）
		Math.acos(y/z);					//返回角度值[0~π]   arc cos（y/z）
		Math.atan(y/x);					//返回角度值[-π/2，π/2]
		Math.atan2(y-y0, x-x0); 		//同上，返回经过点（x，y）与原点的的直线和经过点（x0，y0）与原点的直线之间所成的夹角
		
		Math.sinh(x);					//双曲正弦函数sinh(x)=(exp(x) - exp(-x)) / 2.0;
		Math.cosh(x);					//双曲余弦函数cosh(x)=(exp(x) + exp(-x)) / 2.0;
		Math.tanh(x);					//tanh(x) = sinh(x) / cosh(x);
		
		//角度弧度互换
		Math.toDegrees(angrad);			//角度转换成弧度，返回：angrad * 180d / PI	
		Math.toRadians(angdeg);			//弧度转换成角度，返回：angdeg / 180d * PI
```

## HashSet

```Java
		HashSet<String> set = new HashSet<String>();
		
		// 添加元素（无序的）
		set.add("Tom");
		set.add("Jerry");
		
		// 个数
		System.out.println(set.size());
		
		// 判空
		System.out.println(set.isEmpty());
		
		// 删除元素:romove() 返回true / false
		System.out.println(set.remove("Tom"));
		
		// 是否包含元素: contains(object);
		System.out.println(set.contains("Jerry"));
		
		// clear() ： 移除此Set中的所有元素
		set.clear();
```

## HashMap

```Java
		HashMap<String, String> map = new HashMap<String,String>();
		map.put("zhang", "31");//存放键值对

		// containsKey() 键中是否包含这个数据
		System.out.println(map.containsKey("zhang"));
		System.out.println(map.containsKey("daniu"));

		// get() 通过键拿值,如果没有，则返回null
		System.out.println(map.get("zhang"));

		// 判空
		System.out.println(map.isEmpty());
		
		// 个数
		System.out.println(map.size());
		
		//从键值中删除，返回被删除Key对应的Value
		System.out.println(map.remove("zhang"));
		System.out.println(map);
		
		map.put("zhang", "31");
		map.put("cheng", "32");
		map.put("yun", "33");
		
		// 遍历Key的值
		for (String key : map.keySet()) {
			System.out.println(key);
		}
		
		// 遍历Value的值
		for (String values : map.values()) {
			System.out.println(values);
		}

		// 清空Map
		map.clear();
		System.out.println(map);
```

## Stack

```Java
		Stack<Integer> st = new Stack<Integer>();
		 
		 // 入栈
		 st.push(1);
		 st.push(2);
		 st.push(3);
		 st.push(4);
		 System.out.println(st);
		 
		 // 取栈顶，不出栈:st.peek();
		 System.out.println(st.peek());
		
	     // 出栈，同时获取元素:st.op();
		 System.out.println(st.pop());
		 System.out.println(st);
		 
		 // 栈判空
		 System.out.println(st.empty());
		 
		 // 获取栈中所有元素
		 List<Integer> list = new ArrayList<Integer>();
		 while(!st.empty()){
			 list.add(0,st.pop());  // 注意，要从0开始add
		 } 
		 System.out.println(list);
```

## Queue

```Java
		Queue<Integer> q = new LinkedList<Integer>();
		 
		 // 入队
		 q.offer(1);
		 q.offer(2);
		 q.offer(3);
		 System.out.println(q);
		 
		 // 获取队头，不出队：peek()
		 System.out.println(q.peek());
		 
		 // 出队 : poll();
		 System.out.println(q.poll());
		 System.out.println(q);
		 
		 // 判空 : isEmpty();
		 System.out.println(q.isEmpty());
		 
		 // 个数
		 System.out.println(q.size());
		 	
		 // 获取队列中所有元素
		 List<Integer> list = new ArrayList<Integer>();
		 while(!q.isEmpty()){
			 list.add(q.poll());  // 注意，要从0开始add
		 } 
		 System.out.println(list);
```



## Array

```Java
		int[] intArray = { 1, 2, 3, 4, 5 };
		
		// Array = > Set
		String[] stringArray = { "a", "b", "c", "d", "e" };
		HashSet<String> set = new HashSet<String>(Arrays.asList(stringArray));
		System.out.println(set);
		
		// 排序
		Arrays.sort(intArray);
		
		// 数组填充
		int[] a = new int[20];
		Arrays.fill(a, 1);
		
		// 二分查找
		Arrays.binarySearch(a, 5);

		// 二维数组
		int[][] b = new int[2][4];
		int n = b.length;  // 行
		int m = b[0].lengtth; // 列
```

## Character

```
char x = 'Z';
例：
if(Character.isLetter(x)){
	System.out.println('是');
}
1. isLetter();
2. isDigit();
3. isUpperCase();  //是大写zim
4. isLowerCase();
5. toUpperCase();
6. toLowerCase();
7. toString();     // char => String
```

## String

- ### 字符数组和字符串的转换

```Java
1. char[]  = >  String
char[] helloArray = { 'h', 'e', 'l', 'l', 'o', '.'};
String helloString = new String(helloArray); 
System.out.println( helloString );

2. String  = > char[]
String s = "hsdahda";
char[] cs = s.toCharArray();
```

- ### String类方法 [1](https://www.jb51.net/article/79561.htm)


```Java
1. 获取长度：s.length()
2. 拼接字符串String： s3 =  s1 + s2;
3. s.charAt(i);  //第i个元素
4. int lastIndexOf(String str, int fromIndex) // 返回索引这个字符串中指定子字符串的最后出现处，从指定的索引开始处向后搜索。
5. int lastIndexOf(int ch, int fromIndex)     // 返回此字符串指定字符最后一次出现处的索引，从指定索引开始向后搜索。
6. int indexOf(int ch, int fromIndex)         // 返回索引这个字符串中指定字符第一次出现处，指定索引处开始搜索。
7. int indexOf(String str,int fromIndex)      // 返回这个字符串中指定子字符串的第一次出现处的索引，从指定的索引处开始。
8. s= s.trim();          // 去除头尾空格
9. s = s.toLowerCase();  // 小写
10.s = s.toUpperCase();
11. String s1 = s.substring(5,8);  // 获取s的5-8位置的字符子串
12. System.out.println(s1.startsWith("DA")); // 是否以"DA"为前缀
13. System.out.println(s1.endsWith("D"));       		// 是否以"D"为后缀结束   
14. System.out.println(s.replaceFirst("爱","恨" ));      //只替换第一个
15. System.out.println(s.replace("爱","恨" ));       		 //全部替换
16. System.out.println(s1.equals(s2));               // 字符串比较
17. System.out.println(s.compareTo(s1));                 // 字符串比较
```

- ### String与其他类型的转换 [2](https://www.cnblogs.com/huxiuqian/p/10167415.html)


| 数据类型 | 字符串转换为其他数据类型的方法 | 其它数据类型转换为字符串的方法1 | 其他数据类型转换为字符串的方法2 |
| -------- | ------------------------------ | ------------------------------- | ------------------------------- |
| byte     | Byte.parseByte(str)            | String.valueOf([byte] bt)       | Byte.toString([byte] bt)        |
| int      | Integer.parseInt(str)          | String.valueOf([int] i)         | Int.toString([int] i)           |
| long     | Long.parseLong(str)            | String.valueOf([long] l)        | Long.toString([long] l)         |
| float    | Float.parseFloat(str)          | String.valueOf([float] f)       | Float.toString([float] f)       |
| double   | double.parseDouble(str)        | String.valueOf([double] d)      | Double.toString([double] b)     |
| char     | str.charAt()                   | String.valueOf([char] c)        | Character.toString([char] c)    |
| boolean  | Boolean.getBoolean(str)        | String.valueOf([boolean] b)     | Boolean.toString([boolean] b)   |

```Java
例：
    int integer = Integer.parseInt("20"); //字符串类型转换为整形
	double d = Double.parseDouble("1.52123");//字符串类型转换为双精度浮点型
	String stri1 = String.valueOf(integer); //将整形转换为字符串类型
	String strl1 = String.valueOf(LongInt); //将长整型转换为字符串类型
```

## StringBuffer

```Java
// 初始化
		StringBuffer sb = new StringBuffer();
		System.out.println(sb);
		
		//StringBuffer构造方法1
        StringBuffer sb1=new StringBuffer("Hello");
        System.out.println(sb1);
        
        // String => StringBuffer
        String s1="World";
        StringBuffer sb2=new StringBuffer(s1);
        System.out.println(sb2);
        
        // StringBuffer => String
        String ss = sb2.toString();
        System.out.println(ss);
         
        //length()返回字符串的长度
        System.out.println(sb2.length());
             
        // 添加各种类型
        sb1.append(" World");
        System.out.println(sb1);
         
        //public StringBuffer deleteCharAt(int index)
        //删除指定位置的字符
        sb1.deleteCharAt(0);
        System.out.println(sb1);
         
        //public StringBuffer delete(int start,int end)，删除[Start,end)
        sb1.delete(1, 3);
        System.out.println(sb1);
         
        //字符串反转
        sb1.reverse();
        System.out.println(sb1);

		// charAt(index);  获取指定位置字符，
		StringBuffer sb = new StringBuffer("This is a StringBuffer");
		System.out.println(sb.charAt(sb.length() - 1));

		// 删除元素
		StringBuffer sb = new StringBuffer("This is a StringBuffer!");
		sb.delete(0, 5);				   // 删除[0,5]
		System.out.println(sb.toString());
		sb.deleteCharAt(2);  				// 删除sb[2]
		System.out.println(sb.toString());

		// insert(index,type0; 在指定位置插入任意类型
		StringBuffer sb = new StringBuffer("This is a StringBuffer!");
		sb.insert(2, 'W');
		sb.insert(3, new char[] { 'A', 'B', 'C' });
		sb.insert(8, "abc");
		sb.insert(2, 3);
		sb.insert(3, 2.3f);
		sb.insert(6, 3.75d);
		sb.insert(5, 9843L);
		sb.insert(2, true);
		System.out.println(sb.toString());

		// 字符串中查找相关匹配字符串
		StringBuffer sb = new StringBuffer();
		sb.append("This is a StringBuffer");
		// indexOf 返回子字符串在字符串中最先出现的位置，如果不存在，返回负数
		System.out.println("sb.indexOf(\"is\")=" + sb.indexOf("is"));
		// 给indexOf方法设置参数，指定匹配的起始位置
		System.out.println("sb.indexOf(\"is\")=" + sb.indexOf("is", 3));
		// 返回子字符串在字符串中最后出现的位置，如果不存在，返回负数
		System.out.println("sb.lastIndexOf(\"is\") = " + sb.lastIndexOf("is"));
		// 给lastIndexOf方法设置参数，指定匹配的结束位置
		System.out.println("sb.lastIndexOf(\"is\", 1) = "+ sb.lastIndexOf("is", 1));

		// 字符串截取
		StringBuffer sb = new StringBuffer();
		sb.append("This is a StringBuffer");
		// 默认的终止位置为字符串的末尾
		System.out.print("sb.substring(4)=" + sb.substring(6));
		// substring方法截取字符串，可以指定截取的起始位置和终止位置
		System.out.print("\nsb.substring(4,9)=" + sb.substring(6, 12));
```

## ArrayList

```Java
		 List<String> list = new ArrayList<String>();		 
		 list.add("abc");		 
		 list.add("efg");		 
		 list.add("sdsd");		 
		 System.out.println(list);		 		
		 List<String> list2 = new ArrayList<String>();		 
		 list2.add("a22222bc");		 
		 list2.add("22222efg");		 
		 list2.add("s22222dsd");		 		 // 在指定位置添加		 
		 list.add(2,"sDD");		 
		 System.out.println(list);		 		 // 添加集合		 
		 list.addAll(list2);    // 将集合list2添加到list后面		 
		 System.out.println(list);		 		 // 在指定位置设置元素的值		 
		 list.set(0, "开始");		
		 System.out.println(list);		 		 // list是否包含"abc"		 
		 System.out.println(list.contains("abc"));		 		 // 从前往后查找元素的索引
		 System.out.println(list.indexOf("efg"));		 		 // 从后往前查找元素的索引		
		 System.out.println(list.lastIndexOf("a22222bc"));		 		 // 截取		 
		 List<String> list3 = list.subList(1, 3);		 
		 System.out.println(list3);		 		 // 元素个数/长度		 
		 System.out.println(list.size());		 		 //移除数组中的"abc"		
		 list.remove("abc");		 
		 System.out.println(list);		 		 //移除索引为index的数字元素		 
		 list.remove(0);		 
		 System.out.println(list);				 // 移除所有元素		 
		 list.removeAll(list);		 
		 System.out.println(list);		 // 将ArrayList => 数组		 
		 String[] arr = new String[list.size()];	     
		 list.toArray(arr);		 // 数组 => List : 使用Arrays.asList	     
		 List<String> list2=Arrays.asList(arr);	     
		 System.out.println(list2);	     	     // 数组 => ArrayList	     
		 ArrayList<String> list3 = new ArrayList<String>(Arrays.asList(arr));	     
		 System.out.println(list3);		 // 升序排序 : Comparator.naturalOrder()		 
		 list.sort(Comparator.naturalOrder());		 
		 System.out.println(list);		 		 // 降序排序 : Comparator.reverseOrder()		 
		 list.sort(Comparator.reverseOrder());		 System.out.println(list);
```

## LinkedList

- 双链表实现了`List`和`Deque`接口。

- | 构造方法                                                     |
    | ------------------------------------------------------------ |
    | `LinkedList()`  构造一个空列表。                             |
    | `LinkedList(Collection<? extends E> c)`  构造一个包含指定集合的元素的列表，按照它们由集合的迭代器返回的顺序。 |
    
    ![image-20211219110315669](../../gitbook/markdownImages/image-20211219110315669.png)
    
    ![image-20211219110339269](../../gitbook/markdownImages/image-20211219110339269.png)
    
    ![image-20211219110402062](../../gitbook/markdownImages/image-20211219110402062.png)
    
    ![image-20211219110421367](../../gitbook/markdownImages/image-20211219110421367.png)
    
    ![image-20211219110450564](../../gitbook/markdownImages/image-20211219110450564.png)
    
    ![image-20211219110524768](../../gitbook/markdownImages/image-20211219110524768.png)
    
    ![image-20211219110550140](../../gitbook/markdownImages/image-20211219110550140.png)
    
    ![image-20211219110608382](../../gitbook/markdownImages/image-20211219110608382.png)

## Others

- length,length(),size() 的使用与区别

  - length属性是**<u>针对数组</u>**说的，比如说你声明了一个数组，想知道这个数组的长度则用到了length这个属性。
  - length()方法是**<u>针对字符串String</u>**说的，如果想看这个字符串的长度则用到length()这个方法。
  - size()方法是**<u>针对泛型集合泛型集合(Collection)如Set、List、Map</u>**说的，如果想看这个泛型有多少个元素，就调用此方法来查看。数组没有size()方法。

- MAX_VALUE

  - ```java
        System.out.println(Integer.MAX_VALUE);

  - ```java
        System.out.println(Long.MAX_VALUE);
        System.out.println(Float.MAX_VALUE);

