
# 起步

## cpp
```cpp
#include "servlet.hpp"
namespace hi{
class index : public servlet {
    public:

        void handler(request& req, response& res) {
            res.headers.find("Content-Type")->second = "text/plain;charset=UTF-8";
            res.content = "hello,world";
            res.status = 200;
        }

    };
}

extern "C" hi::servlet* create() {
    return new hi::index();
}

extern "C" void destroy(hi::servlet* p) {
    delete p;
}

```

## java

```java
package hi;

public class index implements hi.servlet {

    public index() {

    }

    public void handler(hi.request req, hi.response res) {
        res.set_content_type("text/plain;charset=UTF-8");
        res.status = 200;
        res.content = "hello,world";

    }
}

```

敬请参考[https://github.com/webcpp/jdemo](https://github.com/webcpp/jdemo)。

想写得更快，就安装groovy。用groovy脚本写好，再编译为.class作为java运行。如此，可谓开发、运行两者得兼。

所有.class可打包成jar文件，通过`hi_java_classpath`命令引入，然后通过`hi_java_servlet`调用需要的`servlet`提供服务。更新时，只需`reload`或者`restart`即可。


hi-nginx-java提供不同于java官方`servlet`的全新的java web开发方式——它直接把nginx转变为`servlet`容器服务器。

hi-nginx-java还提供一个路由组件，也可参考[https://github.com/webcpp/jdemo](https://github.com/webcpp/jdemo)。


## python
```python
data='{client},{method},{uri},{user_agent},{param}'.format(client=hi_req.client(),method=hi_req.method(),uri=hi_req.uri(),user_agent=hi_req.user_agent(),param=hi_req.param())
hi_res.header('Content-Type','text/plain;charset=UTF-8')
hi_res.content(data)
hi_res.status(200)

```
或者使用hi.py框架：
```python
from hi import hi
app =hi()

@app.route(r'^/test/?$',['GET','POST'])
@app.route(r"^/$",['GET'])
def hello_world(req,res,param):
    res.header('Content-Type','text/plain;charset=utf-8')
    res.content('hello,world')
    res.status(200)

@app.route(r"^/client/?$",['GET','POST'])
def client(req,res,param):
    res.content('{}<br>{}<br>{}<br>{}<br>{}'.format(req.client(),req.method(),req.uri(),req.user_agent(),req.param()))
    res.status(200)

@app.route(r"^/hello/(?P<who>\w+)?$",['GET'])
def hello(req,res,param):
    res.content('{}={}'.format('who',param['who']))
    res.status(200)



if __name__ == '__main__':
    app.run(hi_req,hi_res)

```

## lua
```lua
local data=string.format('%s,%s,%s,%s,%s',hi_req:client(),hi_req:method(),hi_req:uri(),hi_req:user_agent(),hi_req:param())
hi_res:header('Content-Type','text/plain;charset=UTF-8')
hi_res:content(data)
hi_res:status(200)

```

## php
```php
require_once 'hi/servlet.php';
require_once 'hi/route.php';

class index implements \hi\servlet {

    public function handler(\hi\request $req, \hi\response $res) {
        $app = \hi\route::get_instance();
        $app->add('{^/$}', array('GET'), function ($rq, $rs, &$param) {
            $rs->content = 'hello,world';
            $rs->status = 200;
        });
        
        $app->add('{^/who/(?P<name>\w+)/?$}', array('GET'), function ($rq, $rs, &$param) {
            $rs->content = 'hello,'.$param['name'];
            $rs->status = 200;
        });
        
        $app->add('{^/phpinfo/?$}', array('GET'), function($rq, $rs, &$param) {
            ob_start();
            phpinfo();
            $rs->content = ob_get_contents();
            $rs->status = 200;
            ob_end_clean();
        });

        $app->add('{^/cache/?$}', array('GET'), function($rq, $rs, &$param) {
            $key = 'cache_test';
            $value = 0;
            if (array_key_exists($key, $rq->cache)) {
                $value = intval($rq->cache[$key]) + 1;
            }
            $rs->cache[$key] = $value;
            $rs->content = "$key : " . $value;
            $rs->status = 200;
        });
        $app->run($req, $res);
    }

}

```

## quickjs
```javascipt
import * as hi from "hi";
hi.header('Content-Type','text/plain;charset=UTF-8')
hi.content('hello,world')
hi.status(200)

```