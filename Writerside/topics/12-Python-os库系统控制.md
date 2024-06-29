# 3.1-Python-os库系统控制

os库提供了许多方法用来执行系统命令，开始逐渐替代shell脚本进行自动化运维工作。os是Python内置库，无需安装直接导入。

```python
import os
```

## 1. 获取当前目录

os.getcwd()方法可以获取当前执行文件所有路径，返回结果为字符串，等价于pwd命令。

```python
print(os.getcwd())
# D:\BaiduSyncdisk\code\Py\script\devops
```

## 2. 查看路径下所有文件

os.listdir()方法快获取指定目录下的所有文件和文件夹，返回结果为列表，默认为当前目录。

```python
print(os.listdir())
print(os.listdir(r'D:/'))
```

## 3. 路径拼接

os.path.join()方法可以对文件路径进行拼接，会根据操作系统修改不同的分隔符。

```python
print(os.path.join(os.getcwd(), 'test'))
```

## 4. 合法校验

os.path.exists()方法可以快速判断指定文件或目录是否存在。

```python
print(os.path.exists(r'D:/'))
print(os.path.exists(r'D:/abc'))
```

## 5. 创建目录

os.makedirs()方法可以快速的帮你创建空目录。需要注意的是该方法无法直接创建层级目录。

```python
os.makedirs(r'./test')
```



## 6. 重命名

os.rename()方法可以帮助你重命名文件或目录。

```python
os.rename(r'./test', r'./data')
```

## 7. 执行系统命令

os.system()方法可以执行命令行指令。

```python
os.system('dir')
```

