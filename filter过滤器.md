# Filter过滤器

## 什么是Filter

1、Filter 过滤器它是 JavaWeb 的三大组件之一。三大组件分别是：Servlet 程序、Listener 监听器、Filter 过滤器。

2、Filter 过滤器它是 JavaEE 的规范。也就是接口。

3、Filter 过滤器它的作用是：**拦截请求**，过滤响应。

### Filter工作流程

![image-20211121201753780](../../gitbook/markdownImages/image-20211121201753780.png)

### 实战

#### 在web.xml上面配置filter——类似Servlet配置

```xml
<filter>
    <filter-name>FilterServlet</filter-name>
    <filter-class>FilterServlet</filter-class>
</filter>

<filter-mapping>
    <filter-name>FilterServlet</filter-name>
    <url-pattern>/login</url-pattern>
</filter-mapping>
```

#### 重写doFilter()方法

```Java
@Override
public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
    // 获取HttpServletRequest和HttpServletResponse
    HttpServletRequest request = (HttpServletRequest) servletRequest;
    HttpServletResponse response = (HttpServletResponse) servletResponse;

    // 获取session
    HttpSession session = request.getSession();
    session.getAttribute("username");

    // pass
}
```

### Filter生命周期

<img src="../../gitbook/markdownImages/image-20211121205447537.png" alt="image-20211121205447537" style="zoom:50%;" />



### FilterConfig 类

见名知义，它是 Filter 过滤器的配置文件类。 Tomcat 每次创建 Filter 的时候，也会同时创建一个 FilterConfig 类，这里包

含了 Filter 配置文件的配置信息。

 FilterConfig 类的作用是获取 filter 过滤器的配置内容 

- 1、获取 Filter 的名称 filter-name 的内容 
- 2、获取在 Filter 中配置的 init-param 初始化参数
- 3、获取 ServletContext 对象

### FIlter过滤链

<img src="../../gitbook/markdownImages/image-20211121205906939.png" alt="image-20211121205906939" style="zoom:50%;" />

### FIlter拦截路径

精确匹配

```
 <url-pattern>/target.jsp</url-pattern> 以上配置的路径，表示请求地址必须为：http://ip:port/工程路径/target.jsp 
```

目录匹配

``` 
 <url-pattern>/admin/*</url-pattern> 以上配置的路径，表示请求地址必须为：http://ip:port/工程路径/admin/* 
```

后缀名匹配

```
<url-pattern>*.html</url-pattern> 以上配置的路径，表示请求地址必须以.html 结尾才会拦截到 <url-pattern>*
```

