# 子请求

这个概念应该是nginx特有的。它的能力就像nginx一样，被严重高估。即便是在nginx最为擅长的静态文件服务方面，nginx也不是最棒的。

为了使用子请求，很简单，通过命令`hi_subrequest`：

```nginx


        location ^~ /sub {
                hi_subrequest '/query';
                hi_lua_content 'hi_res:header("Content-Type",hi_req:get_form("__subrequest_content_type__"))\nhi_res:status(tonumber(hi_req:get_form("__subrequest_status__")))\nhi_res:content(hi_req:get_form("__subrequest_body__"))';
        }

        location ^~ /query {
                internal;
                proxy_pass http://http://hq.sinajs.cn/;
                proxy_set_header Accept-Encoding '';
        }
#  curl -i http://localhost/sub?list=sh601006

```

正常访问`/sub`，hi-nginx会依据hi_subrquest的指示，先访问`/query`获得数据，然后把三个数据存储在请求的form属性中：

- `__subrequest_content_type__`
- `__subrequest_status__`
- `__subrequest_body__`

开发者直接从中取值，然后随意行事即可。

注意：所有向`/sub`提交的参数都会悉数提交给子请求`/query`,`hi_subrequest`支持任意HTTP METHOD。