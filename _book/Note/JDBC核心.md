# JDBC核心

### 建立JDBC连接

```Java
FileInputStream fis = new FileInputStream("jdbc.properties");
Properties props = new Properties();
props.load(fis);
// 1 读取配置文件
String user = props.getProperty("user");
String password = props.getProperty("password");
String url = props.getProperty("url");
String driverClass = props.getProperty("driverClass");
// 2 加载驱动
Class.forName(driverClass);
// 3 获取连接
Connection conn = DriverManager.getConnection(url, user, password);
return conn;
```

### PrepareStatement进行CRUD

#### update

```Java
public void Update( String sql, Object ...args) throws Exception {
    Connection conn = new JDBCUtils().getConnect();
    // 编译sql语句
    PreparedStatement ps = conn.prepareStatement(sql);
    // 填充占位符
    for (int i = 0; i < args.length; i++) {
        ps.setObject(i + 1, args[i]);
    }
    // 执行
    ps.execute();
    // 关闭资源
    ps.close();
    conn.close();
    System.out.println("修改成功！");
}
```

#### query

```Java
public <T> List<T> getInstances(Class<T> clazz, String sql, Object... args) throws Exception {
    Connection conn = JDBCUtils.getConnect();
    PreparedStatement ps = conn.prepareStatement(sql);
    // 填充占位符
    for (int i = 0; i < args.length; i++) {
        ps.setObject(i + 1, args[i]);
    }
    // 执行获取结果集
    ResultSet re = ps.executeQuery();
    // 获取结果集的元数据
    ResultSetMetaData rsmd = re.getMetaData();
    // 获取列数
    int columnCount = rsmd.getColumnCount();
    // 创建bean集合
    ArrayList<T> list = new ArrayList<T>();
    // 遍历结果集
    while (re.next()) {
        // 获取运行时类的实例
        T t = clazz.newInstance();
        // 处理每一行中的每一个属性
        for (int i = 0; i < columnCount; i++) {
            // 获取列的值
            Object columuVale = re.getObject(i + 1);
            // 获取列的（别）名
            String columuLable = rsmd.getColumnLabel(i + 1);
            // 给指定对象t填充属性
            Field field = clazz.getDeclaredField(columuLable);
            field.setAccessible(true);
            field.set(t, columuVale);
        }
        list.add(t);
    }
    JDBCUtils.closeResource(conn,ps,re);
    return list;
}
```

#### Blob类型的操作

```Java
/**
 * 插入blob类型数据
 * @throws Exception
 */
@Test
public void blobTest() throws Exception {
    Connection conn = JDBCUtils.getConnect();
    String sql = "insert into `order`(order_name, order_date, photo) values(?, ?, ?)";
    PreparedStatement ps = conn.prepareStatement(sql);
    ps.setObject(1, "李四");
    ps.setObject(2, "2020-11-02");
    File file;
    FileInputStream is = new FileInputStream(new File("earth.jpg"));
    ps.setObject(3, is);
    ps.execute();
    JDBCUtils.closeResource(conn, ps);
}

/**
 * 查询blob类型数据
 * @throws Exception
 */
@Test
public void blobQuery() throws Exception {
    Connection conn = JDBCUtils.getConnect();
    String sql = "select photo from `order` where order_id = ?";
    PreparedStatement ps = conn.prepareStatement(sql);
    ps.setObject(1, 2);
    ResultSet rs = ps.executeQuery();
    if (rs.next()) {
        Blob photo = rs.getBlob("photo");
        InputStream is = photo.getBinaryStream();
        File file;
        FileOutputStream fos = new FileOutputStream("E:\\getDownload.jpg");
        byte[] buffer = new byte[1024];
        int len;
        while ((len = is.read(buffer)) != -1) {
            fos.write(buffer, 0, len);
        }
    }
    JDBCUtils.closeResource(conn, ps, rs);

}
```

### 批量插入

```Java
@Test
public void multiInsertTest() throws Exception {
    Connection conn = JDBCUtils.getConnect();
    // 设置不允许自动提交数据：对于DDL一般都是执行一次就提交，
    // 这里设置为取消自动提交，可以等到都插入完成，再进行提交
    conn.setAutoCommit(false);
    PreparedStatement ps = null;
    long start = System.currentTimeMillis();
    String sql = "insert into customer(name) values(?)";
    // 编译sql语句
    ps = conn.prepareStatement(sql);
    for (int i = 0; i < 100000; i++) {
        ps.setObject(1, "name_" + i);
        // 1 攒sql,攒成一批
        ps.addBatch();
        if (i % 500 == 0) {
            // 2 执行批
            ps.executeBatch();
            // 3 清空批
            ps.clearBatch();
        }
    }
    // 提交数据
    conn.commit();
    long end = System.currentTimeMillis();
    System.out.println("花费时间：" + (end - start));
    JDBCUtils.closeResource(conn, ps);
}
```

### Duird和dbutils使用

```Java
import bean.Customer;
import com.alibaba.druid.pool.DruidDataSourceFactory;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import org.apache.commons.dbutils.handlers.ScalarHandler;
import org.junit.Test;
import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.Connection;
import java.util.List;
import java.util.Properties;

/**
 * @author 林镕琛
 * @create 2021-11-07-15:08:29
 */
public class DruidTest {
    /**
     * 使用Druid获取连接
     * @return
     * @throws Exception
     */
    public Connection ConnTest() throws Exception {
        // 读取配置文件
        Properties pro = new Properties();
        InputStream is = ClassLoader.getSystemClassLoader().getResourceAsStream("druid.properties");
        pro.load(is);
        DataSource source = DruidDataSourceFactory.createDataSource(pro);
        return source.getConnection();
    }

    /**
     * 使用dbutils查询获取多条数据
     * @throws Exception
     */
    @Test
    public void dbutilsQuery() throws Exception {
        Connection conn = ConnTest();
        QueryRunner runner = new QueryRunner();
        String sql = "select id, name, email, birth from customer where id < ?";
        BeanListHandler<Customer> handler = new BeanListHandler<>(Customer.class);
        List<Customer> customerList = runner.query(conn, sql, handler, 20);
        customerList.forEach(System.out::println);
    }

    /**
     * 使用dautils获取记录数
     * @throws Exception
     */
    @Test
    public void dbutilsGetValueTest() throws Exception {
        Connection conn = ConnTest();
        QueryRunner runner = new QueryRunner();
        String sql = "select count(*) from customer";
        ScalarHandler handler = new ScalarHandler();
        Object query = runner.query(conn, sql, handler);
        System.out.println(query);

    }
}
```
