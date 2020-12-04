# 安装

## 一般说明
安装hi-nginx的方式，就是编译安装nginx的方式。只是多个几个配置选项：

```
                --enable-http-hi-cpp=YES                            \
                --enable-http-hi-python=YES                         \
                --enable-http-hi-lua=YES                            \
                --enable-http-hi-qjs=YES                            \
                --enable-http-hi-java=YES                           \
                --enable-http-hi-php=YES                            \
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
- jdk11+,如果 `--enable-http-hi-java=YES`
- PHP 7.0,7.1,7.2(--enable-embed=shared),如果 `--enable-http-hi-php=YES`

## systemctl

安装后，`systemctl enable nginx`和`systemctl start nginx`。

这是保证启动目录正确，从而配置时保证相对路径有效的最简单的方法。


## 关于php7支持
要安装php7支持，需要编译安装php7时添加--enable-embed=shared配置选项。这是必须的。

另外，如果是7.0和7.1，似乎需要把php7源码包中configure脚本需要略略的修改下。找到该脚本中的以下段落：
```
ac_fn_c_check_decl "$LINENO" "isfinite" "ac_cv_have_decl_isfinite" "#include <math.h>
"
if test "x$ac_cv_have_decl_isfinite" = xyes; then :
  ac_have_decl=1
else
  ac_have_decl=0
fi

```

把第一等于1的等式改为等于0即可。

7.2不需要上述修改。

编译安装完成php7后，配置下php.ini文件，假设hi-nginx将安装在/usr/local
```

include_path = ".:/usr/local/nginx/php"

```
如果hi-nginx将安装在其他地方，需要相应地修改下上面的值。

如果有必要，还需把libphp7.so所在的路径添加到`/etc/ld.so.conf.d/php.conf`中。

## 关于java支持
虽然hi-nginx可以在java8环境下工作，但为了配合hi-nginx-java的升级，强烈建议java版本不低于 11。

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

## 关于javascript支持

hi-nginx对javascript的支持方案有两种。

第一种基于java，第二种基于quickjs。前者比较吃内存，但能方便嵌入java库类。
后者很轻，采用c/c++语言实现，故而对熟悉c/c++的开发者更有利。

### 基于Java的方案

hi-nginx对javascript的支持是通过javax.script.ScriptEngine实现的。这种实现有两种方式，第一种是在java层，第二种是走jni。两种都是可行的。在hi-nginx-demo项目中，我给出了一个基于java层的简单实现。在hi-nginx项目中采用的基于jni实现。这种实现的好处是能够充分利用c/c++的优势降低JVM对内存的消耗，而且可以获得更高的性能表现。

更重要的是，因为可以利用javax.script.ScriptEngine接口，所以任何实现JSR-223的基于JVM的脚本语言，都是可以直接支持的，例如groovy和kotlin——当然需要安装相关语言并正确设置配置。比如gooovy：

```nginx

        hi_java_classpath "-Djava.class.path=.:/usr/local/nginx/java:/usr/local/nginx/java/hi-nginx-java.jar:/usr/local/groovy-3.0.6/lib:/usr/local/groovy-3.0.6/lib/groovy-3.6.0.jar:/usr/local/groovy-3.0.6/lib/groovy-jsr223-3.0.6.jar";

        location / {
                hi_need_cache off;
                hi_cache_expires 5s;
                hi_need_kvdb on;
                hi_kvdb_size 10;
                hi_kvdb_expires 5m;
                hi_need_cookies on;
                hi_need_headers on;
                hi_need_session on;
                hi_session_expires 300s;
                hi_javascript_compiledscript_expires 5m;
                hi_javascript_lang groovy;
                hi_javascript_extension groovy;
                hi_javascript_script groovy/index.groovy;
        }

```

有一点必须指出：如果你需要运行多种jsr-223的JVM语言，比如javascript和groovy，那么由于JVM是进程级的，每个进程只有一个JVM，且只会初始化一次，所以为了确保多种JVM语言能够同时运行，需要确保JVM初始化时的`hi_java_classpath`指令参数能够同时为多种语言所用。比如，单单运行javascript，不需要跟groovy有关的配置，但是若同时需要运行groovy,则需要添加相关配置。

由于java本身对javascript的良好支持，使得在hi-nginx中，可以直接在javascript脚本中使用java——相当于把java嵌入了javascript。注意：java14是最后一个支持javasrcipt引擎得java版本，故而欲同时使用java和js，java版本不能超过java14。当然，最好的选择似乎是groovy。java8以上均能获得很好得支持。

### 基于quickjs的方案
原来v2.0.3之前有个基于duktape引擎的方案。自v2.0.3开始，替换为现在的方案。
该方案基于开源javascript引擎quickjs。如果熟悉该引擎的api，开发C/C++扩展是很容易。具体可参考quickjs官网介绍。

除了quickjs官网内置的模块`os`,`std`,`bjson`，我附加了三个功能模块：`hi`,`crypto`和`hash`。具体用法请参考安装文件包里的`qjs`目录。

## 关于第三方模块
hi-nginx自带两个第三方模块，在`3rd`目录中，需要的话，用`--add-module=3rd/[module_name]`添加到编译配置命令中即可。

如果需要更多第三方模块，可以自行下载需要的模块。其实，很多常用的第三方模块都可以在[这里](https://github.com/alibaba/tengine/tree/master/modules)和[这里](https://www.nginx.com/resources/wiki/modules/)找到。各取所需吧，建议统一放在`3rd`目录中——凡nginx能接受的，hi-nginx必能接受。