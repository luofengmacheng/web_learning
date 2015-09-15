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

将CI部署到apache服务器上时，需要注意以下问题：

* 注意修改config.php中的base_url
* 当网站需要连接数据库时，注意修改database.php中的配置信息
* 进行部署时，可能会遇到这个问题：

```
Session: Configured save path '' is not a directory, doesn't exist or cannot be created.
```

解决方案是：为config.php中的$config['sess_save_path']设置路径，而且是绝对路径，例如$config['sess_save_path'] = '/usr/local/ci_sessions'，然后创建该目录，并修改权限为可写。



