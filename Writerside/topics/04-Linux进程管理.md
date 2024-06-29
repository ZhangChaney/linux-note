# 1.4-Linux进程管理



## 一、进程是什么

在Linux下存在进程process 和线程thread两个操作系统的基本概念。

计算机的核心是CPU，承担机器的计算任务，好比一个工厂，不断的进行加工生产任务。进程就好比是工厂的一个车间，一个工厂可能有多个车间，而线程就好比车间中的工人。



## 二、进程管理命令

### 1. ps查看进程

用于报告当前系统的进程状态，主要用于查询进程信息，搭配kill命令杀死进程

1. ps找出进程id
2. kill -9强制杀死进程，重启进程

```shell
[root@localhost ~]# ps

   PID TTY          TIME CMD
  1950 pts/0    00:00:00 bash
  2014 pts/0    00:00:00 ps
```

PID：进程ID，进程的唯一标识符。

TTY：进程使用的终端号码。

TIME：进程使用CPU的总时间

CMD：正在执行的命令行。

```shell
# 使用grep过滤筛选需要的进程信息
[root@localhost ~]# ps | grep bash

  1950 pts/0    00:00:00 bash
```

```shell
# 查看linux机器所有详细的进程信息
[root@localhost ~]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          2      0  0 16:53 ?        00:00:00 [kthreadd]
root          3      2  0 16:53 ?        00:00:00 [ksoftirqd/0]


-e：列出所有运行的进程
-f：显示详细信息
```

UID：执行进程的用户。

PPID：父进程的ID标识。

C：表示CPU使用的资源百分比。

STIME：进程开始执行的时间

#### PS两种风格参数

- 带 - 的参数
- 不带 - 的参数

```shell
ps ef  # e列出进程所在的环境变量  以ASCII显示进程层级
ps -ef # 查看linux机器所有详细的进程信息
```

##### ps aux

a：显示所有进程用户信息

u：以用户为主的格式显示进程

x：显示所有进程

```shell
[root@localhost ~]$ ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          2  0.0  0.0      0     0 ?        S    16:53   0:00 [kthreadd]
root          3  0.0  0.0      0     0 ?        S    16:53   0:00 [ksoftirqd/0]
```

VSZ：该进程使用的swap内存

RSS：进程所占用的内存量

STAT：

- S 终端睡眠中，可以被唤醒
- s 含有子进程
- R 进程运行中
- D 进程不可中断睡眠
- T 进程已经停止
- Z 僵尸进程
- +： 前台运行进程
- N：低优先级
- <：高优先级
- L：已锁定

##### ps -u

查看指定用户的所有进程

##### ps -eH

显示父子进程的目录结构信息

```shell
[root@localhost ~]$ ps -eH
   PID TTY          TIME CMD
  1057 ?        00:00:00   sshd
  1933 ?        00:00:00     sshd
  1950 pts/0    00:00:00       bash
  2536 pts/0    00:00:00         ps
```

##### ps -o

```bash
[root@localhost ~]$ ps -u root -o pid,uid
   PID   UID
     1     0
```

指定格式显示



#### pstree

查看进程树

```shell
[root@localhost ~]# pstree
systemd─┬─ModemManager───2*[{ModemManager}]
        ├─NetworkManager───2*[{NetworkManager}]
```

####  pgrep

查询指定进程， 判断进程是否存活

```shell
[root@localhost ~]$ pgrep bash  # 查询bash进程
[root@localhost ~]$ pgrep -u root  # 查询root的进程
```

### 2. kill杀死进程

发送相关信号给进程，达到不同效果

```shell
[root@localhost ~]$ kill -l # 列出所有的杀死，终止信号
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
```

#### 常用信号

```shell
1-挂起进程
2-中断信号 ctrl + c发送
3-退出信号 ctrl + \发送
9-强制中断信号  立即杀死
15-kill的默认信号，终止进程
20-暂停信号 ctrl + z发送
```

#### 特殊信号：0

常用在shell脚本中，kill -0 $pid 表示不发送任何信号给pid，但是会对这个pid进行检测，如果结果是0表示进程存在，1表示不存在，其余表示错误。信号0一般用于判断进程是否存在

