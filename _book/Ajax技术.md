# Ajax技术

## Ajax简介

AJAX 全称为 Asynchronous JavaScript And XML，就是异步的 JS 和 XML。

通过 AJAX 可以在浏览器中向服务器发送异步请求，最大的优势：**无刷新获取数据。**

### AJAX 的优点
1) 可以无需刷新页面而与服务器端进行通信。
2) 允许你根据用户事件来更新部分页面内容。 
### AJAX 的缺点
1. 没有浏览历史，不能回退
2. 存在跨域问题(同源)
3. SEO 不友好

## Ajax的使用

## HTTP

### 请求报文

```
行      POST  /s?ie=utf-8  HTTP/1.1 
头      Host: atguigu.com
        Cookie: name=guigu
        Content-type: application/x-www-form-urlencoded
        User-Agent: chrome 83
空行
体      username=admin&password=admin
```

### 响应报文

```
行      HTTP/1.1  200  OK
头      Content-Type: text/html;charset=utf-8
        Content-length: 2048
        Content-encoding: gzip
空行    
体      <html>
            <head>
            </head>
            <body>
                <h1>尚硅谷</h1>
            </body>
        </html>
```

