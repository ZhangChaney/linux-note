# 05-Linux三剑客

本文主要讲解grep awk 和 sed三个命令配合正则表达式的使用方法，江湖人称三剑客。

## 1. 基本和拓展正则表达式

基本正则表达式：BRE ---  **^ $  .[ ] ***

拓展正则表达式：ERE --- **() {} ? + |**

由一类特殊字符及文本字符所编写的模式，常用于快速过滤、替换、处理所需的字符串和文本。同样大部分编程语言也都支持真这个表达式

### 基本正则表达式

|    符号    |                             作用                             |
| :--------: | :----------------------------------------------------------: |
|   **^**    | 模式最左侧，匹配以XX开头的字符串，如"^abc"表示匹配以abc开头的字符串 |
|   **$**    | 模式最右侧，匹配以XX结尾的字符串，如"^abc"表示匹配以abc结尾的字符串 |
|     ^$     |                       组合符，表示空行                       |
|   **.**    |           匹配任意一个且只有一个字符，不能匹配空行           |
|     \      |                           转义字符                           |
|   *****    | 匹配前一个字符（连续出现）0次或1次以上，重复0次代表空即匹配所有内容 |
|   **.***   |                    组合符号，匹配所有内容                    |
|  **^.***   |             组合符号，匹配任意多个字符开头的内容             |
|  **.*$**   |             组合符号，匹配任意多个字符结尾的内容             |
| **[abc]**  |       匹配[]集合内的任意一个字符，a或b或c，可以写[a-c]       |
| **[^abc]** |       匹配除了^后面字符的任意字符，a或b或c，^表示取反        |

### 拓展正则表达式

拓展正则表达式必须使用grep -E才生效

|  符号  |                 作用                 |
| :----: | :----------------------------------: |
|   +    |       匹配前一个字符1次或多次        |
| [:/]+  |  匹配括号内的：或者\字符一次或多次   |
|   ?    |        匹配前一个字符0次或1次        |
|   \|   |     表示或者，同时过滤多个字符串     |
|   ()   | 分组过滤，被括起来的内容表示一个整体 |
| a{m,n} |    匹配前一个字符最少m次，最多n次    |
| a{n,}  |        匹配前一个字符最少n次         |
|  a{n}  |          匹配前一个字符n次           |
| a{,n}  |        匹配前一个字符最多n次         |



## 2. grep文本搜索

根据用户指定的条件对文本逐行进行匹配检查，打印匹配成功的结果。

**grep玩不好，运维干不了**

```shell
grep 常用参数
-i  忽略字符大小写
-o  仅显示匹配到的字符串本身
-v  显示不能被模式匹配到的行(取反)
-E  支持拓展正则表达式
-q  静音模式，不输出任何信息
-n  显示行号
-c  统计结果行数
```

### 案例

测试文件data.txt

```tex
My name is chaney,
i am from china.
I like play basketball.

My email is zhangqiuqian01@gmail.com

# 我是一行注释
I am a teacher, i teach python.
```

**1.输出所有以m开头的行**

```shell
[root@localhost ~]$ grep -i "^m" -n data.txt
1:My name is chaney,
5:My email is zhangqiuqian01@gmail.com
```

**2.输出所有以i开头的行**

```shell
[root@localhost ~]$ grep -i "^i" -n data.txt
2:i am from china.
3:I like play basketball.
8:I am a teacher, i teach python.
```

**3.输出所有空行**

```shell
[root@localhost ~]$ grep "^$" -n data.txt 
4:
6:
```

**4.输出所有空行以外的信息同时去除注释。**

```shell
[root@localhost ~]$ grep "^$" data.txt -v | grep "^#" -v -n
1:My name is chaney,
2:i am from china.
3:I like play basketball.
4:My email is zhangqiuqian01@gmail.com
6:I am a teacher, i teach python.
```

**5.输出包含字符s且长度为2的行**

```shell
[root@localhost ~]$ grep ".s" data.txt 
My name is chaney,
I like play basketball.
My email is zhangqiuqian01@gmail.com
```

**6.找出文本中除数字以外的所有内容**

```shell
[root@localhost ~]$ grep "[^0-9]" data.txt 
```



## 3. sed 文本加工

sed是操作、过滤和转换文本内容的强大工具，常用于结合正则表达式对文件实现快速的增删改查，最常用的是过滤和取行。

**语法**

```bash
sed [选项] [sed内置命令字符] [输入文件]
```

**选项**

