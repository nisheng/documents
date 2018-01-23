## 由img标签上报数据延伸的Ajax跨域问题

### 如何使用img标签上报数据

```
var REPORT_URL = '//gamepre.ltyun.cc/api/public/page-data/save?'	//数据上报接口
var img = new Image;						//创建img标签
img.onload = img.onerror = function(){		//img加载完成或加载src失败时的处理
    img = null;								//img置空，不会循环触发onload/onerror
};
img.src = REPORT_URL + Build._format(params); //数据上报接口地址拼接上报参数作为img的src
```

##### img标签之onError事件与问题

有时，img标签中的src图片加载失败，原来的图片位置会出现一个碎片图标，用户体验会下降。

要想去除载入图片失败时显示在左上角的碎片图标，就要借用img标签的onerror事件和javascript，img标签支持onerror 事件，在装载文档或图像的过程中如果发生了错误，就会触发onerror事件。可以使用一张提示错误的图片代替载入失败的图片。代码如下：

```
<img src="images/bg.png" onerror="javascript:this.src='images/bgError.png';">
```

当src默认的src不存在时，就会触发onerror事件，在该事件中指定img.src。

但问题来了，如果 bgError.png也不存在，则继续触发 onerror事件，导致循环，故出现错误。

**解决方法：**

第一种：去掉 onerror 代码；或者更改 onerror 代码为其它；或者确保 onerror 中的图片足够小，并且存在.

第二种：img=img.onLoad=img.onError=null



### 使用img标签上报数据

很多地方不使用Ajax发送请求上报数据而使用img标签，主要有以下两个原因：

1. 不存在Ajax跨域的问题，可做不同源的请求；
2. 很古老的标签。没有浏览器的兼容问题，及时老浏览器不支持部分js也支持img。

因为img不存在跨域的问题，所以很多网站也做了图片的防盗链处理，比如百度、知乎是有处理的，博客园应该是没有相应处理的。



### 跨域

跨域，指的是浏览器对于javascript的同源策略的限制，是**浏览器施加的**安全限制。**所谓同源是指协议，域名，端口均相同**。

http://www.123.com/index.html 调用 http://www.123.com/server.php （非跨域）

http://www.123.com/index.html 调用 https://www.123.com/server.php （协议不同，跨域）

http://www.123.com/index.html 调用 http://www.456.com/server.php（ 主域名不同，跨域）

http://abc.123.com/index.html 调用 http://def.123.com/server.php （子域名不同，跨域）

http://localhost:8080/index.html 调用 http://localhost:8081/server.php （端口不同，跨域）

http://localhost:8080/index.html 调用 http://127.0.0.1:8081/server.php （域名不同，跨域）

**请注意：localhost和127.0.0.1虽然都指向本机，但也属于跨域。**



### 可以跨域的标签及使用场景

允许跨域加载资源的有三个标签。分别是：

1. **<img src=xxx>**  用于打点统计，统计网站可能是其他域
2. **<link href=xxx>** 可以使用CDN的资源
3. **<script src=xxx>** 可以使用CDN资源、用于JSONP



### Ajax跨域解决方案

浏览器同源策略有两种限制，其限制之一是不能通过ajax的方法去请求不同源中的文档。 第二个限制是浏览器中不同域的框架之间不能进行js的交互操作的，我们主要解决第一种限制Ajax跨域。

#### 1. JSONP  只支持get请求

实现如下：

```
<script type="text/javascript">
    var messagetow = function(data){
        //data 通过跨域得到的信息
        console.log(data);
    };
    var url = "gamepre.ltyun.cc/api/public/page-data/save?callback=messagetow";
    var script = document.createElement('script');
    script.setAttribute('src', url);
    document.getElementsByTagName('head')[0].appendChild(script);
</script>
```

#### 2. CROS跨域资源共享

它允许浏览器向跨源服务器，发出`XMLHttpRequest`请求，从而克服了AJAX只能同源使用的限制。CORS需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。

整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于前端开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。

