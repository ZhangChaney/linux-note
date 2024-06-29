# 3.2-Python快速建库脚本

## 1. 编写脚本

```python
# coding: utf-8
# author: chaney
# file  : script.py
# time  : 2024-06-03

# 1. 脚本外部传入的变量： db_name, port, password
#    /opt/software/mysql/database/conf、data、logs
# 2. 第一步 --> 创建文件夹 os.makedirs()
# 3. 第二步 --> 拷贝my.cnf  （shutil）
# 4. 第三步 --> 拼接启动命令 （字符串拼接）
# 5. 第四步 --> 执行命令
import os
import argparse
import shutil

BASE_DIR = '/opt/software/mysql'

args = argparse.ArgumentParser()
args.add_argument('-d', '--database', help='database name', required=True)
args.add_argument('-p', '--port', help='database port', required=True)
args.add_argument('-pw', '--password', help='database password', default='123456')


class CreateDatabase(object):
    def __init__(self, database, port, password):
        self.database = database  # 数据库名称
        self.port = port  # 数据库端口
        self.password = password  # 数据库密码
        self.my_cnf = 'my.cnf'
        # BASE_DIR:/opt/software/mysql  self.database:mysql-02
        # /opt/software/mysql/mysql-02
        self.database_dir = os.path.join(BASE_DIR, self.database)

        self.config_dir = os.path.join(self.database_dir, 'conf')  # 配置文件目录
        self.config_file = os.path.join(self.config_dir, self.my_cnf)  # my.cnf路径
        self.data_dir = os.path.join(self.database_dir, 'data')  # 数据文件目录
        self.logs_dir = os.path.join(self.database_dir, 'logs')  # 日志文件目录

    # 分别创建挂载所需目录
    def create_dir(self):
        print('挂载目录开始创建！')
        os.makedirs(self.data_dir, exits_ok=True)
        os.makedirs(self.config_dir, exits_ok=True)
        os.makedirs(self.logs_dir, exits_ok=True)
        print('挂载目录创建完成！')

    # 拷贝配置文件
    def copy_cnf(self):
        print('配置文件my.cnf开始拷贝！')
        shutil.copy(self.my_cnf, self.config_file)
        print('配置文件my.cnf拷贝完成！')

    # 拼接启动命令
    def get_cmd(self):
        cmd = 'docker run -itd -p {}:3306 --name {} '.format(self.port, self.database)
        cmd += '--restart=always --privileged=true '
        cmd += '-v {}:/var/log/mysql '.format(self.logs_dir)
        cmd += '-v {}:/var/lib/mysql '.format(self.data_dir)
        cmd += '-v {}:/etc/my.cnf '.format(self.config_file)
        cmd += '-v /etc/localtime:/etc/localtime:ro '
        cmd += '-e MYSQL_ROOT_PASSWORD={} mysql:5.7 '.format(self.password)
        print('准备执行命令：')
        print(cmd)
        return cmd

    def run(self):
        self.create_dir()
        self.copy_cnf()
        cmd = self.get_cmd()
        os.system(cmd)
        print('脚本执行完成，请使用docker ps查看是否创建成功！')


if __name__ == '__main__':
    args = args.parse_args()
    db_name = args.database
    db_port = args.port
    db_password = args.password
    opt = CreateDatabase(db_name, db_port, db_password)
    opt.run()


```

## 2. 编写my.cnf

my.cnf放在script.py文件同级目录下

```tex
[mysqld]
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

# 注册集群id
server-id=101
# 开启二进制日志文件
log-bin=mysql-bin
# 设置日志格式
binlog-format=row
# 开启中继日志
relay-log=relay-bin
# 忽略拷贝错误
slave-skip-errors=all

symbolic-links=0

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/
```

## 3. 将script.py和my.conf上传到服务器

执行脚本，创建数据库

```bash
python script.py -d mysql-02 -p 3308
```

## 4. 测试

docker ps 查看是否建库成功。