| 参数选项 |                        作用                         |
| :------: | :-------------------------------------------------: |
|    -n    |      取消默认sed的输出，常与sed内置命令p一起用      |
|    -i    | 直接将修改结果写入文件，不用-i，sed修改的是内存数据 |
|    -e    |               多次编辑,不需要管道符了               |
|    -r    |                    支持正则扩展                     |

**内置命令字符**

|     命令字符      |                          作用                           |
| :---------------: | :-----------------------------------------------------: |
|         a         |    append，对文本追加，在指定行后面添加一行/多行文本    |
|         d         |                   Delete，删除匹配行                    |
|         i         |    insert，表示插入文本，在指定行前添加一行/多行文本    |
|         p         |        Print，打印匹配行的内容，通常p与-n一起用         |
| s/正则/替换内容/g | 匹配正则内容，然后替换内容(支持正则)，结尾g代表全局匹配 |

**匹配范围**

|   范围    |                             解释                             |
| :-------: | :----------------------------------------------------------: |
|  空地址   |                           全文处理                           |
|  单地址   |                        指定文件某一行                        |
| /pattern/ |                     被模式匹配到的每一行                     |
| 范围区间  | 10,20  十到二十行 ， 10,+5第十行向下的5行， /pattern1/, /pattern2/ |
|   步长    | 1~2,表示1、3、5、7、9行，2~2两个步长，表示2、4、6、8、10偶数行 |

#### 案例

测试文件data.txt

```tex
My name is chaney,
i am from china.
I like play basketball.

My email is zhangqiuqian01@gmail.com

# 我是一行注释
I am a teacher, i teach python.
```

1. 输出文件第二行和第三行的内容

```shell
[root@localhost ~]$ sed "2,3p" data.txt -n
My name is chaney,
i am from china.
```

2. 过滤出含有email的行

```shell
[root@localhost ~]$ sed "/email/p" data.txt -n
My email is zhangqiuqian01@gmail.com
```

3. 删除有basketball的行

```shell
[root@localhost ~]$ sed "/basketball/d" data.txt -i  # 结果直接写入文件
[root@localhost ~]$ cat data.txt
My name is chaney,
i am from china.

My email is zhangqiuqian01@gmail.com

# 我是一行注释
I am a teacher, i teach python.
```

4.  删除第五行以后的数据

```shell
[root@localhost ~]$ sed "5,$d" data.txt -i
[root@localhost ~]$ cat data.txt
My name is chaney,
i am from china.

My email is zhangqiuqian01@gmail.com
```

5. 将文本中的所有My替换为His

```shell
# s内置符配合g，全局替换，中间的/可以替换为@和#
[root@localhost ~]$ sed "s/My/His/g" data.txt -i
[root@localhost ~]$ cat data.txt
His name is chaney,
i am from china.

His email is zhangqiuqian01@gmail.com
```

6. 替换所有的i为He，am改为is，同时替换email为 linux@163.com

```shell
# 两次及多次编辑增加-e参数
[root@localhost ~]$ sed -e "s/i/He/g" -e "s/am/is/g" -e "s/zhangqiuqian01@gmail.com/linux@163.com/g" data.txt -i
[root@localhost ~]$ cat data.txt
His name is chaney,
He is from china.

His email is linux@163.com
```

7. 在文件的最后一行增加 QQ123456

```shell
[root@localhost ~]$ sed -i "$a QQ:123456" data.txt
[root@localhost ~]$ cat data.txt
```

8. 在每一行最后添加分割线

```shell
[root@localhost ~]$ sed -i "a ---------------" data.txt
[root@localhost ~]$ cat data.txt
```

#### 实战

sed获取ens33网卡ip地址

```Shell
# 管道符写法
[root@localhost ~]$ ifconfig ens33 | sed "2p" -n | sed "s/^.*inet//" | sed "s/netmask.*$//"
# -e多次编辑写法
[root@localhost ~]$ ifconfig ens33 | sed -e "2s/^.*inet//" -n -e "s/netmask.*$//p"

```



## 4. awk 文本格式化

awk支持条件判断、数组、循环等功能。awk擅长文本格式化且输出格式化的结果。

**语法**

```shell
awk [options] pattern {action} file
```

awk默认以空格为分隔符。

```shell
# 数据 data.txt
a1 a2 a3
b1 b2 b3
c1 c2 c3
[root@localhost ~]$ awk '{print $1}' data.txt
a1
b1
c1
```

通过$1获取到了第一列的数据，同样的可以通过$n获取到第n列的数据。









































