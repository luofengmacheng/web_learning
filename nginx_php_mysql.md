## nginx、php、mysql部署指南

### 1 nginx(1.10.3)

直接从官网上下载安装包，解压，然后执行:

```
./configure
make
make install
```

编译过程中依赖一些环境，例如gcc等，缺少什么再使用yum或者apt-get安装即可。

### 2 php(5.3.29)

开始下载5.3.8版本的，结果在编译时失败，需要从网上下载补丁，然后才能顺利编译，注意编译过程中需要启用fpm才行，下面是一些常用的选项:

```
./configure --prefix=/usr/local/php53 \
--enable-fpm \
--with-fpm-user=nginx \
--with-fpm-group=nginx \
--with-libxml-dir \
--with-gd \
--with-jpeg-dir \
--with-png-dir \
--with-zlib-dir \
--enable-gd-native-ttf \
--enable-gd-jis-conv \
--enable-mysqlnd \
--with-mysql=mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--enable-sockets \
--enable-soap \
--with-curl \
--with-mcrypt
```

因此，如果是大版本，建议下载较新的版本。

### 3 mysql(5.7.17)

* 从官网下载二进制的包，然后解压到安装路径。
* 创建数据仓库目录，例如/data/mysql
* 创建mysql用户：

```
groupadd mysql
useradd mysql -r -s /sbin/nologin -g mysql -d /usr/local/mysql
```

* 将安装路径和数据仓库路径的属主修改为mysql
* 初始化数据库

```
./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql
```

注意：`此处需要记录生成的默认的root用户密码`

* 修改配置文件

```
cd support-files
cp my-default.cnf /etc/my.cnf
cp mysql.server /etc/init.d/mysql
```

然后分别配置这两个文件中的值，主要是basedir、datadir、sock、port等。

* 启动mysql

```
./bin/mysqld_safe --user=mysql &
```

* 修改默认密码

```
./bin/mysql --user=root -p
set password=password('123456');
grant all privileges on *.* to root@'%' identified by '123456';
flush privileges;
```

* 设置PATH：修改/etc/profile，将mysql的安装路径的bin目录添加到PATH