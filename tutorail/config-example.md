
# 配置例子

```nginx

server {
    listen 80;
    server_name localhost;
	userid on;
	userid_name SESSIONID;
	userid_domain localhost;
	userid_path /;

    client_max_body_size 100k;

    hi_need_tokens off;
    hi_cache_method GET;
    hi_need_cache on;
    hi_cache_size 10;
    hi_cache_expires 300s;
    hi_need_kvdb off;
    hi_kvdb_size 10;
    hi_kvdb_expires 5m;
    hi_need_headers off;
    hi_need_cookies off;
    hi_need_session off;
    hi_session_expires 300s;
    hi_java_classpath "-Djava.class.path=.:/usr/local/nginx/java:/usr/local/nginx/java/hi-nginx-java.jar:/usr/lib/groovy/lib:/usr/lib/groovy/indy:/usr/lib/groovy/indy/groovy-3.0.6-indy.jar:/usr/lib/groovy/indy/groovy-jsr223-3.0.6-indy.jar";
    hi_java_options "-server -d64 -Dnashorn.args=--global-per-engine";
    hi_java_servlet_cache_expires 300s;
    hi_java_servlet_cache_size 10;
    hi_java_version 8;
    hi_lua_package_path '/usr/local/nginx/lua/?.lua;/usr/local/nginx/lua/package/?.lua;';
    hi_lua_package_cpath '/usr/local/nginx/lua/?.so;';

    hi_qjs_memory_limit 1024m;
    hi_qjs_ctx_called_limit 10240;


    autoblacklist on|off;#开关，默认关闭
	autoblacklist_size 1024;#黑名单长度，默认1024
	autoblacklist_expires 86400s;#恶意ip封禁时长，默认1天
	autoblacklist_limit 30;#客户端限速指标，默认每秒30次

    location ~ \.(cpp|lua|py|qjs|java|php) {
        hi_need_kvdb off;
		hi_need_cache off;
		hi_cache_expires 5s;
		hi_need_cookies off;
		hi_need_session off;
		hi_session_expires 300s;
        hi_need_headers off;
        
        set $app_t $1;
        
        if ( $app_t = cpp ) {
            rewrite ^/(.*)\.cpp$ /$1 break;
            hi cpp/hipp.so;
        }

        if ( $app_t = lua ) {
            rewrite ^/(.*)\.lua$ /$1 break;
		    hi_lua_script lua/index.lua;
        }

        if ( $app_t = py ) {
            rewrite ^/(.*)\.py$ /$1 break;
		    hi_need_cache off;
		    hi_cache_expires 1s;
		    hi_python_script python/index.py;
        }

        if ( $app_t = qjs ) {
            rewrite ^/(.*)\.qjs$ /$1 break;
		    hi_qjs_script qjs/index.js;
        }

        if ( $app_t = java ) {
            rewrite ^/(.*)\.java$ /$1 break;
            hi_need_kvdb on;
		    hi_kvdb_size 50;
		    hi_kvdb_expires 5m;
		    hi_java_servlet hi/jdemo;	
        }
        
    }


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

    location / {
	        access_log off;
            root   html;
            index  index.html index.htm;
        }


}


```


# 特别说明

以上配置命令演示均使用相对路径。如果你按照配置启动后发现404错误，多半是你启动hi-nginx的方式使得相对路径无效。

这时，较差的建议是把配置改成绝对路径。较好的建议是只通过`systemctl`启动，从而确保启动目录正确、从而相对路径有效。

修改配置后，一定要`systemctl reload nginx`(最好用`restart`，NGINX的`reload`有时似乎不起作用。)。

安装包中有示例文件，可参考。