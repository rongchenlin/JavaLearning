# IO流

## File类的使用

 * 1. File类的一个对象，代表一个文件或一个文件目录(俗称：文件夹)
 * 2. File类声明在java.io包下。
 * 3. **File**类中涉及到关于**文件或文件目录的创建、删除、重命名、修改时间、文件大小等方法**
 *    4. 并未涉及到写入或读取文件内容的操作。**如果需要读取或写入文件内容，必须使用IO流来完成**。

```Java
public class Test {

    public static void main(String[] args) {
        // 注意：这里的路径是以module为单位的，不是src这个目录，而是Test这个目录
        File file = new File("hello.txt");
        //相对路径
        System.out.println(file.getAbsolutePath());
        // 路径
        System.out.println(file.getPath());
        // 名字
        System.out.println(file.getName());
        // 父目录：如果写的是相对路径，则null
        System.out.println(file.getParent());
        // 长度
        System.out.println(file.length());
        // 最后修改时间
        System.out.println(new Date(file.lastModified()));
        
        File file2 = new File("C:\\Program Files\\");
        // 当前目录的所有文件的名称
        String[] list = file2.list();
        for (String s : list) {
            System.out.print(s);
        }

        // 返回目录中所有文件的File数组
        File[] files = file2.listFiles();
        for (File s : files) {
            System.out.print(s);
        }
    }
}
```

### 常用方法

```
public boolean isDirectory()：判断是否是文件目录
public boolean isFile() ：判断是否是文件
public boolean exists() ：判断是否存在
public boolean canRead() ：判断是否可读
public boolean canWrite() ：判断是否可写
public boolean isHidden() ：判断是否隐藏
public boolean createNewFile() ：创建文件。若文件存在，则不创建，返回false
public boolean mkdir() ：创建文件目录。如果此文件目录存在，就不创建了。如果此文件目录的上层目录不存在，也不创建。
public boolean mkdirs() ：创建文件目录。如果此文件目录存在，就不创建了。如果上层文件目录不存在，一并创建
public boolean delete()：删除文件或者文件夹。删除注意事项：Java中的删除不走回收站。
public boolean renameTo(File dest):把文件重命名为指定的文件路径
     比如：file1.renameTo(file2)为例：
```

```Java
File fileD = new File("res");
File file1 = new File("res/test.txt");
if (!fileD.exists()){
    /**
     * 如果不存在，创建
     */
    fileD.mkdirs();
    System.out.println("文件夹res创建成功！");
}
if(!file1.exists()){
    /**
     * 创建文件
     */
    file1.createNewFile();
    System.out.println("文件test创建成功");
}else{
    /**
     * 重命名
     */
    file1.renameTo(new File("res/new_name.txt"));
}
```

