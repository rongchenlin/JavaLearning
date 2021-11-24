# cookie和session

## cookie

Cookie 是服务器通知客户端保存键值对的一种技术。 客户端有了 Cookie 后，每次请求都发送给服务器。每个 Cookie 的大小不能超过 4kb。

##### cookie的创建

<img src="../../gitbook/markdownImages/image-20211121170251459.png" alt="image-20211121170251459" style="zoom: 50%;" />

##### **Cooke的获取**

<img src="../../gitbook/markdownImages/image-20211121173441802.png" alt="image-20211121173441802" style="zoom:50%;" />

### Cookie 的生命控制

Cookie 的生命控制指的是如何管理 Cookie 什么时候被销毁（删除） 

```
setMaxAge() 

正数，表示在指定的秒数后过期 

负数，表示浏览器一关，Cookie 就会被删除（默认值是-1） 

零，表示马上删除 Cookie
```

### Cookie 的 path 属性

Cookie 的 path 属性可以有效的过滤哪些 Cookie 可以发送给服务器。哪些不发。 

**path 属性是通过请求的地址来进行有效的过滤**

#### 实战

```Java
	
	   // 创建cookie
	   Cookie cookie1 = new Cookie("key1", "value1");
        // 设置 Cookie 一小时之后被删除。无效
        cookie1.setMaxAge(60 * 60);
        resp.addCookie(cookie1);
        
        // 获取cookie
        Cookie[] cookies = req.getCookies();
        for (Cookie cookie : cookies) {
            resp.getWriter().write("Cookie[" + cookie.getName() + "=" + cookie.getValue() + "] <br/>");
        }
        
        // 修改cookie
        cookie1.setValue("新设置的值");
        resp.addCookie(cookie1);
```



## Session （会话）

1、Session 是一个接口（HttpSession）。 

2、Session 就是会话。它是用来维护一个客户端和服务器之间关联的一种技术。 

3、每个客户端都有自己的一个 Session 会话。 

4、Session 会话中，我们经常用来保存用户登录之后的信息



### session的创建

- 创建和获取 Session。它们的 API 是一样的。
- request.getSession() 第一次调用是：创建 Session 会话 之后调用都是：获取前面创建好的 Session 会话对象。
-  isNew(); 判断到底是不是刚创建出来的（新的） 
  - true 表示刚创建 
  - false 表示获取之前创建 
- 每个会话都有一个身份证号。也就是 ID 值。而且这个 ID 是唯一的。 getId() 得到 Session 的会话 id 值。



### Session 生命周期控制

-  public void setMaxInactiveInterval(int interval) 设置 Session 的超时时间（以秒为单位），超过指定的时长，Session 就会被销毁。
  - 值为正数的时候，设定 Session 的超时时长。
  -  负数表示永不超时（极少使用） 
- public int getMaxInactiveInterval()获取 Session 的超时时间 
- public void invalidate() 让当前 Session 会话马上超时无效。
- **session的超时指的是，客户端两次请求的最大间隔时长。**



![image-20211121195733708](../../gitbook/markdownImages/image-20211121195733708.png)

### 实战

```Java
	   // 获取session
	   HttpSession session = req.getSession();
	   // 判断是否新建
        boolean isNew = session.isNew();
	   // 获取Session的ID
        String id = session.getId();

        resp.getWriter().write("session's id is " + id + "<br/>");
        resp.getWriter().write("is this session created? " + isNew + "<br/>");
        
         // Session域中添加数据
        req.getSession().setAttribute("key1", "value1");
        resp.getWriter().write("完成session保存数据");
        // session域中获取数据
        Object key1 = req.getSession().getAttribute("key1");
        resp.getWriter().write("session 中获得的key的数据为：" + key1);
```

