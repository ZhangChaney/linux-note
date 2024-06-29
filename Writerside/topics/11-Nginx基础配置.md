# 2.7-Nginx基础配置

**本文中的Nginx基于Docker容器部署，后续所有Nginx实验均基于Docker操作Nginx。**

Docker安装Nginx教程：http://t.csdnimg.cn/w1yE9

## 一、Nginx基础介绍 {id="nginx_1"}

Nginx是一个高性能的HTTP服务器，也是反向代理服务器

**Nginx常用功能场景：**

- 前端服务，静态页面展示
- 多网站、多域名的网页服务
- 反向代理服务
- 简单的资源下载服务
- 用户行为分析（日志）

Nginx是多进程架构，当Nginx启动时会使用root创建Master进程，由Master进程创建多个work子进程。



## 二、Nginx进程管理命令 {id="nginx_2"}

检测nginx.conf语法

```bash
$ nginx -t
```

重新读取并加载nginx.conf

```bash
$ nginx -s reload
```

停止nginx

```bash
$ nginx -s stop
```



## 三、Nginx配置文件

nginx.conf文件详解

```nginx
##########  全局配置/核心模块/事件驱动  ##########
user  nginx;  # nginx运行用户
worker_processes  auto;  # work进程数量

error_log  /var/log/nginx/error.log notice;  # 日志记录路路径
pid        /var/run/nginx.pid;   # pid记录文件

# 事件驱动（性能设置）
events {
    worker_connections  1024;
}
##############################################

##########  http部署  ##########
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/ *.conf;
    
    #  虚拟主机配置
    server{
        location{
            
        }
    }
}
```



## 四、快速部署一个单虚拟主机

创建虚拟主机子配置文件 **server1.chaney.site.conf**

```bash
$ touch /opt/software/nginx/conf/conf.d/server1.chaney.site.conf
```

手写配置文件

```nginx
server{
    listen 80;  # 监听端口
    server_name server1.chaney.site;  # 监听域名
    
    location / {
        # 根据root参数填写网页根目录信息
        root /usr/share/nginx/html/server1;
        index index.html;
    }
}
```

创建静态资源

```bash
$ mkdir /opt/software/nginx/html/server1
$ echo "<h1>server1</h1>" > /opt/software/nginx/html/server1/index.html
$ echo "server1.txt" > /opt/software/nginx/html/server1/server1.txt
```

热加载nginx配置文件

```bash
$ docker exec -it nginx nginx -t  # 检查配置文件是否错误
$ docker exec -it nginx nginx -s reload  # 热加载nginx配置文件
```

修改windows域名映射， **C:\Windows\System32\drivers\etc**

末行添加以下内容后保存

> 192.168.110.211    server1.chaney.site     chaney.site

浏览器输入域名server1.chaney.site以及server1.chaney.site/server1.txt测试，得到如下内容

> # server1 {id="server1_1"}
>
> 
>
> server1.txt



## 五、基于多域名的主机配置

创建虚拟主机子配置文件 **server1.chaney.site.conf**

```bash
$ vim /opt/software/nginx/conf/conf.d/server1.chaney.site.conf
```

```nginx
server{
    listen 80;
    server_name server2.chaney.site;
    
    access_log /var/log/nginx/server2.access.log;
    error_log /var/log/nginx/server2.error.log;
        
    charset utf-8;
    
    location / {
        root /usr/share/nginx/html/server2
        index index.html;
    }

}
```

检查配置文件

```bash
$ docker exec -it nginx nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

创建静态资源

```bash
$ mkdir /opt/software/nginx/html/server2
$ echo "<h1>我是Server2</h1>" > /opt/software/nginx/html/server2/index.html
```

热加载nginx配置文件

```bash
$ docker exec -it nginx nginx -s reload  # 热加载nginx配置文件
```

修改windows域名映射， **C:\Windows\System32\drivers\etc**

末行添加以下内容后保存

> 192.168.110.211    server2.chaney.site    chaney.site

浏览器分别输入域名 **server1.chaney.site **  **server2.chaney.site **测试分别得到如下内容

> server1.chaney.site
>
> # server1
>
> server2.chaney.site
>
> # 我是Server2



