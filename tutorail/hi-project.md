# hi-project

hi-project是一个创建hi-nginx模板化工程的脚本。`--help`可查看使用方法:

```shell

$1:project name.
	default:demo
$2:type:cpp,python,lua ,php , java, javascript
	default:cpp
$3:hi(nginx) install directory.
	default:/usr/local/nginx


```

它接受三个参数来创建工程，使用`make`,`make clean`和`sudo make install`来完成工程构建、清理和安装。必要时，可以通过修改Makefile来添加自定义行为。

举个例子。我想创建一个hi-nginx-cpp工程。

- 第一步,`mkdir test && cd test`，test目录作为全部hi-nginx工程的根目录。
- 第二步,`/usr/local/nginx/sbin/hi-project demo cpp /usr/local/nginx`,会看到一个`demo`目录被创建里面有cpp 模板源码和Makefile。后两个参数因为是默认值，可省略。
- 第三步，`make`,正确无误就可以`sudo make install`。
- 第四步， 修改nginx 配置，启用编译生成的demo.so，并reload nginx。

就这么简单。如果重写了源码，修改了Makefile比如添加依赖库之类，可以`make clean`重来一次。

cpp模板Makefile如下：

```Makefile

PROJECT=demo.so
SRC=$(shell find . -type f | egrep *.cpp$$)
OBJ=$(patsubst %.cpp,%.o,$(SRC))

ifndef NGINX_INSTALL_DIR
NGINX_INSTALL_DIR=/usr/local/nginx
endif

CXX=g++ 
CXXFLAGS+=-O3 -std=c++11 -fPIC -Wall -I$(NGINX_INSTALL_DIR)/include
LDLIBS+=
LDFLAGS+=-shared 

all:$(PROJECT)

$(PROJECT): $(OBJ)
	$(CXX) $(CXXFLAGS) $(LDFLAGS)  -o $@ $^ $(LDLIBS)

clean:
	rm -f  $(OBJ) $(PROJECT)

install:
	test -d $(NGINX_INSTALL_DIR)/cpp || mkdir -p $(NGINX_INSTALL_DIR)/cpp
	install $(PROJECT) $(NGINX_INSTALL_DIR)/cpp


```

常常需要自定义的是`CXXFLAGS+=`和`LDLIBS+=`，可以添加自己需要编译选项和库，比如要添加libfoo.so，它在/opt/lib，那么可以做如下：

```Makefile

CXXFLAGS+=-L/opt/lib
LDLIBS+=-lfoo

```
如果使用clang++，可以把`CXX=g++`改成`CXX=clang++`。



cpp模板源码是这样的：

```cpp
#include "servlet.hpp"
namespace hi{
class hello : public servlet {
    public:

        void handler(request& req, response& res) {
            res.headers.find("Content-Type")->second = "text/plain;charset=UTF-8";
            res.content = "hello,world";
            res.status = 200;
        }

    };
}

extern "C" hi::servlet* create() {
    return new hi::hello();
}

extern "C" void destroy(hi::servlet* p) {
    delete p;
}

```

要理解和用好cpp servlet类非常简单，只需熟悉STL即可。

`sudo make install`会把demo.so安装在hi-nginx安装目录下的cpp文件夹。编译出来的demo.so要能够正确运行，除了需要正确配置依赖外，还需要留意它的使用权限。因此，有时需要`chmod +x`。