##  流的分类：

 * 1.操作数据单位：字节流、字符流
 * 2.数据的流向：输入流、输出流
 * 3.流的角色：节点流、处理流



 * 二、流的体系结构
 * 抽象基类                 节点流（或文件流）                                      缓冲流（处理流的一种）
 * **InputStream**     FileInputStream   (read(byte[] buffer))           BufferedInputStream (read(byte[] buffer))
 * **OutputStream**  FileOutputStream(write(byte[] buffer,0,len)  BufferedOutputStream (write(byte[] buffer,0,len) / flush()
 * **Reader**                 FileReader (read(char[] cbuf))                         BufferedReader (read(char[] cbuf) / readLine())
 * **Writer**                  FileWriter (write(char[] cbuf,0,len)                BufferedWriter (write(char[] cbuf,0,len) / flush()

#### InputStream 和 Reader 是所有输入流的基类。

- InputStream（典型实现：**FileInputStream**） ：**一般用来处理字节byte**
  - int read()
  - int read(**byte**[] b)
  - int read(byte[] b, int off, int len) 
- Reader（典型实现：**FileReader**）：**一般用来处理字符char**
  - int read()
  - int read(**char** [] c)
  - int read(char [] c, int off, int len)

#### OutputStream 和 Writer 是所有输出流的基类。

- OutputStream：（典型实现：**FileOutputStream**）
  - void write(int b/int c);
  - void write(byte[] cbuf);
  - void write(byte[] buff, int off, int len);
  - void flush();
  - void close(); 需要先刷新，再关闭此流

- Writer：（典型实现：**FileWriter**）
  - void write(int b/int c);
  - void write(char[] cbuf);
  - void write(char[] buff, int off, int len);
  - void flush();
  - void close(); 需要先刷新，再关闭此流

==上面4种流，说的通俗一些，就是首先我们可以根据输入输出分成两类：Input和Output，然后输入和输出又可以选择是字符还是字节输入或者输出==

### 节点流&处理流

节点流：直接从数据源或目的地读写数据

处理流：不直接连接到数据源或目的地，而是**“连接”在已存在的流（节点流或处理流）之上**，通过对数据的处理为程序提供更为强大的读写功能

### 节点流的基本使用：4个步骤

#### FileReader:字符输入流

```java
public class Test {

    public static void main(String[] args) throws IOException {
        // 1- 实例化文件对象
        File file1 = new File("res/test.txt");
        // 2- 提供具体使用的流, FileReader:输入流
        FileReader fr = new FileReader(file1);
        // 3- 数据读入
        int len;
        // 可以设置每次读的字符数
        char[] cbuf =new char[50];
        while ((len = fr.read(cbuf)) != -1){
            // 方式一：使用String读取
            String str = new String(cbuf,0,len);
            System.out.print(str);

            // 方式二：循环遍历读取cbuf
            for (int i = 0; i < len; i++) {
                System.out.print(cbuf[i]);
            }
        }
        // 4- 关闭流
        fr.close();
    }
}

```

#### FileWriter:字符输出流

```java
public class Test {
    /**
     * File对应的硬盘中的文件如果不存在，在输出的过程中，会自动创建此文件。
     * File对应的硬盘中的文件如果存在：
     *           如果流使用的构造器是：FileWriter(file,false) / FileWriter(file):对原有文件的覆盖
     *           如果流使用的构造器是：FileWriter(file,true):不会对原有文件覆盖，而是在原有文件基础上追加内容
     */

    public static void main(String[] args) throws IOException {
        // 1- 实例化文件对象
        File file1 = new File("res/rr.txt");
        // 2- 提供具体使用的流, FileWriter:输出流
        FileWriter fw = new FileWriter(file1,true);
        // 3- 数据写出
        fw.write("这是第一行\n");
        fw.write("这是第二行\n");
        // 4- 关闭流
        fw.close();
    }
}
```

#### FileInputStream：字节输入流

#### FileOutputStream：字节输入流

结论：
 * 1. 对于文本文件(.txt,.java,.c,.cpp)，使用字符流处理
 * 2. 对于非文本文件(.jpg,.mp3,.mp4,.avi,.doc,.ppt,...)，使用字节流处理

### 处理流的使用之一：缓冲流的使用

1.缓冲流：
 * BufferedInputStream
 * BufferedOutputStream
 * BufferedReader
 * BufferedWriter

2.作用：

- **提高流的读取、写入的速度**

 * 提高读写速度的原因：内部提供了一个**缓冲区**

#### 实践

```Java
public class Test {

    public static void main(String[] args) throws IOException {
        /**
         * 实现图片（非文本文件，即用字节的方式）的复制
         */
        // 1- 造文件
        File srcFile = new File("res/1.jpg");
        File desFile = new File("res/2.jpg");
        // 2- 造流
        FileInputStream fis = new FileInputStream(srcFile);
        FileOutputStream fos = new FileOutputStream(desFile);
        BufferedOutputStream bos = new BufferedOutputStream(fos);
        BufferedInputStream bis = new BufferedInputStream(fis);
        // 3. 复制文件：读文件 和 写文件
        byte[] buffer = new byte[10];
        int len = 0;
        while ((len = bis.read(buffer)) != -1){
            bos.write(buffer,0,len);
        }
        // 4- 关闭资源：先关外面，再关里面(关闭外层，内层也会自动关闭）
        bis.close();
        bos.close();
    }
}
```

### 处理流之二：转换流的使用

 * 1.转换流：属于字符流
 *   InputStreamReader：将一个字节的输入流转换为字符的输入流
 * OutputStreamWriter：将一个字符的输出流转换为字节的输出流
 * 2.作用：**提供字节流与字符流之间的转换**
 * 3.解码：字节、字节数组  --->字符数组、字符串
 *      编码：字符数组、字符串 ---> 字节、字节数组

#### 实践

```Java
public void test2() throws Exception {
    //1.造文件、造流
    File file1 = new File("dbcp.txt");
    File file2 = new File("dbcp_gbk.txt");

    FileInputStream fis = new FileInputStream(file1);
    FileOutputStream fos = new FileOutputStream(file2);

    InputStreamReader isr = new InputStreamReader(fis,"utf-8");
    OutputStreamWriter osw = new OutputStreamWriter(fos,"gbk");

    //2.读写过程
    char[] cbuf = new char[20];
    int len;
    while((len = isr.read(cbuf)) != -1){
        osw.write(cbuf,0,len);
    }

    //3.关闭资源
    isr.close();
    osw.close();

}
```

## 对象流的使用

 * 1.ObjectInputStream 和 ObjectOutputStream
 * **2.作用：用于存储和读取基本数据类型数据或对象的处理流。它的强大之处就是可以把Java中的对象写入到数据源中，也能把对象从数据源中还原回来。**
 * 3.要想一个java对象是可序列化的，需要满足相应的要求：
      * 1.需要实现接口：Serializable
      * 2.当前类提供一个全局常量：serialVersionUID
 * 4.序列化机制：
      * 对象序列化机制允许把内存中的Java对象转换成平台无关的二进制流，从而允许把这种二进制流持久地保存在磁盘上，或通过网络将这种二进制流传输到另一个网络节点。当其它程序获取了这种二进制流，就可以恢复成原来的Java对象。

#### 实践

```java
public class Test {

    public static void main(String[] args) throws IOException, ClassNotFoundException {
        oos();
        ois();
    }
    public static void oos() throws IOException {
        /**
         * 将对象写入文件
         */
        // 1- 造文件、对象流
        ObjectOutputStream os = new ObjectOutputStream(new FileOutputStream("res/test.dat"));
        // 2- 对象写入test.dat
        os.writeObject(new String("我爱北京天安门"));
        os.flush();

        os.writeObject(new String("hello!"));
        os.flush();
        // 3- 关闭资源
        os.close();
    }
    public static void ois() throws IOException, ClassNotFoundException {
        /**
         * 将文件中对象读出
         */
        ObjectInputStream is = new ObjectInputStream(new FileInputStream("res/test.dat"));
        Object obj = is.readObject();
        String str = (String) obj;
        System.out.println(str);
        is.close();
    }
}
```

