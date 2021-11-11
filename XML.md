# XML

## 文本区域（CDATA 区） 

CDATA 语法可以告诉 xml 解析器，我 CDATA 里的文本内容，只是纯文本，不需要 xml 语法解析 CDATA 格式： 

```xml
<![CDATA[ 这里可以把你输入的字符原样显示，不会解析 xml ]]>
```

```xml
<book id="x002">
    <name><![CDATA[<<葵花宝典>>]]></name>
    <price>56</price>
</book>
```

## xml 解析技术介绍

xml 可扩展的标记语言。 不管是 html 文件还是 xml 文件它们都是标记型文档，都可以使用 w3c 组织制定的 dom 技术来解析。

### dom4j解析

由于 dom4j 它不是 sun 公司的技术，而属于第三方公司的技术，我们需要使用 dom4j 就需要到 dom4j 官网下载 dom4j 

的 jar 包。

### dom4j使用步骤

```java
// 1 创建SAXReader 对象，读取xml文件，获取Document对象
SAXReader saxReader = new SAXReader();
Document document = saxReader.read("src/xml/books.xml");
// 2 通过Document对象获取xml根元素
Element rootElement = document.getRootElement();
// 3 通过根元素对象获取所有的标签对象
List<Element> books = rootElement.elements("book");
// 4 遍历
for (Element book : books) {
    String name = book.element("name").getText();
    String price = book.element("price").getText();
    System.out.println("书名 : " + name + " 价格 ：" + price);
}
```


```xml
<?xml version="1.0" encoding="utf-8" ?>
<!-- xml声明 version是版本的意思   encoding是编码  -->
<books> <!-- 这是xml注释 -->
    <book id="SN123123413241"> <!-- book标签描述一本图书   id属性描述 的是图书 的编号  -->
        <name>java编程思想</name> <!-- name标签描述 的是图书 的信息 -->
        <author>华仔</author>       <!-- author单词是作者的意思 ，描述图书作者 -->
        <price>9.9</price>    <!-- price单词是价格，描述的是图书 的价格 -->
    </book>
    <book id="SN12341235123">  <!-- book标签描述一本图书   id属性描述 的是图书 的编号  -->
        <name>葵花宝典</name>  <!-- name标签描述 的是图书 的信息 -->
        <author>班长</author>    <!-- author单词是作者的意思 ，描述图书作者 -->
        <price>5.5</price> <!-- price单词是价格，描述的是图书 的价格 -->
    </book>
</books>
```

