# 编程接口

应用开发者可以很方便地使用hi-nginx提供的编程接口完成开发工作。

相对于不同的编程语言，编程接口的实现略有不同，但是总体上非常简易，使用的是特定语言的基本数据结构和方法，而且已经完全概念化。开发者只需了解http协议的基本概念即可轻松开发出高性能的web应用。

## cpp 接口

cpp接口基于STL实现，非常容易操作，不多说了。如果还不熟悉STL，赶紧看下；主要是`std::string`,`std::unordered_map<std::string, std::string>`和`std::unordered_multimap<std::string, std::string>`。是不是太简单？

具体实现参考hi-nginx安装目录下的以下三个文件：
- `module/lib/request.hpp`
- `module/lib/response.hpp`
- `module/lib/servlet.hpp`


## java 接口

java接口也非常简单，包括`String`,`HashMap<String, String>`和`HashMap<String, ArrayList<String>>`。没什么好说的。

具体实现参考[hi-nginx-java](https://github.com/webcpp/hi-nginx-java)

## python，lua 接口

这两种语言的接口符号名是一致的：

### request
#### 属性
- uri
- method
- client
- param
- user_agent
#### 方法 
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
### response
#### 属性
- status
- content
#### 方法
- set_header
- set_session
- set_cache
- set_cookie


# 附注

新版hi-nginx暂停了会话和缓存机制。请暂停以下4个方法的使用。
- has_session
- get_session
- has_cache
- get_cache