```shell
[root@localhost ~]# ping www.baidu.com
64 bytes from 183.2.172.42 (183.2.172.42): icmp_seq=1 ttl=128 time=29.7 ms
64 bytes from 183.2.172.42 (183.2.172.42): icmp_seq=2 ttl=128 time=28.2 ms

[root@localhost ~]# ps -ef | grep ping
root       2407   2153  0 23:10 pts/0    00:00:00 ping www.baidu.com
root       2560   2509  0 23:11 pts/1    00:00:00 grep --color=auto ping

[root@localhost ~]# kill -0 2407
[root@localhost ~]# echo $?
0

[root@localhost ~]# kill -9 2407
[root@localhost ~]# kill -0 2407
-bash: kill: (2407) - No such process
[root@localhost ~]# echo $?
1
[root@localhost ~]# 
```

#### killall

kill只能杀死一个进程，killall可以通过名字杀死所有进程

```shell
kill vim  # 杀死所有vim进程
kill nginx  # 杀死所有nginx进程
```

#### pkill

pkill可以通过进程名字杀死多个进程，killall可能一次杀不死（进程可能有子进程，killall要多次杀死），pkill可以直接杀死父子进程

```shell
pkill ping
```

#### 通过终端杀死进程

```shell
[root@localhost ~]# ping jd.com
64 bytes from 111.13.149.108 (111.13.149.108): icmp_seq=1 ttl=128 time=44.5 ms
64 bytes from 111.13.149.108 (111.13.149.108): icmp_seq=2 ttl=128 time=44.0 ms
# 切换到另一个终端
[root@localhost ~]# ps -ef | grep jd.com
root       3195   3139  0 23:23 pts/1    00:00:00 ping jd.com
root       3205   2153  0 23:24 pts/0    00:00:00 grep --color=auto jd.com
[root@localhost ~]# pkill -9 -t pts/1
```



### 3. top资源管理器

top命令用于实时监控处理器状态以及其他硬件负载信息还有动态的进程信息。

```shell
[root@localhost ~]# top
top - 23:29:36 up 42 min,  1 user,  load average: 0.00, 0.02, 0.05
Tasks: 181 total,   1 running, 180 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3863568 total,  3026700 free,   440732 used,   396136 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used.  3130700 avail Mem 

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                             
     1 root      20   0  193716   6888   4128 S   0.0  0.2   0:02.14 systemd                                             
     2 root      20   0       0      0      0 S   0.0  0.0   0:00.01 kthreadd                                            
     3 root      20   0       0      0      0 S   0.0  0.0   0:00.01 ksoftirqd/0         
```

- **top - 23:29:36 up**  ---  当前系统时间
- up 42 min  ---  系统运行时长
- 1 user  --- 几个用户在使用
- load average: 0.00, 0.02, 0.05   --- 平均负载情况 分别是1、5、15分钟的，越小负载越低
- Tasks: 181 total,   1 running, 180 sleeping,   0 stopped,   0 zombie --- 进程使用情况
- PR -- 优先级   NI --- 越高优先级越高  
- VIRT --- 进程使用的虚拟内存总量  VIRT = SWAP + RES
- RES --- 进程使用的物理内存大小  SHR --- 共享内存大小
- S --- 进程状态

#### 常用指令

- top -c：显示命令的绝对路径

- top -d：单位是秒，设置刷新时间

- top -n：刷新次数

- top -p pid：指定查看某一进程信息

- ```shell
  [root@localhost ~]# ps -ef | grep firewall
  root        711      1  0 22:47 ?        00:00:00 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid
  [root@localhost ~]# top -p 711
  ```

- 

- z：打开/关闭颜色
- 1：查看逻辑cpu个数
- M：内存使用量从大到小排序
- xb：某一列高亮，< >符号切换列
- q：退出



### 4. nohup后台运行

nohup可以让程序以忽略挂起信号的形式在后台运行，输出的结果不打印到终端。nohup默认将输出结果写入到当前目录下的nohup.out文件里。如果当前目录的nohup.out文件禁止写入，则会写入到用户家目录下的nohup.out文件里。

