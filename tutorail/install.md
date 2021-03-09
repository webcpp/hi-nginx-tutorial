# 安装

## 一般说明
安装hi-nginx的方式，就是编译安装nginx的方式。只是多个几个配置选项：

```
                --enable-http-hi-cpp=YES                            \
                --enable-http-hi-python=YES                         \
                --enable-http-hi-lua=YES                            \
                --enable-http-hi-qjs=YES                            \
                --enable-http-hi-java=YES                           \
                --with-http-hi-python-version=python3               \
                --with-http-hi-lua-version=lua5.3                   \
                --add-module=module/ngx_http_hi_module              \
                --add-module=module/ngx_autoblacklist_module

```
除c++外，均为可选的。所有语言支持均由模块ngx_http_hi_module提供。因此，必须通过--add-module添加该模块。

不需要哪种语言，就把相关enable项设置为NO。

同时，对于lua和python来说，它们还可以选择支持的版本。对lua而言，可选lua，lua5.x或者luajit。对python而言，可选python2或者python3。

## 依赖
- linux，hi-nginx暂不支持windows系统
- gcc 和 g++(c++11) 或者 clang 和 clang++
- python-devel,如果 `--enable-http-hi-python=YES` 并且 `with-http-hi-python-version=python2`
- python3.x-devel 如果 `--enable-http-hi-python=YES` 并且 `with-http-hi-python-version=python3`
- lua-devel(lua5.1,5.2,5.3),如果 `--enable-http-hi-lua=YES`  并且 `--with-http-hi-lua-version=lua5.x`
- luajit-devel,如果 `--enable-http-hi-lua=YES` 并且 `--with-http-hi-lua-version=luajit`
- jdk8+,如果 `--enable-http-hi-java=YES`

## systemctl

安装后，`systemctl enable nginx`和`systemctl start nginx`。

这是保证启动目录正确，从而配置时保证相对路径有效的最简单的方法。




## 关于java支持
java版本不低于 8。

最简单方法是直接安装openjdk，如果不怕麻烦，用oracle的也可以的。从[http://jdk.java.net/](http://jdk.java.net/)下载openjdk安装包，解压，安装至`/usr/lib/jvm`目录下；同时创建一个`default-java`得符号链接，指向openjdk安装目录即可。关键是设置$JAVA_HOME和java搜索路径，否则编译hi-nginx时无法找到相关java库和头文件，运行时会无法加载libjvm.so。推荐的方案：

```txt
#cat /etc/ld.so.conf.d/jdk.conf 
/usr/lib/jvm/default-java/lib/server
/usr/lib/jvm/default-java/lib
/usr/lib/jvm/default-java/jre/lib/amd64/server


#cat /etc/profile.d/jdk.sh
export JAVA_HOME=/usr/lib/jvm/default-java
export CLASSPATH=/usr/local/nginx/java:/usr/local/nginx/java/hi-nginx-java.jar
export PATH=$JAVA_HOME/bin:$PATH

```
根据自己的jdk版本和安装目录，修改其中的相关项即可。注意其中`/usr/lib/jvm/default-java/jre/lib/amd64/server`项就是特别针对java8的安排。

记得`sudo ldconfig`。


## 关于quickjs支持
原来v2.0.3之前有个基于duktape引擎的方案。自v2.0.3开始，替换为现在的方案。
该方案基于开源javascript引擎quickjs。如果熟悉该引擎的api，开发C/C++扩展是很容易。具体可参考quickjs官网介绍。

除了quickjs官网内置的模块`os`,`std`,`bjson`，我附加了三个功能模块：`hi`,`crypto`和`hash`。具体用法请参考安装文件包里的`qjs`目录。

## 关于第三方模块
hi-nginx自带几个第三方模块，在`3rd`目录中，需要的话，用`--add-module=3rd/[module_name]`添加到编译配置命令中即可。

如果需要更多第三方模块，可以自行下载需要的模块。其实，很多常用的第三方模块都可以在[这里](https://github.com/alibaba/tengine/tree/master/modules)和[这里](https://www.nginx.com/resources/wiki/modules/)找到。各取所需吧，建议统一放在`3rd`目录中——凡nginx能接受的，hi-nginx必能接受。