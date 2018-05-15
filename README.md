## Cookie,Session,token
### Cookie
#### http协议的无状态：标准的http协议是无状态的，无连接的。例如：一个Web应用由很多个Web页面组成，每个页面都有唯一的URL来定位。用户在浏览器的地址栏输入页面的URL，浏览器就会向Web Server去发送请求。比如，浏览器向Web服务器发送了两个请求，申请了两个页面。这两个页面的请求是分别使用了两个单独的HTTP连接。浏览器和Web服务器会在第一个请求完成以后关闭连接通道，在第二个请求的时候重新建立连接。Web服务器并不区分哪个请求来自哪个客户端，对所有的请求都一视同仁，都是单独的连接。因此服务器端没有办法知道两个请求是否来自于同一个浏览器。
#### Cookie：一个Cookie就是存储在用户主机浏览器中的一小段文本文件。Cookies是纯文本形式，一个Web页面或服务器告之浏览器来将这些信息存储并且基于一系列规则在之后的每个请求中都自动将该信息返回至服务器。
#### Cookie特点：
#### 1.0 Cookie是有大小限制的。每个Cookie所存放的数据不能超过4k，如果Cookie字符串长度超过4k，则该属性将返回空字符串。
#### 2.0 每个Cookie是存在有效期的。在默认情况下，一个Cookie的生命周期就是在关闭浏览器的时候结束。如果想要Cookie能在浏览器关掉之后还可以使用，就必须设置有效期，也就是失效日期。
#### 3.0 Cookie有域和路径这个概念。域就是domain的概念，因为浏览器是个注意安全的环境，所以不同的域之间是不能互相访问cookie的(当然可以通过特殊设置达到cookie跨域访问)。路径就是routing的概念，一个网页所创建的cookie只能被这个网页在同一目录或者子目录下的所有页面访问，而不能被其他目录下的网页访问。
#### 4.0 后台可以设置Cookie使其存储在相应头信息中（set-cookie）；可以通过httpOnly属性，设置是否允许前台通过JS获取Cookie值。前台可以通过JS来设置Cookie，设置的Cookie可通过请求头传送给后台。
#### 5.0 Cookie 的获取必须是在相同域名的子集页面下，不允许跨域获取Cookie值。
#### 6.0 Cookie 的安全：通常Cookie信息都是使用http连接传递数据，这种传递方式很容易被拦截被查看，所以Cookie存储的信息容易被窃取。加入Cookie中所传递的内容比较重要，那么就要求使用加密的数据传输。如果一个Cookie的属性为secure，那么它与服务器之间的就通过https或者其他安全协议传递数据。把Cookie设置为secure，只保证Cookie与服务器之间的数据传输过程加密，而保存在本地的Cookie文件并不加密。如想让本地Cookie加密，要自己加密。
 ```
 document.cookie = 'username=yulong;secure'
 ```
#### 7.0 Cookie 分类：会话Cookie（会话Cookie），永久Cookie。
#### 什么是临时Cookie：不设置过期时间，则表示这个Cookie生命周期为浏览器会话期间，只要关闭浏览器窗口，Cookie就消失了。这种生命周期为浏览会话期的Cookie被称为会话Cookie。会话Cookie一般不保存在硬盘上而是保存在内存里。
#### 永久Cookie：设置了过期时间，浏览器就会把Cookie保存到硬盘上，关闭后再次打开浏览器，这些Cookie依然有效直到超过设定的过期时间。存储在硬盘上的Cookie可以在不同的浏览器进程间共享，比如两个IE窗口。而对于保存在内存的Cookie，不同的浏览器有不同的处理方式。
#### 8.0 Cookie的使用：
#### 8.1 JS存取Cookie：
```
sdocument.cookie = 'username=yulong'
```

```
// 设置cookie
   function setCookie(name, value) {
   var Days = 30;
   var exp = new Date();
   exp.setTime(exp.getTime() + Days * 24 * 60 * 60 * 1000);
   document.cookie = name + "=" + escape(value) + ";expires=" + exp.toGMTString();
  }


// 读取Cookie
function getCookie(c_name) {
    if (document.cookie.length>0) {
     c_start=document.cookie.indexOf(c_name + "=")
    if (c_start!=-1) { 
     c_start=c_start + c_name.length+1 
     c_end=document.cookie.indexOf(";",c_start)
     if (c_end==-1) { c_end=document.cookie.length
     return unescape(document.cookie.substring(c_start,c_end))
    } 
}
   return ""
}
```

