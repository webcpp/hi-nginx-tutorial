# 性能

hi-nginx是基于NGINX的web应用开发平台，其搭载的web开发语言引擎运行于NGINX工作进程内部。此方案可大幅度提升web应用的运行效率。

机器: 双核四线程 4G Intel(R) Core(TM) i5-5200U CPU @ 2.20GHz 老式笔记本(Linux Mint 20.3  Cinnamon Edition)

配置:`worker_processes  auto;`,`worker_connections  65535;`,`x_expires 0;`

压测软件: wrk

压测参数: `-c30000 -t4 -d30`

结果: 

| 语言引擎 | QPS  |
|---------|------|
| python  | 30k+ |
| lua     | 60k+ |
| cpp     | 70k+ |
| java    | 45k+ |