因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。



##### -CORS请求类型-**

跨域请求有两种形式：

1、  简单请求         2、  非简单请求

简单请求满足以下条件：

HTTP请求方法（区分大小写）为以下之一：

**·**  HEAD          **·**  GET          **·**POST

HTTP头部匹配（不区分大写小）为以下：

**·**  Accept

**·**  Accept-Language

**·**  Content-Language

**·**  Last-Event-ID

**·**  Content-Type:  只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain



##### 简单请求

对于简单请求，浏览器直接发出CORS请求。具体来说，就是自动在头信息之中，增加一个**Origin**字段。

```
GET /cors HTTP/1.1
Origin: http://api.bob.com
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```

上面的头信息中，**Origin**字段用来说明，本次请求来自哪个源（协议 + 域名 + 端口）。服务器根据这个值，决定是否同意这次请求。

如果`Origin`指定的源，不在许可范围内，服务器会返回一个正常的HTTP回应。浏览器发现，这个回应的头信息没有包含`Access-Control-Allow-Origin`字段，就知道出错了，从而抛出一个错误，被`XMLHttpRequest`的`onerror`回调函数捕获。注意，这种错误无法通过状态码识别，因为HTTP回应的状态码有可能是200。

如果`Origin`指定的域名在许可范围内，服务器返回的响应，会多出几个头信息字段。

```
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true		//布尔值，是否允许发送cookie,默认cros中不包含
Access-Control-Expose-Headers: FooBar		//header除默认可获取字段
Content-Type: text/html; charset=utf-8
```

*withCredentials 属性*

CORS请求默认不发送Cookie和HTTP认证信息。如果要把Cookie发到服务器，一方面要服务器同意，指定**Access-Control-Allow-Credentials:true**。另一方面，开发者必须在AJAX请求中打开**withCredentials**属性。

```
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```



##### 非简单请求

我们想使用PUT或者DELETE请求，又或者我们想使用Content-Type:application/json来支持JSON。那么，我们就需要**非简单请求**。

我们在使用非简单请求时，表面上看起来客户端只发送了一个请求，但实际上，要完成一次非简单请求，客户端在私底下是要向服务器发起两次请求的。第一次请求（**"预检"请求**），是向服务器确认权限，一旦被授权，则发起第二次请求（真正意义上的数据请求）。且，第一次请求也可以被缓存，所以不是每次我们发起非简单请求，都会预请求一次。

**预检**请求的HTTP头信息：

```
OPTIONS /cors HTTP/1.1			//请求方法是OPTIONS,表示这个请求是用来询问的
Origin: http://api.bob.com
Access-Control-Request-Method: PUT	//CORS请求会用到哪些HTTP方法
Access-Control-Request-Headers: X-Custom-Header	//逗号分隔的字符串，指定浏览器CORS请求会额外发送的头信息字段
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive	//告诉WEB服务器或者代理服务器，在完成本次请求的响应后，保持连接，等待本次连接的后续请求
User-Agent: Mozilla/5.0...
```

服务器收到"预检"请求以后，检查了`Origin`、`Access-Control-Request-Method`和`Access-Control-Request-Headers`字段以后，确认允许跨源请求，就可以做出回应。

```
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://api.bob.com		//该字段设为星号时，表示同意任意跨源请求。
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
Content-Type: text/html; charset=utf-8
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```

如果浏览器否定了"预检"请求，会返回一个正常的HTTP回应，但是没有任何CORS相关的头信息字段。这时，浏览器就会认定，服务器不同意预检请求，因此触发一个错误，被`XMLHttpRequest`对象的`onerror`回调函数捕获。控制台会打印出如下的报错信息。

```
XMLHttpRequest cannot load http://api.alice.com.
Origin http://api.bob.com is not allowed by Access-Control-Allow-Origin.
```

一旦预检查得到授权信息，那么浏览器就会发送真正的跨域请求了。且，请求和服务器响应与简单CORS请求一样。



参考[跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)，更多详情点击查看