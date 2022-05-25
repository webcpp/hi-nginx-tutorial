# 安装

## 一般说明

安装hi-nginx的方式，与编译安装nginx一般模块的方式无异。只是多个几个配置选项：

```
--add-module=module/ngx_http_autoblacklist_module   \
--with-http-hi-lua-version=lua5.3                   \
--add-module=module/ngx_http_lua_module             \
--with-http-hi-python-version=python-3.8-embed      \
--add-module=module/ngx_http_py_module              \
--add-module=module/ngx_http_java_module            \
--add-module=module/ngx_http_cpp_module    

```

每个模块都是独立的，可按需安装。

同时，对于lua和python来说，它们还可以选择支持的版本。对lua而言，可选lua，lua5.x或者luajit。对python而言，可选python2或者python3。

## 依赖

### c/c++基础环境

#### Ubuntu

```shell
apt-get install build-essential
apt-get install zlib1g-dev
apt-get install libssl-dev
```

#### centos

```shell
yum install gcc gcc-c++ make
yum install zlib-devel
yum install openssl-devel
```

### java 开发环境

从[http://jdk.java.net/](http://jdk.java.net/)下载需要的openjdk，至少java 8 版本，推荐 java 11+。

解压缩下载的安装包。将解压出来的文件目录整个移动至 `/usr/lib/jvm/` 目录下。

用 `ln -s` 创建一个符号链接，名为 `default-java` ，指向上一步jdk的安装目录。

创建 `/etc/ld.so.conf.d/jdk.conf` 文件，内容三行：

```txt
/usr/lib/jvm/default-java/lib/server
/usr/lib/jvm/default-java/lib
/usr/lib/jvm/default-java/jre/lib/amd64/server
```

运行 `sudo ldconfig` 命令，保证 `libjvm.so` 能被hi-nginx找到。

创建 `/etc/profile.d/jdk.sh` 文件，内容三行:

```txt
export JAVA_HOME=/usr/lib/jvm/default-java
export CLASSPATH=/usr/local/nginx/app/java:/usr/local/nginx/app/java/config-1.4.2.jar:/usr/local/nginx/app/java/jmustache-1.15.jar:/usr/local/nginx/app/java/hi-nginx-java.jar
export PATH=$JAVA_HOME/bin:$PATH
```

运行命令 `source /etc/profile` 后，一般可通过 `java -version` 命令验证jdk安装成功与否。 

### python 环境

#### ubuntu

```shell
apt-get install python3-dev

```

#### centos

```shell
yum install  python3-devel

```

确保 `pkg-config --cflags --libs $HTTP_HI_PYTHON_VERSION` 正确有效。

### lua 环境

#### ubuntu

```shell
apt-get install lua5.3
apt-get install luarocks
```

#### centos

```shell
yum install lua5.3
yum install luarocks
```

确保 `pkg-config --cflags --libs $HTTP_HI_LUA_VERSION` 正确有效。

## systemctl

安装后， `systemctl enable nginx` 和 `systemctl start nginx` 。

这是保证启动目录正确，从而配置时保证相对路径有效的最简单的方法。

## 关于第三方模块

hi-nginx自带几个第三方模块，在 `3rd` 目录中，需要的话，用 `--add-module=3rd/[module_name]` 添加到编译配置命令中即可。

如果需要更多第三方模块，可以自行下载需要的模块。其实，很多常用的第三方模块都可以在[这里](https://github.com/alibaba/tengine/tree/master/modules)和[这里](https://www.nginx.com/resources/wiki/modules/)找到。各取所需吧，建议统一放在 `3rd` 目录中——适合nginx的，也必定适合hi-nginx。