### Session
#### Session 是一种服务器端的存储方式，HTTP 是一种无状态的协议，为了分辨链接是谁发起的，就需要我们自己去解决这个问题。不然有些情况下即使是同一个网站我们每打开一个页面也都要登录一下。而Session和Cookie就是为解决这个问题而提出来的两个机制。Cookie通过在客户端记录信息确定用户身份，Session通过在服务器端记录信息确定用户身份。
#### 由于Cookie本地存储信息是在客户端，并且每次都会被浏览器自动发送给后台，因此使用Cookie存储数据不安全。所以重要的用户信息需要在服务器Session中进行存储。
#### 当第一次发送请求时，服务器需要为客户端的请求创建一个Session，生成一个SessionID，将这个SessionID返回给客户端。[ 服务器首先检查这个客户端的请求里是否已包含了一个Session标识（称为SessionId），如果已包含则说明以前已经为此客户端创建过Session，服务器就按照SessionId把这个Session检索出来使用（检索不到，会新建一个），如果客户端请求不包含SessionId，则为客户端创建一个Session并且生成一个与此Session相关联的SessionId，SessionId的值应该是一个既不会重复，又不容易被找到规律以仿造的字符串] 这个SessionId将被在本次响应中返回给客户端保存。
#### 服务器和浏览器之间仅需传递SessionId即可，服务器根据SessionId找到对应用户的Session对象，会话数据仅在一段时间内有效，这个时间就是server端设置的Session有效期。服务器端保存所有的用户的数据，所以服务器端的开销较大，而浏览器端保存则把不同用户需要的数据分别保存在用户各自的浏览器中，浏览器端一般只用来存储小数据，而非服务可以存储大数据或小数据服务器存储数据安全一些，浏览器只适合存储一般数据.

### token
#### Session的存储需要空间，而token服务端是可以不需要存储用户信息的。并且Session传递一般通过Cookie来传递。但token的传递方式也不局限与cookie传递。
#### 浏览器第一次访问服务器，根据传过来的唯一标识userId。服务器会通过一些算法，如：HMAC-SHA256算法，然后加上一个密匙，生成一个token，然后通过BASE64编码一下，将这个token发给客户端。客户端将其存储在浏览器中。下次请求时，会带这个这个token发给服务器。服务器接收到请求后，然后用相同的算法和密匙去校验token，如果通过执行业务，不通过返回错误信息。
#### Session：我发给你一张身份证，但只是一张写着身份证号码的纸片。你每次来办事，我去后台查一下你的 id 是不是有效。 
#### token：我发给你一张加密的身份证，以后你只要出示这张卡片，我就知道你一定是自己人。

### 浏览器存储
#### 浏览器存储：Storage的概念和Cookie相似，区别是它是为了更大容量存储设计的，Cookie的大小是受限的，并且每次请求一个新的页面的时候Cookie都会被发送过去，这样无形中浪费了带宽，另外Cookie不可跨域调用。 除此之外，Storage拥有setItem,getItem,removeItem,clear等方法，不像Cookie需要前端开发者自己封装setCookie，getCookie。 但是cookie也是不可或缺的，cookie的作用是与服务器进行交互，作为http规范的一部分而存在的，而web Storage仅仅是为了在本地“存储”数据而生 
#### sessionStorage、localStorage、cookie都是在浏览器端存储的数据，其中sessionStorage的概念很特别，引入了一个“浏览器窗口”的概念，sessionStorage是在同源的同窗口中，始终存在的数据，也就是说只要这个浏览器窗口没有关闭，即使刷新页面或进入同源另一个页面，数据仍然存在，关闭窗口后，sessionStorage就会被销毁，同时“独立”打开的不同窗口，即使是同一页面，sessionStorage对象也是不同的
#### 共同点：都是保存在浏览器端、且同源的不可跨域调用。
#### 区别： 1、cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递，而sessionStorage和localStorage不会自动把数据发送给服务器，仅在本地保存。cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下 2、存储大小限制也不同，cookie数据不能超过4K，同时因为每次http请求都会携带cookie、所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大 3、数据有效期不同，sessionStorage：仅在当前浏览器窗口关闭之前有效；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie：只在设置的cookie过期时间之前有效，即使窗口关闭或浏览器关闭 4、作用域不同，sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localstorage在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的 5、web Storage支持事件通知机制，可以将数据更新的通知发送给监听者 6、web Storage的api接口使用更方便

##### 参考文章：https://www.v2ex.com/t/80003
