# 03-Shell编程

### 1. Shell变量

- 定义变量时，变量名不加美元符号
  - 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头;
  - 中间不能有空格，可以使用下划线;
  - 不能使用标点符号;
  - 不能使用bash里的关键字;

- 变量的类型
  - 局部变量
    - 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
  - 环境变量
    - 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。
  - Shell变量
    - shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量

```shell
# 变量的声明
name="zhangsan"
for file in `ls /etc`
for file in $(ls /etc)

# 变量的调用
echo $name
echo ${name}

for skill in Ada Coffe Action Java; do
	echo "I am good at ${skill}Script"
done

# 只读变量 readonly
city="Beijing"
readonly name
city="NewYork"

# 删除变量名
unset name
```

### 2. Shell的字符串

- 字符串是shell编程中最常用最有用的数据类型，字符串可以用单引号，也可以用双引号，也可以不用引号。
- 单引号
  - 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的;
  - 单引号字串中不能出现单独一个的单引号，但可成对出现，作为字符串拼接使用。
- 双引号
  - 双引号里可以有变量
  - 双引号里可以出现转义字符

```shell
#声明字符串
str1="he1lo world 1"
str2='helllo world 2'

# 字符串拼接--双引号
name='sunwukong'
namel="hello,"$name"!"
name2="hello,${name}!"

#字符串拼接--单引号
passwd='123456'
passwd1='hello,'${passwd}''
passwd2='hello,${passwd}'
echo passwd1:$passwd1 - passwd2:$passwd2

# 字符串的长度
data='1234567'
echo ${#data}
echo ${data:1:4}
```

### 3. Shell数组

- bash支持一维数组(不支持多维数组)，并且没有限定数组的大小。

- 数组元素的下标由0开始编号。获取数组中的元素要利用下标，下标可以是整数或算术表达式，其值应大于或等于 0。

```shell
# 定义数组 括号来表示数组，数组元素用"空格"符号分割开
# 数组名=(值1 值2..· 值n)
hobbies=('basketball' 'football' 'tennis')

# 读取数组 ${数组名[下标]}
hobby1=${hobbie[1]}

#使用 @ 符号可以获取数组中的所有元素
echo ${hobbies[@]}

# 获取数组的长度
1ength1=${#hobbies[@]}
length2=${#hobbies[*]}
```

### 4. Shell传递参数

- 我们可以在执行 Shel 脚本时，向脚本传递参数，脚本内获取参数的格式为 $n，n代表一个数字，1为执行脚本的第一个参数，2 为执行脚本的第二个参数。
- 例如可以使用 $1、$2 等来引用传递给脚本的参数，其中 $1 表示第一个参数，$2 表示第二个参数，依此类推。

```shell
#!/bin/bash
echo "Shell 传递参数:";
echo "执行的文件名：$0";
echo "第一个参数为：$1";
echo "第二个参数为：$2";
echo "第三个参数为：$3";
echo "参数个数为：$#";
```

### 5. Shell运算符

```shell
#!/bin/bash

a=10
b=20

val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

# 乘号(*)前边必须加反斜杠(\)才能实现乘法运算；
val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"
```

**逻辑运算符**

```shell
!   # 非
-o  # 或
-a  # 与
```

**比较运算符**

```shell
-gt  # 大于
-lt  # 小于
-eq  # 相等
-ne  # 不相等
-ge  # 大于等于
-le  # 小于等于
```



```shell
#!/bin/bash

a=10
b=20

val=`expr ${a} + ${b}`
echo "${a} + ${b} = ${val}"

val=`expr ${a} - ${b}`
echo "${a} - ${b} = ${val}"

val=`expr ${a} \* ${b}`
echo "${a} * ${b} = ${val}"

val=`expr ${a} / ${b}`
echo "${a} / ${b} = ${val}"

val=`expr ${a} % ${b}`
echo "${a} % ${b} = ${val}"
```

### 6. 条件判断

