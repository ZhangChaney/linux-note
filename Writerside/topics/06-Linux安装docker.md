# 2.1-CentOS 7安装Docker



### 一、yum换源

```bash
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo

yum clean all
yum makecache

yum repolist
```





### 二、移除旧版本docker {id="docker_1"}

```bash
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```





### 三、安装docker-ce

```bash
# 安装依赖
yum install -y yum-utils

yum-config-manager --add-repo   http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

yum install docker-ce -y

# 换源
vim /etc/docker/daemon.json
```

```tex
{
   "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn/"]
}
```





### 四、启动docker

```bash
systemctl start docker   # 启动docker
systemctl enable docker # 开机自启
```

