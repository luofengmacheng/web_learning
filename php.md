## PHP中知识点集锦

### 1 $_GET、$_POST、$_REQUEST的区别

浏览器提交数据有两种方式：GET和POST。GET方式是将数据包含在URL中，POST是将数据包含在请求的消息体中。在服务器端可以通过以上的三个变量获取数据，$_GET变量接收GET方式的数据，$_POST变量接收POST方式的数据，而$_REQUEST支持两种方式发送过来的数据。

### 2 关于CI中的若干问题

CI提供了大量的数据库操作，方便了开发人员操作数据库。

#### 2.1 连接数据库的db_debug选项

在进行操作数据库时，如果参数错误或者其它等错误，CI会返回一个页面，即使用try catch也无法捕捉该错误，但是，当系统上线后，并不想让数据库的错误信息返回给用户，而是希望自己处理，这时，就可以使用db_debug选项，该选项可以控制“是否显示数据库错误信息”，一般设置为TRUE，当系统上线后，可以设置为FALSE，禁止显示错误信息。

#### 2.2 数据库操作的where()

数据库在操作时会经常用到where()或者get_where()等函数，CI提供的示例如下所示：

``` PHP
$array = array('name' => $name, 'title' => $title, 'status' => $status);
$this->db->where($array);
```

给where()提供一个数组就可以生成连续的AND条件。

而当前台提交请求时，通常会采用JSON进行传输，因此，可以使用json_decode()将JSON条件转换成array()就可以直接交给where()处理，但是，如果接收到的不是有效的JSON时，json_decode()的返回值就是NULL。因此，在使用json_decode()进行转换时，注意检查转换的结果是否是NULL，从而可以过滤无效的JSON。

#### 2.3 CI的部署问题

使用CI开发网站后，需要将网站部署到服务器上，在进行部署时，需要注意以下问题：

* 修改base_url。将config/config.php中的base_url修改为网站的根目录，例如：$config['base_url'] = 'http://localhost/db'
* 数据库授权。当网站使用数据库时，需要对部署服务器进行授权
* 修改session目录。当session使用文件存储时，需要修改session的存储路径：$config['sess_save_path'] = '/usr/local/ci_sessions';而且，路径必须是绝对路径，而且程序可以对该目录有写权限

#### 2.4 CI中的MVC各自负责什么

MVC是CI的一大特点，那么，哪些内容该写到M(Model)里面呢？哪些内容又该写到V(View)里面呢？而哪些内容又该写到C(Controller)里面呢？

原则(1)：MVC只负责业务，只有跟业务相关的内容才写到MVC中。

原则(2)：Controller只负责请求的转发：通过input获取参数中的操作类型，然后根据操作类型进行操作的转发。将一个页面的操作封装到一个Controller中。将控制器中的函数分成三类：显示主界面的index()，显示每个窗口的函数，进行各种操作的转发函数。

原则(3)：Model是模型，但是实际编码时可以理解为模块。

原则(4)：基本的功能函数封装到library(面向对象)或者helper(面向过程)。

例如：

当要实现一个角色管理的功能时，创建一个该页面对应的控制器，控制器的index()负责显示角色管理的主界面。当点击角色管理页面上的某个按钮时，会调用一个js函数，而在这个js函数中会向该控制器发送请求，获取窗口的信息，然后显示这个窗口，而且控制器的每个函数都对应了主界面的一个窗口的内容。当该页面有响应时，就会调用控制器的一个方法，执行固定的操作。

### 3 关于curl

curl是一个常用的发起请求的工具，也有对应的libcurl供开发人员使用。

比较常用的是使用curl发起http请求，模拟客户端的请求，从而测试服务器是否能够正常提供服务，也可以测试CGI是否可以正常处理请求。

如下所示，curl请求百度主页的例子。

``` SHELL
curl http://www.baidu.com
```

但是，在测试过程中，经常需要给服务器传送参数，http中只有两种传参方式：POST和GET。

curl的-d选项可以用于发送数据，而且curl默认的传参方式是POST，也就是说，下面的方式采用POST方式传参：

``` SHELL
curl -d "name=luo&age=10" http://127.0.0.1/website/test_func
```

因此，test_func就可以直接处理$_POST中的数据。

如果要使用GET方式，需要加-G选项：

``` SHELL
curl -G -d "name=luo&age=10" http://127.0.0.1/website/test_func
```

这就采用GET方式传参，curl在发送请求时，会将参数添加到请求的URL后面，再发起http请求。

需要注意的是，参数类型不一定是上面的类型，例如，如果test_func()操作的是$_POST数组，就必须是以上的格式，如果test_func()操作的是其它类型(例如json)，就可以传送它可以处理的类型。

### 4 PHP中的对象与数组 VS javascript中的对象与数组(对象与数组都是针对json而言的)

#### 4.1 PHP中的对象与数组

PHP中array()既可以是键值对也可以是数组，或者说，数组也是一种特殊的键值对形式，但是键值对和数组转换成json却不是一样的。

``` php
$arr = array();
$obj = 5;
array_push($arr, $obj); // 等同于$arr[] = $obj;
$obj = 6;
array_push($arr, $obj);
echo json_encode($arr);
```

转换成的json就是：

```
[5, 6]
```

如果采用键值对形式，则有：

``` php
$arr = array('name' => 'luofeng', 'city' => 'shenzhen');
$arr['address'] = 'fun2';
echo json_encode($arr);
```

转换成的json就是：

```
{"name": "luofeng", "city": "shenzhen", "address": "fun2"}
```

#### 4.2 javascript中的对象与数组

javascript中的json形式有两种，一种是json对象，另一种是json字符串。

例如：

``` javascript
var json_obj = [ ];
json_obj.push({"name": "luofeng", "city": "shenzhen"});
```

那么，json_obj就是个json对象：

```
[{"name": "luofeng", "city": "shenzhen"}]
```

以上的方式是构造一个json对象，但是，许多函数在处理时，都是采用json字符串处理的，这时，就需要将json对象转换成json字符串。

``` javascript
// 将json_obj对象转换成json字符串
var json_str = JSON.stringify(json_obj);
```