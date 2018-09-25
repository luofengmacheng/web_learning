## Django环境搭建

### 1 Python环境安装

一般Linux操作系统上模版都安装了python2，最新的django2只支持python3，而且python3是未来的趋势，因此，这里重新安装了python3。

从python网管下载python3.7，但是在安装之前需要安装libffi-devel(python3.7以下是不需要的)：

```
yum install libffi-devel
```

然后直接解压，编译安装，安装python3时默认不会覆盖系统的python2，如果确实要覆盖，可以创建软链接。

安装python3的同时会安装setuptools和pip3。

### 2 安装及使用virtualenv

为了不影响系统已经安装的包，使用python时通常会使用到虚拟环境。

```
pip3 install virtualenv
```

然后就可以创建和删除虚拟环境：

```
virtualenv -p /usr/local/bin/python3.7 django_web

rm -rf django_web
```

python3已经自带了虚拟环境：

```
python3 -m venv django_web
```

虚拟环境中只包含基本的setuptools和pip，不包含系统安装的第三方包。

有了虚拟环境，就可以开始使用啦！

```
. django_web/bin/activate

deactivate
```

### 3 安装Django

```
pip install django

python3 -m django --version
```