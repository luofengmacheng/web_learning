## cookie和session

### 1 cookie

cookie是存放在`客户端`的用于保存用户信息的机制。当用户使用浏览器登录一个网站时，网站服务器会在响应包头中加入一些指令，让浏览器在客户端生成cookie。当用户再次访问这个网站时，浏览器会根据一定的规则将cookie发送给服务器。

### 2 session

session是存放在`服务端`的用于保存用户信息的机制。当用户使用浏览器登录一个网站时，网站服务器会为用户生成一些状态信息，例如，用户的登录信息。当用户下次登录时，可以从session中获取这些信息，进行验证。

但是，服务端可能会保存多个客户端的session，那么，当客户端发送请求时，如何知道该获取哪个session的信息呢。这里会有两种方式：

* cookie。每个session信息都会有一个session_id，将session_id保存在客户端的cookie，访问网站时，将session_id发送给服务端，服务端通过session_id获取信息。
* rewrite。利用重写，将session_id放到URL中。

### 3 cookie的域和路径

在浏览器的调试窗口的Resources -> Cookies中可以查看该网站的cookie，会发现cookie有以下字段：

* Name，键。
* Value，值。
* Domain，域。
* Path，路径。
* Expires，过期时间，如果没有，则表示当关闭网页时即过期。

其它的比较好理解，这里重点讲下域和路径：

可以理解为，将所有的cookie的信息放在一个数据库中，那么，当访问一个URL时，该发送哪些cookie呢？首先，会查找域为当前URL的域名或者父域名的cookie，然后，会在这些cookie中查找路径等于当前请求的URL的目录或者是父目录的cookie，于是，就会将这些cookie发送出去。

### 4 对比

* 存放位置：cookie存放在客户端；session存放在服务端。
* 实现机制：cookie通常可以单独实现；session通常需要借助cookie实现。