# Java自定义排序

## 自定义排序的方法

### 基于Comparator接口（不推荐）

**缺陷：**

- 需要创建一个新的类，用于自定义排序规则，十分麻烦
- 这种方法一般是用于补救措施，正常情况下编程不推荐使用。

**例如：**

```java
class Person {
    private String name;
    private Integer age;

    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public int getAge(){
        return this.age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}' ;
    }
}

// 创建一个新的比较器规则类来实现自定义排序，实现Person按照年龄排序
class PersonComparator implements Comparator<Person> {
    @Override
    public int compare(Person p1, Person p2) {
        return p1.getAge() - p2.getAge();
    }
}

@Test
public void testComparator() {
    Person[] data = new Person[]{
        new Person("张三", 20),
        new Person("李四", 19),
        new Person("王五", 21),
    };
    // 将person数组按照年龄age进行排序
    Arrays.sort(data, new PersonComparator());
    System.out.println(Arrays.toString(data));
}
```

**结果：**

`[Person{name='李四', age=19}, `

`Person{name='张三', age=20}, `

`Person{name='王五', age=21}]`

### 基于Comparable接口

**使用：**只要实现Comparable接口，并重写CompareTo方法即可，十分方便。

**例如：**

```Java
class Person implements Comparable<Person>{
    private String name;
    private Integer age;
    private Integer score;

    public Person(String name, Integer age, Integer score) {
        this.name = name;
        this.age = age;
        this.score = score;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", score=" + score +
                '}';
    }

    // 重写这个方法：先按照age排序，如果age相同，按照score排序
    @Override
    public int compareTo(Person p) {
        if (this.age > p.age) {
            return 1;
        } else if (this.age < p.age) {
            return -1;
        } else {
            // 年龄相同，按照score比较
            return this.score - p.score;
        }
    }
}

@Test
public void testComparator() {
    Person[] data = new Person[]{
        new Person("张三", 20,75),
        new Person("李四", 20,66),
        new Person("王五", 21,99),
    };
    Arrays.sort(data);
    System.out.println(Arrays.toString(data));
}
```

**结果：**
`[Person{name='李四', age=20, score=66}, `

`Person{name='张三', age=20, score=75}, `

`Person{name='王五', age=21, score=99}]`

## 其它数据结构的应用

### PriorityQueue按照数组某个元素进行排序

```Java
@Test
public void testTwo() {
    // 自定义：按照数组的第2个元素排序
    PriorityQueue<int []> priorityQueue = new PriorityQueue<>(new Comparator<int[]>() {
        @Override
        public int compare(int[] o1, int[] o2) {
            return o1[1] - o2[1];
        }
    });
    // 添加数组
    priorityQueue.add(new int[]{1, 99});
    priorityQueue.add(new int[]{2, 98});
    priorityQueue.add(new int[]{3, 100});
    // 遍历
    for (int[] i : priorityQueue) {
        System.out.println(i[0] + "---" + i[1]);
    }
}
```

**结果：**
`2---98
1---99
3---100`

