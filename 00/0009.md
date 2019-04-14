# 安全

hi-nginx自带一个安全模块:autoblacklist。该模块能动态地自定义IP黑名单。

## 使用方法

```nginx

	autoblacklist on|off;#开关，默认关闭
	autoblacklist_size 1024;#黑名单长度，默认1024
	autoblacklist_expires 86400s;#恶意ip封禁时长，默认1天
	autoblacklist_limit 30;#客户端限速指标，默认每秒30次

```