```shell
[root@localhost ~]# nohup ping baidu.com
nohup: ignoring input and appending output to ‘nohup.out’

# 此时关闭终端后重新连接
[root@localhost ~]# ps -ef | grep ping
root       2104      1  0 09:26 ?        00:00:00 ping baidu.com
root       2193   2127  0 09:28 pts/0    00:00:00 grep --color=auto ping

[root@localhost ~]# tail -f nohup.out 
64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=138 ttl=128 time=43.8 ms
64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=139 ttl=128 time=43.7 ms
64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=140 ttl=128 time=44.2 ms

[root@localhost ~]# pkill ping
```

可以发现关闭终端后，命令还继续在后台运行。

#### 追加&

追加&后nohup执行命令后不会占用终端，可以继续输入命令，此时进程已经被放入后台运行

```shell
[root@localhost ~]# nohup ping baidu.com &
[1] 2269
[root@localhost ~]# nohup: ignoring input and appending output to ‘nohup.out’
[root@localhost ~]# 
[root@localhost ~]# jobs
[1]+  Running                 nohup ping baidu.com &
```

#### 重定向

将命令执行结果打印到指定文件，例如日志文件等。

```shell
[root@localhost ~]# nohup ping baidu.com > baidu.out 2>&1 &
# 把进程的标准输出和错误输出都写入到baidu.out
```



### 5. fg进程调度

fg可以将后台进程调度到前台

```shell
[root@localhost ~]# ping baidu.com     # 按下ctrl + z
PING baidu.com (110.242.68.66) 56(84) bytes of data.
64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=1 ttl=128 time=45.9 ms
64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=2 ttl=128 time=45.9 ms
^Z
[1]+  Stopped                 ping baidu.com
[1]+  Stopped                 ping baidu.com
[root@localhost ~]# jobs
[1]+  Stopped                 ping baidu.com
[root@localhost ~]# fg 1
ping baidu.com
64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=3 ttl=128 time=45.6 ms
64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=4 ttl=128 time=46.4 ms
```



### 6. init运行级别

检查当前系统的运行级别

```shell
[root@localhost ~]# runlevel
N 5
```

0：关机

1：单用户模式

2：多用户无网络模式

3：完全多用户模式

4：用户自定义的级别

5：GUI的多用户模式

6：重启机器

#### init命令

init是linux进程的初始化工具，是所有的linux进程的父进程，pid默认为1

可以使用init加上运行级别直接操作系统运行级别。

```shell
init 6 # 重启机器
```

### 7. glances资源检测

- CPU/内存使用情况
- 磁盘IO速度
- 文件系统的剩余空间
- 系统负载信息

...

```shell
yum install glances -y
# 无法安装配置EPEL：企业版Linux附加软件包
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

glances  # 按下h查看各项输入指令
Glances 2.5.1 with PSutil 5.6.7

Configuration file: None

 a  Sort processes automatically          b  Bytes or bits for network I/O      
 c  Sort processes by CPU%                l  Show/hide alert logs               
 m  Sort processes by MEM%                w  Delete warning alerts
 u  Sort processes by USER                x  Delete warning and critical alerts 
 p  Sort processes by name                1  Global CPU or per-CPU stats        
 i  Sort processes by I/O rate            I  Show/hide IP module                
 t  Sort processes by TIME                D  Enable/disable Docker stats        
 d  Show/hide disk I/O stats              T  View network I/O as combination    
 f  Show/hide filesystem stats            U  View cumulative network I/O        
 n  Show/hide network stats               F  Show filesystem free space         
 s  Show/hide sensors stats               g  Generate graphs for current history
 2  Show/hide left sidebar                r  Reset history                      
 z  Enable/disable processes stats        h  Show/hide this help screen         
 3  Enable/disable quick look plugin      q  Quit (Esc and Ctrl-C also work)    
 e  Enable/disable top extended stats  
 /  Enable/disable short processes name
 0  Enable/disable Irix process CPU    

ENTER: Edit the process filter pattern
```

#### glances的web可视化

```shell
# 安装所需依赖
yum install -y python python-pip python-devel gcc
# 安装web服务启动模块
pip install bottle

# 启动服务
glances -w
# 浏览器输入192.168.xxx.xxx:61208
# 虚拟机无法访问请先关闭防火墙
iptable -F
```

#### glances的C/S模式

```shell
nohup glances -s -B 0.0.0.0 >/dev/null 2>&1 &  # 后台启动服务端

glances -c ip地址  # 启动客户端
```

