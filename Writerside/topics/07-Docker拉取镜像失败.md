# 2.4-Docker拉取镜像失败

```text
docker pull redis

Using default tag: latest
latest: Pulling from library/redis
09f376ebb190: Already exists 
9ae6a7172b01: Pulling fs layer 
2c310454138b: Pulling fs layer 
3eba9ec960aa: Pulling fs layer 
3d36c165ff0a: Waiting 
493d196d734f: Waiting 
4f4fb700ef54: Waiting 
484e0560ae90: Waiting 
```

解决方法

```bash
$ vim /etc/docker/daemon.json

{
    "registry-mirrors" : [
    "https://docker.mirrors.ustc.edu.cn",
    "https://yxzrazem.mirror.aliyuncs.com",
    "http://hub-mirror.c.163.com"]
}


$ systemctl daemon-reload
$ systemctl restart docker.service
```

