# 2.5-MySQL主从配置

## 1. 修改数据库my.cnf文件

修改数据库my.cnf文件，在文件中添加如下内容，其中主数据库的server-id必须要比从库的更小。

```bash
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
```

从库server-id设置为102

## 2. 主库创建拷贝用户

```sql
CREATE USER 'slave'@'%' IDENTIFIED BY 'slave';
GRANT REPLICATION SLAVE ON *.* TO 'slave'@'%';
SHOW MASTER STATUS;
# mysql-bin.000003
# 597
```

记住输出的**file**和**position**。

## 3. 从库配置主库信息

```sql
CHANGE MASTER TO 
MASTER_HOST='10.0.0.11', 
MASTER_PORT=3321,
MASTER_USER='slave', 
MASTER_PASSWORD='slave', 
MASTER_LOG_FILE='mysql-bin.000003', 
MASTER_LOG_POS=597;
START SLAVE;
SHOW SLAVE STATUS \G;
```

查看Slave_IO_Running和Slave_SQL_Running是否为yes。

## 4. 测试

在主库中新建test数据库和test表，检查从库是否拷贝。

```sql
create database test;
use test;
CREATE TABLE `test` (
  `id` int NOT NULL AUTO_INCREMENT,
  `test` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
# 插入数据测试
INSERT INTO `test` (`test`) VALUES ('abc');
INSERT INTO `test` (`test`) VALUES ('123');
```

从库中查询数据

```SQL
mysql> select * from test.test;
+----+------+
| id | test |
+----+------+
|  1 | abc  |
|  2 | 123  |
+----+------+
```



