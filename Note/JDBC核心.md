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

