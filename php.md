## PHP中知识点集锦

### 1 $_GET、$_POST、$_REQUEST的区别

浏览器提交数据有两种方式：GET和POST。GET方式是将数据包含在URL中，POST是将数据包含在请求的消息体中。在服务器端可以通过以上的三个变量获取数据，$_GET变量接收GET方式的数据，$_POST变量接收POST方式的数据，而$_REQUEST支持两种方式发送过来的数据。