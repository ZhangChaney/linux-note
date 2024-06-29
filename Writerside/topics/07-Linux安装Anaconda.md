# 2.2-Linux安装Anaconda3

## 1. 官网下载

官网下载地址：https://repo.anaconda.com/archive/Anaconda3-2024.02-1-Linux-x86_64.sh

```shell
$ cd /opt/software
$ wget https://repo.anaconda.com/archive/Anaconda3-2024.02-1-Linux-x86_64.sh --no-check-certificate
```

## 2. 安装

```shell
$ chmod +x Anaconda3-2024.02-1-Linux-x86_64.sh
$ ./Anaconda3-2024.02-1-Linux-x86_64.sh

# 一直按回车，然后输入yes
# 输入yes后输入安装路径，直接回车默认安装在/root/anaconda3
# 最后yes

$ source /root/.bashrc
```

## 3. conda基本命令

### 3.1 创建新环境

```bash
$ conda create --name script python=3.9
```


### 3.2 查看所有环境

```bash
$ conda env list
```

### 3.3 激活环境

```bash
$ conda activate script
```

### 3.4 退出当前环境

```bash
$ conda deactivate
```



