# 编程接口

应用开发者可以很方便地使用hi-nginx提供的编程接口完成开发工作。

相对于不同的编程语言，编程接口的实现略有不同，但是总体上非常简易，使用的是特定语言的基本数据结构和方法，而且已经完全概念化。开发者只需了解http协议的基本概念即可轻松开发出高性能的web应用。

## cpp 接口

cpp接口基于STL实现，非常容易操作，不多说了。如果还不熟悉STL，赶紧看下；主要是`std::string`,`std::unordered_map<std::string, std::string>`和`std::unordered_multimap<std::string, std::string>`。是不是太简单？

具体实现参考hi-nginx安装目录下的`include`。


## java,javascript以及jsr-223 JVM language 接口

java接口也非常简单，包括`String`,`HashMap<String, String>`和`HashMap<String, ArrayList<String>>`。没什么好说的。

具体实现参考[hi-nginx-java](https://github.com/webcpp/hi-nginx-java)

## python，lua 接口

这两种语言的接口符号名是一致的：

`hi_req`和`hi_res`是两个嵌入的对象实例，由hi-nginx管理其生命期。它们提供统一的方法来操控http协议:

### hi_req
- uri
- method
- client
- param
- user_agent
- has_header
- get_header
- has_form
- get_form
- has_session
- get_session
- has_cookie
- get_cookie
- has_cache
- get_cache
### hi_res
- status
- content
- header
- session
- cache

因为python(以及duktape)和lua使用对象方法的语法略有差异，前者使用`.`，后者使用`:`，所以在python中写

```python
hi_res.status(200)
hi_res.content("hello,world")

```

在lua中则要写作:

```lua
hi_res:status(200)
hi_res:content("hello,world")

```

## quickjs 接口
把python和lua的请求和响应接口合并在一起了。
### hi
- uri
- method
- client
- param
- user_agent
- has_header
- get_header
- has_form
- get_form
- has_session
- get_session
- has_cookie
- get_cookie
- has_cache
- get_cache
- status
- content
- header
- session
- cache


## php 接口

php有神奇的array类型，所以，除了字符串，一切都是它了。

具体实现参考hi-nginx安装目录下的`php/hi`。





# 附注

- 对于php使用者，强烈推荐采用引入`hi/route.php`并采用单一入口模式。
- 对于python使用者，强烈推荐采用[hi.py框架](https://github.com/webcpp/hi.py),它实现单一入口模式，并提供一个类似flask或bottle的迷你框架，但比两者都要快的多。最佳使用方法，请务必参考[hi.py框架例子](https://github.com/webcpp/pyexample)
- 在hi-nginx强劲的缓存机制下，没有什么慢脚本是不能大幅度提速的。

[hi-nginx-demo](https://github.com/webcpp/hi-nginx-demo)提供各种语言的演示开发方法，敬请参考。