**if -else语句，表达式用[  ]或者((  ))包裹，结尾用fi结束**

```shell
#!/bin/bash

a=$1
b=$2

echo a=${a} b=${b}

# if-else的写法
if [ $a -gt $b ]
then
    echo "a大于b"
else
    echo "a小于等于b"
fi
```

**if-elif-else语句**

```shell
#  if-elif-els的写法
if [ $a -gt $b ]
then
    echo "a大于b"
elif [ $a -lt $b  ]
then
    echo "a小于b"
else
    echo "a等于b"
fi
```

**case-esac语句**

**case ... esac** 为多选择语句，与其他语言中的 switch ... case 语句类似，是一种多分支选择结构，每个 case 分支用右圆括号开始，用两个分号 **;;** 表示 break，即执行结束，跳出整个 case ... esac 语句，esac（就是 case 反过来）作为结束标记。

可以用 case 语句匹配一个值与一个模式，如果匹配成功，执行相匹配的命令。

```shell
#!/bin/bash

echo '输入 1 到 4 之间的数字:'
echo -e '你输入的数字为:\c'
read aNum

case $aNum in
    1)
        echo '你选择了 1'
    ;;
    2)
        echo '你选择了 2'
    ;;
    3)
        echo '你选择了 3'
    ;;
    4)
        echo '你选择了 4'
    ;;
    *)
        echo '你没有输入 1 到 4 之间的数字'
    ;;
esac
```

### 7. 循环控制

#### for循环格式：

```shell
# 标准格式
for item in item1 item2 item3 ..
do
	command1
	command2
done

# 单行格式
for item in item1 item2 item3; do command1;command2... done
```

当变量值在列表里，for 循环即执行一次所有命令，使用变量名获取列表中的当前取值。命令可为任何有效的 shell 命令和语句。in 列表可以包含替换、字符串和文件名。in列表是可选的，如果不用它，for循环使用命令行的位置参数。

```shell
#!/bin/bash
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done

# 查看当前所有文件内容
for file in `ls`
do
	cat $file
done

# 顺序输出字符串中的字符：
#!/bin/bash

for str in "This is a string"
do
    echo $str
done
```

#### while循环格式

结合**break**和**continue**

```shell
while condition
do
    command
done
```

```shell
# 案例一：
#!/bin/bash
int=1
while (( $int<=5 ))
do
    echo $int
    let "int++"
done
 
# 案例2  while输入控制
echo '按下 <CTRL-D> 退出'
echo -n '1+1=？ '
while read num
do
    if [ $num == 2 ]
    then
    	echo "答对了, 1 + 1 = ${num}"
    	break
    else
    	echo "答错了，重新回答: "
    	continue
    fi
done
```

#### 死循环

```shell
# 写法一：
while :
do
    command
done

# 写法二：
while true
do
    command
done

# 写法三：
for (( ; ; ))
```

#### until循环

until 循环执行一系列命令直至条件为 true 时停止。

until 循环与 while 循环在处理方式上刚好相反。

一般 while 循环优于 until 循环，但在某些时候—也只是极少数情况下，until 循环更加有用。

```shell
until condition
do
    command
done

# 案例
#!/bin/bash

a=0

until (( $int <= 5 ))
do
   echo $a
   a=`expr $a + 1`
done
```



### 函数

```shell
#!/bin/bash

# 定义函数
hello(){
	echo "hello world!"
}

hello

add(){
	echo "输入第一个数字"
	read number1
	echo "输入第二个数字"
	read number2
	val=`expr $number1 + $number2`
	return $val
}
echo "开始调用add函数"
add  # 调用add函数
echo "add函数返回结果为： $?"  #  $?占位接收参数返回值

params(){
	#  函数获取参数
	echo "第一个参数为：$1"
	echo "第一个参数为：$2"
	echo "参数总数有 $# 个!"
	echo "作为一个字符串输出所有参数 $* !"
}

# 函数调用时传递参数
params 5 6 7 8
```

