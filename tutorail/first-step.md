# 起步

新版hi-nginx统一采用uri映射模块的方式加载应用。

对cpp而言，一个应用模块就是一个动态库(*.so)；对python而言，就是一个python模块; 对lua而言，就是一个lua模块。

## cpp

```cpp

#include "servlet.hpp"

namespace hi {
class test : public servlet{
    public:
        test()=default;
        virtual~test()=default;
        void handler(const request& req,response& res)
        {
            res.set_header("Content-Type", "text/plain;charset=UTF-8");
            res.content = "welcome to cpp\n";
            res.status = 200;
        }
};

#ifdef __cplusplus
   extern "C" {
#endif

servlet* create() {
    return new test();
}

void destroy(servlet* p) {
    delete p;
}

#ifdef __cplusplus
   }
#endif

```

## java

请参考[hi-nginx-java](https://hi-nginx-java.hi-nginx.com/index.html)

## python

新版hi-nginx改进了python模块的加载方式。直接架构在python语言本身的模块加载机制之上。
参考`app/python`。

## lua

新版hi-nginx改进了lua模块的加载方式。直接架构在lua语言本身的模块加载机制之上。
参考`app/lua`。
