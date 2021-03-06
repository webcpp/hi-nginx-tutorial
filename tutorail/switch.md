# 开关

这一节专门讲各种开关。

在hi-nginx中，一共有6个开关：
- 系统缓存开关
- 用户缓存开关
- header开关
- cookie开关
- session开关
- tokens开关

## 系统缓存开关

```nginx

    hi_need_cache on|off；
    hi_cache_method GET|POST|PUT|HEAD;
    hi_cache_size 50;
    hi_cache_expires 5m;

```
这个开关负责开启或者关闭系统级缓存机制。该机制为进程级机制，能够自动缓存响应内容，一旦命中，直接从内存中提取内容响应。

它是距离客户端最近的缓存，采用LRU+过期时间混合控制算法更新缓存。它能够大幅增强hi-nginx的并发能力，对于计算密集而无需经常更新的响应，强烈推荐开启该机制。

使用时，应该合理设置`hi_cache_size`和`hi_cache_expires`，已达到最佳效果。

注意，该机制仅对四种请求方法：GET|POST|PUT|HEAD有效,且必须响应码为200。


## 用户缓存开关

```nginx

    hi_need_kvdb off;
    hi_kvdb_size 50;
    hi_kvdb_expires 5m;

```

这个开关负责开启或者关闭用户级缓存机制。该机制与系统级缓存一样，属于进程级机制。不同于后者，它是应用开发者能够控制的缓存机制。应用开发者可以把它视为内嵌在nginx工作进程中的NOSQL,它采用的也是LRU+过期时间混合控制算法更新缓存。

对于不同的语言，该机制提供的api稍有不同，但不管如何，总体是依然是非常方便的。

该机制有利于应用开发者细粒度地控制需要缓存的内容。结合系统缓存机制一起使用，效果更佳！



以上两种缓存机制已经能够很好的提升应用的并发能力，无需开发者在语言层面构建自己的缓存机制。通常，语言级的缓存机制也不及进程级的缓存有效！



## header 开关

```nginx

    hi_need_headers on|off;

```
这个开关负责开启或者关闭客户端传过来的http协议头信息。如果应用不需这些信息，请关闭。

## cookie 开关

```nginx

    hi_need_cookies on|off;

```

这个开关负责开启或者关闭客户端传过来的cookie。如果应用不需这些信息，请关闭。

## session 开关

```nginx
    userid on;
    userid_name SESSIONID;
    userid_domain localhost;
    userid_path /;
    hi_need_session on|off;
    hi_session_expires 300s;

```
这个开关负责开启或者关闭会话机制。该机制需配合nginx内置的userid模块和redis服务器协调使用。请注意，`userid_name`必须是`SESSIONID`。

开启该机制时，`hi_need_cookies`是同步开启的。

如果应用不需会话，请关闭。

当前会话机制通过leveldb实现。

特别注意:开启会话管理开关后，安装目录下的`leveldb`文件夹需要可写可读权限。最简单的办法是`user  root;`。


## tokens开关

```nginx
    hi_need_tokens on|off;
```

该开关负责显示或者隐藏hi-nginx版本信息。