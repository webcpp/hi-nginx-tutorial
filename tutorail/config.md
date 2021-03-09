
# 配置命令



- directives : content: loc,if in loc
    - hi,default: ""

    example:
    
```nginx
            location = /hello {
                hi cpp/hello.so ;
            }
```

- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_need_tokens,default: on

    example:

```nginx

        hi_need_tokens on|off;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_cache_method,default: GET

    example:

```nginx

        hi_cache_method GET|POST|PUT|HEAD;

```


- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_need_cache,default: on

    example:

```nginx
        hi_need_cache on|off;
```

- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_cache_size,default: 10

    example:

```nginx
        hi_cache_size 10;
```

- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_cache_expires,default: 300s

    example:

```nginx
        hi_cache_expires 300s;
```

- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_need_kvdb,default: off

    example:

```nginx
        hi_need_kvdb on|off;
```

- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_kvdb_size,default: 10

    example:

```nginx
        hi_kvdb_size 10;
```

- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_kvdb_expires,default: 300s

    example:

```nginx
        hi_kvdb_expires 300s;
```



- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_need_headers,default: off

        example:

```nginx
        hi_need_headers on|off;
```

- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_need_cookies,default: off

        example:

```nginx
        hi_need_cookies on|off;
```

- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_need_session,default: off

        example:

```nginx
        hi_need_session on|off;
```

- directives : content: http,srv,loc,if in loc ,if in srv
    - hi_session_expires,default: 300s

    example:

```nginx
        hi_session_expires 300s;
```
     

- directives : content: loc,if in loc
    - hi_python_content,default: ""

    example:
    
```nginx
            location = /pyecho {
                hi_python_content "hi_res.status(200)\nhi_res.content('hello,world')" ;
            }
```

- directives : content: loc,if in loc
    - hi_python_script,default: ""

    example:
    
```nginx
            location ~ \.py$  {
                hi_python_script python;
            }
```

or

```nginx
            location / {
                hi_python_script python/index.py;
            }
```


- directives : content:  http,srv,if in srv
    - hi_lua_package_path,default: ""

    example:
    
```nginx

            hi_lua_package_path '/usr/local/nginx/lua/?.lua;';

```

- directives : content:  http,srv,if in srv
    - hi_lua_package_cpath,default: ""

    example:
    
```nginx

            hi_lua_package_cpath '/usr/local/nginx/lua/?.so;';

```



- directives : content: loc,if in loc
    - hi_lua_content,default: ""

    example:
    
```nginx
            location = /luaecho {
                hi_lua_content "hi_res:status(200)\nhi_res:content('hello,world')" ;
            }
```

- directives : content: loc,if in loc
    - hi_lua_script,default: ""

    example:
    
```nginx
            location ~ \.lua$  {
                hi_lua_script lua;
            }
```

or

```nginx
            location / {
                hi_lua_script lua/index.lua;
            }
```

- directives : content:  http,srv,if in srv
    - **hi_qjs_memory_limit** ,default: 1024m

    example:
    
```nginx


            hi_qjs_memory_limit 1024m;


```

- directives : content:  http,srv,if in srv
    - **hi_qjs_stack_limit** ,default: 8m

    example:
    
```nginx


            hi_qjs_stack_limit 8m;


```

- directives : content:  http,srv,if in srv
    - **hi_qjs_ctx_called_limit**,default: 10240

    example:
    
```nginx


            hi_qjs_ctx_called_limit 10240;


```


- directives : content: loc,if in loc
    - hi_qjs_script,default: ""

    example:
    
```nginx
            location ~ \.js$  {
                hi_qjs_script qjs;
            }
```

or

```nginx
            location / {
                hi_qjs_script qjs/index.js;
            }
```



- directives : content: http,srv,if in srv
    - hi_java_classpath,default:"-Djava.class.path=."

    example:

```nginx
hi_java_classpath "-Djava.class.path=.:/usr/local/nginx/java:/usr/local/nginx/java/hi-nginx-java.jar";

```

- directives : content: http,srv,if in srv
    - hi_java_options,default:"-server -d64"

    example:

```nginx
hi_java_options "-server -d64 -Dnashorn.args=--global-per-engine";

```

- directives : content: loc,if in loc
    - hi_java_servlet,default:""

    example:

```nginx
hi_java_servlet hi/jhello;


```

- directives : content : http,srv,loc,if in loc ,if in srv
    - hi_java_servlet_cache_expires,default:300s

    example:

```nginx
hi_java_servlet_cache_expires 300s;

```

- directives : content : http,srv,loc,if in loc ,if in srv
    - hi_java_servlet_cache_size,default:10

    example:

```nginx
hi_java_servlet_cache_size 10;

```

- directives : content: http,srv,if in srv
    - hi_java_version,default:8

    example:

```nginx
hi_java_version 8;

```

- directives : content: loc if in loc
    - **hi_subrequest**, default : ""

    example:

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

- directives : content: http,srv,loc,if in loc ,if in srv
    - **autoblacklist**, default : "off"
    - **autoblacklist_size**, default : 1024
    - **autoblacklist_limit**, default : 30
    - **autoblacklist_expires**, default : 86400s 

    example:

```nginx

	autoblacklist on|off;
	autoblacklist_size 1024;
	autoblacklist_expires 86400s;
	autoblacklist_limit 30;

```



# 特别说明

以上配置命令演示均使用相对路径。如果你按照配置启动后发现404错误，多半是你启动hi-nginx的方式使得相对路径无效。

这时，较差的建议是把配置改成绝对路径。较好的建议是只通过`systemctl`启动，从而确保启动目录正确、从而相对路径有效。

修改配置后，一定要`systemctl reload nginx`。

