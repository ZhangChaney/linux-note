# 2.8-Linux部署JumpServer

官方网站：https://www.jumpserver.org/

## 1. 创建JumpServer数据库MySQL

使用python建库脚本快速创建JumpServer数据库

```bash
$ conda activate script
$ python script.py -d jumpserver-db -p 3327
$ docker logs jumpserver-db
$ docker exec -it jumpserver-db mysql -uroot -p123456
```

建库加权

```sql
create database jumpserver default charset 'utf8mb4';
grant all on jumpserver.* to 'root'@'%';
flush privileges;
exit;
```

## 2. 创建JumpServer数据库Redis

拉取镜像

```bash
$ docker search redis
$ docker pull redis
$ docker run -itd --name redis -p 6379:6379 redis --requirepass 123456
```

## 3. 部署jumpserver

```bash
# 拉取镜像
$ docker search jumpserver
$ docker pull jumpserver/jms_all

# 生成随机密钥并初始化token

if [ ! "$SECRET_KEY" ]; then
    SECRET_KEY=`cat /dev/urandom | tr -dc A-Za-z0-9 | head -c 50`;  
    echo "SECRET_KEY=$SECRET_KEY" >> ~/.bashrc;  
    echo $SECRET_KEY;
else
  echo $SECRET_KEY;
fi

if [ ! "$BOOTSTRAP_TOKEN" ]; then
    BOOTSTRAP_TOKEN=`cat /dev/urandom | tr -dc A-Za-z0-9 | head -c 16`;
    echo "BOOTSTRAP_TOKEN=$BOOTSTRAP_TOKEN" >> ~/.bashrc;
    echo $BOOTSTRAP_TOKEN;
else
    echo $BOOTSTRAP_TOKEN;
fi


$ vim ~/.bashrc

SECRET_KEY=AOL8aKCgcutwHuxOxaqgdl7cwpZiifgfimgAuNtOWxCZjVDAbX
BOOTSTRAP_TOKEN=gbM7dO4dtS5qAQ6d

# 查看mysql和redis的ip
$ docker inspect jumpserver-db
# 172.17.0.4
$ docker inspect redis
# 172.17.0.2

$ docker run -itd -p 2688:2222 -p 81:80 --restart=always \
    --name jumpserver --privileged=true \
    -v /opt/software/jumpserver/data:/opt/jumpserver/data \
    -v /opt/software/jumpserver/koko:/opt/koko/data \
    -v /opt/software/jumpserver/lion:/opt/lion/data \
    -e SECRET_KEY=AOL8aKCgcutwHuxOxaqgdl7cwpZiifgfimgAuNtOWxCZjVDAbX \
    -e BOOTSTRAP_TOKEN=gbM7dO4dtS5qAQ6d \
    -e DB_HOST=172.17.0.4 -e DB_PORT=3306 -e DB_USER=root -e DB_PASSWORD=123456 \
    -e DB_NAME=jumpserver -e REDIS_HOST=172.17.0.2 -e REDIS_PORT=6379 \
    -e REDIS_PASSWORD=123456 jumpserver/jms_all

$ docker logs -f jumpserver
```

## 4. 测试

浏览器输入ip:81访问，如192.168.110.211:80

默认账号密码admin admin
