# 2.6-Docker安装Nginx

## 一、拉取Nginx镜像 {id="nginx_1"}

```bash
$ docker pull nginx
$ docker images

REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    e784f4560448   2 weeks ago    188M
```

## 二、快速启动Nginx {id="nginx_2"}

```bash
$ docker run -itd --name nginx -p80:80 nginx
$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                               NAMES
a1c60bef14ce   nginx     "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, :::80->80/tcp   nginx
```

关闭防火墙测试

```bash
$ systemctl stop firewalld
```

浏览器输入服务器ip/虚拟机ip

>http://192.168.110.211  可以访问到如下页面
>
># Welcome to nginx!
>
>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
>
>For online documentation and support please refer to [nginx.org](http://nginx.org/).
>Commercial support is available at [nginx.com](http://nginx.com/).
>
>*Thank you for using nginx.*

## 三、创建数据挂载目录

- conf：挂载nginx配置文件
- html：挂载nginx前端页面
- log：挂载nginx日志文件

```bash
$ mkdir -p /opt/software/nginx/conf/conf.d
$ mkdir -p /opt/software/nginx/html
$ mkdir -p /opt/software/nginx/log
```

拷贝nginx配置文件

```bash
$ docker cp nginx:/etc/nginx /opt/software/nginx/conf/
$ mv /opt/software/nginx/conf/nginx/* /opt/software/nginx/conf
$ rm -rf /opt/software/nginx/conf/nginx
```



## 四、挂载启动Nginx

首先删除快速启动的Nginx测试容器

```bash
$ docker rm -f nginx
```

挂载启动Nginx

```bash
$ docker run -itd --name=nginx -p 80:80 --restart=always \
    -v /opt/software/nginx/conf:/etc/nginx  \
    -v /opt/software/nginx/html:/usr/share/nginx/html \
    -v /opt/software/nginx/log:/var/log/nginx \
    --privileged=true nginx:latest
```

挂载测试

```bash
echo '<h1>nginx test</h1>' > /opt/software/nginx/html/index.html
```

浏览器输入服务器ip/虚拟机ip

> http://192.168.110.211  可以访问到如下页面内容
>
> # nginx test





