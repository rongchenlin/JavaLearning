# Ajax技术
- [Ajax技术](#ajax技术)
  - [1. 原生Ajax简介](#1-原生ajax简介)
    - [1.1 Ajax简介](#11-ajax简介)
    - [1.2 Ajax特点](#12-ajax特点)
      - [AJAX 的优点](#ajax-的优点)
      - [AJAX 的缺点](#ajax-的缺点)
    - [1.3 HTTP报文格式](#13-http报文格式)
      - [1.3.1 请求报文](#131-请求报文)
      - [1.3.2 响应报文](#132-响应报文)
    - [1.4 Ajax使用](#14-ajax使用)
      - [使用步骤](#使用步骤)
    - [1.5 IE缓存](#15-ie缓存)
    - [1.6 取消请求](#16-取消请求)
    - [1.7 取消重复请求](#17-取消重复请求)
    - [1.8 超时和网络异常](#18-超时和网络异常)
  - [2. Jquery中使用Ajax](#2-jquery中使用ajax)
    - [get方法发送](#get方法发送)
    - [post方法发送](#post方法发送)
    - [通用Ajax方法发送](#通用ajax方法发送)
  - [3. axios中使用Ajax](#3-axios中使用ajax)
    - [get方法](#get方法)
    - [post方法](#post方法)
    - [通用方法](#通用方法)
  - [4. Ajax解决跨域问题](#4-ajax解决跨域问题)
    - [同源策略](#同源策略)
    - [JSONP——解决跨越问题](#jsonp解决跨越问题)
    - [JSONP使用方法](#jsonp使用方法)
    - [CORS——解决跨越问题](#cors解决跨越问题)


说明：通过学习[【尚硅谷】3小时Ajax入门到精通](https://www.bilibili.com/video/BV1WC4y1b78y?p=1)视频，进行的总结。

## 1. 原生Ajax简介

### 1.1 Ajax简介

AJAX 全称为 Asynchronous JavaScript And XML， 就是**异步的 JS 和 XML**。

通过 AJAX 可以在浏览器中向服务器发送异步请求，最大的优势：**无刷新获取数据。**

### 1.2 Ajax特点

#### AJAX 的优点

1) 可以无需刷新页面而与服务器端进行通信。
2) 允许你根据用户事件来更新部分页面内容。 
#### AJAX 的缺点
1. 没有浏览历史，不能回退
2. 存在跨域问题(同源)
3. SEO 不友好

### 1.3 HTTP报文格式

#### 1.3.1 请求报文

```
行：      POST  /s?ie=utf-8  HTTP/1.1 
头：      Host: atguigu.com
        Cookie: name=guigu
        Content-type: application/x-www-form-urlencoded
        User-Agent: chrome 83
空行：
体：      username=admin&password=admin
```

#### 1.3.2 响应报文

```
行：      HTTP/1.1  200  OK
头：      Content-Type: text/html;charset=utf-8
        Content-length: 2048
        Content-encoding: gzip
空行：    
体：      <html>
            <head>
            </head>
            <body>
                <h1>测试</h1>
            </body>
        </html>
```

### 1.4 Ajax使用

**XMLHttpRequest**，AJAX 的所有操作都是通过该对象进行的。

#### 使用步骤

```javascript
<script>
        // 绑定事件
        btn.onclick = function(){
            //1. 创建对象
            const xhr = new XMLHttpRequest();
            //2. 初始化 设置请求方法和 url
            xhr.open('GET', 'http://127.0.0.1:8000/server?a=100&b=200&c=300');
            //3. 发送
            xhr.send();
            //4. 事件绑定 处理服务端返回的结果
            // on  when 当....时候
            // readystate 是 xhr 对象中的属性, 表示状态 0 1 2 3 4
            // change  改变
            xhr.onreadystatechange = function(){
                // 判断 (服务端返回了所有的结果)
                if(xhr.readyState === 4){
                    //判断响应状态码 200  404  403 401 500
                    // 2xx 成功:200-300之间表示返回成功
                    if(xhr.status >= 200 && xhr.status < 300){
                        //处理结果  行 头 空行 体
                        //响应 
                        // console.log(xhr.status);//状态码
                        // console.log(xhr.statusText);//状态字符串
                        // console.log(xhr.getAllResponseHeaders());//所有响应头
                        // console.log(xhr.response);//响应体
                        //设置 result 的文本
                        result.innerHTML = xhr.response;
                    }else{

                    }
                }
            }
        }
    </script>
```

### 1.5 IE缓存

IE浏览器，由于缓存机制的存在，**ajax 只会发送的第一次请求，剩余多次请求不会在发送给浏览器而**

**是直接加载缓存中的数据。**

解决方式：修改 url 地址，即可避免缓存问题

```
xhr.open("get","/testAJAX?t="+Date.now());     // 加上Date.now() 就可以解决
```

### 1.6 取消请求

**当浏览器向服务器发送请求过程中，用户想要取消请求，可以在服务器返回之前，使用abort进行取消请求。**

````javascript
	<button>点击发送</button>
    <button>点击取消</button>
    <script>
        //获取元素对象
        const btns = document.querySelectorAll('button');
        let x = null;  // 设置成全局变量
        btns[0].onclick = function(){
            x = new XMLHttpRequest();
            x.open("GET",'http://127.0.0.1:8000/delay');
            x.send();
        }
        // 取消发送：abort
        btns[1].onclick = function(){
            x.abort();
        }
    </script>	
````

### 1.7 取消重复请求

假设用户一直点击一个发送请求的按钮，但是**每次的请求内容又都是相同的**，此时**浏览器就会向服务器发送很多次请求**，这个时候网站的性能就变差了，因此我们需要**将重复发送的请求取消掉，只进行一次有效的请求。**

````javascript
<button>点击发送</button>
    <script>
        //获取元素对象
        const btns = document.querySelectorAll('button');
        let x = null;
        //标识变量
        let isSending = false; // true：正在发送，false：还未发送
        btns[0].onclick = function(){
            //判断标识变量
            if(isSending) 
                x.abort();// 如果正在发送, 则取消该请求, 创建一个新的请求
            // 创建一个新的请求
            x = new XMLHttpRequest();
            //修改 标识变量的值
            isSending = true;
            x.open("GET",'http://127.0.0.1:8000/delay');
            x.send();
            x.onreadystatechange = function(){
                if(x.readyState === 4){
                    //修改标识变量，表示发送过了
                    isSending = false;
                }
            }
        }
    </script>
````

### 1.8 超时和网络异常

````javascript
	<button>点击发送请求</button>
    <div id="result"></div>
    <script>
        const btn = document.getElementsByTagName('button')[0];
        const result = document.querySelector('#result');
        btn.addEventListener('click', function(){
            const xhr = new XMLHttpRequest();
            //超时设置 2s 设置
            xhr.timeout = 2000;
            //超时回调
            xhr.ontimeout = function(){
                alert("网络异常, 请稍后重试!!");
            }
            //网络异常回调
            xhr.onerror = function(){
                alert("你的网络似乎出了一些问题!");
            }
            xhr.open("GET",'http://127.0.0.1:8000/delay');
            xhr.send();
            xhr.onreadystatechange = function(){
                if(xhr.readyState === 4){
                    if(xhr.status >= 200 && xhr.status< 300){
                        result.innerHTML = xhr.response;
                    }
                }
            }
        })
    </script>
````

## 2. Jquery中使用Ajax

### get方法发送

```javascript
	<div class="container">
        <button class="btn btn-primary">GET方法发送</button>
    </div>
    <script>
        $('button').eq(0).click(function(){
            $.get('http://127.0.0.1:8000/jquery-server', 
                  {a:100, b:200},    // 设置参数
                  function(data){
                		console.log(data);
           		   },
                  'json');          // 返回json格式
        });
    </script>
```

### post方法发送

````javascript
	<div class="container">
        <button class="btn btn-danger">POST方法发送</button>
    </div>
    <script>
        $('button').eq(1).click(function(){
            $.post('http://127.0.0.1:8000/jquery-server', 
                   {a:100, b:200}, 
                   function(data){
                console.log(data);
            });
        });
    </script>
````

### 通用Ajax方法发送

`````javascript
	<div class="container">
        <button class="btn btn-info">通用型方法ajax</button>
    </div>
    <script>
        $('button').eq(2).click(function(){
            $.ajax({
                //url
                url: 'http://127.0.0.1:8000/jquery-server',
                //参数
                data: {a:100, b:200},
                //请求类型
                type: 'GET',
                //响应体结果
                dataType: 'json',
                //成功的回调
                success: function(data){
                    console.log(data);
                },
                //超时时间
                timeout: 2000,
                //失败的回调
                error: function(){
                    console.log('出错啦!!');
                },
                //头信息
                headers: {
                    c:300,
                    d:400
                }
            });
        });
    </script>
`````

## 3. axios中使用Ajax

```JavaScript
    //配置 baseURL，后面就不需要再出现url
    axios.defaults.baseURL = 'http://127.0.0.1:8000';
```

### get方法

```javascript
	btns[0].onclick = function () {
            //GET 请求
            axios.get('/axios-server', {
                //url 参数
                params: {
                    id: 100,
                    vip: 7
                },
                //请求头信息
                headers: {
                    name: 'atguigu',
                    age: 20
                }
            }).then(value => {
                console.log(value);
            });
        }
```

### post方法

```javascript
	btns[1].onclick = function () {
            axios.post('/axios-server', {
                username: 'admin',
                password: 'admin'
            }, {
                //url 
                params: {
                    id: 200,
                    vip: 9
                },
                //请求头参数
                headers: {
                    height: 180,
                    weight: 180,
                }
            });
        }
```

### 通用方法

`````javascript
	btns[2].onclick = function(){
            axios({
                //请求方法
                method : 'POST',
                //url
                url: '/axios-server',
                //url参数
                params: {
                    vip:10,
                    level:30
                },
                //头信息
                headers: {
                    a:100,
                    b:200
                },
                //请求体参数
                data: {
                    username: 'admin',
                    password: 'admin'
                }
            }).then(response=>{
                //响应状态码
                console.log(response.status);
                //响应状态字符串
                console.log(response.statusText);
                //响应头信息
                console.log(response.headers);
                //响应体
                console.log(response.data);
            })
        }
`````

说明：更多关于axios的使用，可以参考[**官方文档**](http://www.axios-js.com/zh-cn/docs/)。

## 4. Ajax解决跨域问题

### 同源策略

同源策略是浏览器的一种安全策略，**也就是浏览器向服务器发送请求时的前提是浏览器和服务器具有相同的来源（同源），也就是浏览器和服务器的协议、域名、端口号相同，这也是使用Ajax需要的件。**

同源： 协议、域名、端口号 必须完全相同。

**跨域：违背同源策略。**

### JSONP——解决跨越问题

**JSONP 就是利用 script 标签的跨域能力来发送请求的。**

### JSONP使用方法

**前端**

`````javascript
1.动态的创建一个 script 标签
	var script = document.createElement("script");
2.设置 script 的 src，设置回调函数
	script.src = "http://localhost:3000/testAJAX?callback=abc";
	function abc(data) {
	alert(data.name);
	};
3.将 script 添加到 body 中
	document.body.appendChild(script);
`````

**后端**

````javascript
	const data = {
        name:'尚硅谷',
        city: ['北京','上海','深圳']
    };
    //将数据转化为字符串
    let str = JSON.stringify(data);
    //接收 callback 参数
    let cb = request.query.callback;
    //返回结果
    response.end(`${cb}(${str})`);
````

### CORS——解决跨越问题

CORS（Cross-Origin Resource Sharing），跨域资源共享。CORS 是官方的跨域解决方案，它的特点是不需要在客户端做任何特殊的操作，完全在服务器中进行处理，支持get 和 post 请求。

**具体使用方法：在服务器端进行设置（设置响应头）**

`````javascript
    app.all('/cors-server', (request, response)=>{
    //设置响应头
    response.setHeader("Access-Control-Allow-Origin", "*");
    response.setHeader("Access-Control-Allow-Headers", '*');
    response.setHeader("Access-Control-Allow-Method", '*');
    // response.setHeader("Access-Control-Allow-Origin", "http://127.0.0.1:5500");
    response.send('hello CORS');
});
`````

