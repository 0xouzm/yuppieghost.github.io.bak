---
title: Python总结 
description: 1. a=a^b   b=b^a   a=a^b...
date: 2018-07-02 16:16:00
categories: python
tags: 面试
---

# Python基础

## 基础语法

1. a=a^b

   b=b^a

   a=a^b

2. 修改不可变类型 TypeError

3. print 调用 sys.stdout.write方法

4. 小内存读取大文件：

   ```python
   def get_lines():
       l=[]
       with open('file.txt','rb') as f:
           data = f.readlines(60000)
       l.append(data)
       yield l
   ```

   通过**生成器**，分多次读取，每次读取数量相对少的数据进行处理

   要考虑的问题： 分批读入数据要记录每次读入数据的位置；分批每次读入数据的大小，太小会在读取操作上耗费过多时间。

5. read、readline、readlines的区别？

   read:读取整个文件

   readline:读取下一行，使用生成器方法

   readlines:读取整个文件到一个迭代器供我们遍历（可指定行数）

6. linux

   - 这个函数接收文件夹的路径作为输入参数
   - 返回该文件夹中文件的路径
   - 以及其包含文件夹中文件的路径

   ```python
   def print_directory_contents(sPath):
       import os
       for sChild in os.listdir(sPath):
           sChildPath = os.path.join(sPath,sChild)
           if os.path.isdir(sChildPath):
               print_directory_contents(sChildPath)
           else:
               print(sChildPath)
   ```

7. 常用的Python标准库：

   - os
   - time
   - random
   - pymysql
   - threading
   - multiprocessing
   - queue

8. 浅拷贝（shallow copy）

   浅拷贝会创建新对象，其内容非原对象本身的引用，而是原对象内第一层对象的引用。

   浅拷贝有三种形式：

   - 切片操作: b=a[:] or b = [x for x in a]

   - 工厂模式: b=list(a)

   - copy: b=copy.copy(a)

     

9. os模块常见方法

   - os.remove()

   - os.rename()

   - os.walk()

   - os.chdir()

   - os.mkdir/makedirs

   - os.rmdir/removedirs

   - os.listdir()

   - os.getcwd()

   - os.chmod()

   - os.path.basename()

   - os.path.dirname()

   - os.path.join()

   - os.path.split()

   - os.path.getsize()

   - os.path.exists()

   - os.path.isabs()

   - os.path.isdir()

   - os.path.isfile()

10. sys 常用方法

    - sys.argv
    - sys.modules.keys()
    - sys.exc_info()
    - sys.exit(n)
    - sys.version
    - sys.maxint
    - sys.maxunicode
    - sys.modules
    - sys.path
    - sys.platform
    - sys.stdout
    - sys.stdin
    - sys.stderr
    - sys.exc_clear
    - sys.exec_prefix
    - sys.byteorder
    - sys.copyright
    - sys.api_version
    - sys.version_info

11. Python 是强语言类型还是弱语言类型？

    Python 是强类型的动态脚本语言。

    **强类型：不允许不同类型相加。**

    **动态：不使用显示数据类型声明，确定一个变量的类型是第一次给它赋值的时候。**

    **脚本语言： 一般也是解释性语言，运行代码只需要一个解释器，不需要编译。**

    