---
title: fluent python笔记
description: By Luciano Ramalho
date: 2018-06-07 00:14:39
categories: python
tags: 
---

# 数据类型

* * *

### bool
bool(x) 的背后是调用x.__bool__() 的结果；如果不存在 __bool__ 方法，那么 bool(x) 会尝试调用 x.__len__()。若返回 0，则 bool 会返回 False；否则返回True。

*bool> \_\_bool\_\_>\_\_len\_\_*

### collections.namedtuple
自 Python 2.6 开始，namedtuple 就加入到 Python 里，用以构建只有少数属性但是没有方法的对象，比如数据库条目。


# 序列数组

* * *

### 序列

容器序列

　　list、tuple 和 collections.deque 这些序列能存放不同类型的数据。

扁平序列

　　str、bytes、bytearray、memoryview 和 array.array，这类序列只能容纳一种类型。

容器序列存放的是它们所包含的任意类型的对象的引用，而扁平序列里存放的是值而不是引用。

可变序列（仅限数组）

　　list、bytearray、array.array、collections.deque 和memoryview。

不可变序列

　　tuple、str 和 bytes。

![](http://upload-images.jianshu.io/upload_images/12502239-64aa498350f3f65a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 关于迭代器和生成器：

> [白话解释迭代器和生成器](https://segmentfault.com/a/1190000007208388)

***迭代器：含\_\_next\_\_()***

***可迭代：含\_\_iter\_\_()***

***生成器：yield 或 ( 推导式 )***

```
l=[0, 1, 20, 11, 5, 22, 9]

l[2:5] = 100 ➊

>  Traceback (most recent call last):File "", line 1,
>  in*TypeError:can only assign an iterable
```
➊如果赋值的对象是一个切片，那么赋值语句的右侧必须是个可迭代对象。即便只有单独一个值，也要把它转换成可迭代的序列。

### 初始化多维列表

错误用法

` my_list = [[]] * 3`

初始化得到同一个列表的引用*3

正确用法：

` l=[[''] * 3 for i in range(3)]`

总结：注意对象的引用是否变化

### 关于序列的增量赋值运算

**+=** 类似于 ***=**

程序先调用\_\_iadd\_\_，如果没找到，就调用\_\_add\_\_方法，

！可变序列+=后，变量名不会被关联到新的对象，不可变序列因为不存在增量赋值运算，调用\_\_add\_\_后创建新变量赋值给原不可变序列。（str 是一个例外，因为对字符串做 += 实在是太普遍了，所以 CPython 对它做了优化。为 str初始化内存的时候，程序会为它留出额外的可扩展空间，因此进行增量操作的时候，并不会涉及复制原有字符串到新位置这类操作）

栗子：

![](http://upload-images.jianshu.io/upload_images/12502239-7efb570c5a3e337e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/12502239-770fd0c03a1d66f2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 元组
栗子：
```python
 >>> t = (1, 2, [30, 40])

 >>> t[2] += [50, 60]
```
抛出异常同时修改t

**总结：**

**1、不要把可变对象放在元组里面。**

**2、增量赋值不是一个原子操作。我们刚才也看到了，它虽然抛出了异常，但还是完成了操作。**

**3、查看 Python 的字节码并不难，而且它对我们了解代码背后的运行机制很有帮助。**


# 字典和集合

### 创建字典的不同方式：
```python
>>> a = dict(one=1, two=2, three=3)

>>> b = {'one': 1, 'two': 2, 'three': 3}

>>> c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))

>>> d = dict([('two', 2), ('one', 1), ('three', 3)])

>>> e = dict({'three': 3, 'one': 1, 'two': 2})

>>> a == b == c == d == e
True
```

### 减少字典查询次数：
这样写
`my_dict.setdefault(key, []).append(new_value)`

*获取单词的出现情况列表，如果单词不存在，把单词和一个空列表放进映射，然后返回这个空列表，这样就能在不进行第二次查找的情况下更新列表了。*

跟这样写：

```python
if key not in my_dict:
    my_dict[key] = []
    my_dict[key].append(new_value)
```

二者的效果是一样的，只不过后者至少要进行两次键查询——如果键不存在的话，就是三次，用 setdefault 只需要一次就可以完成整个操作。


### defaultdict
defaultdict 里的default_factory只会在\_\_getitem\_\_里被调用，在其他的方法里完全不会发挥作用。比如，dd 是个 defaultdict，k 是个找不到的键， dd[k] 这个表达式会调用 default_factory 的\_\_getitem\_\_ 方法创造某个默认值，而 dd.get(k) 则会返回 None。

### \_\_missing\_\_

```python
class StrKeyDict0(dict): ➊
    def __missing__(self, key):
        if isinstance(key, str): ➋
            raise KeyError(key)
        return self[str(key)] ➌
    def get(self, key, default=None):
        try:
            return self[key] ➍
        except KeyError:
            return default ➎
    def __contains__(self, key):
        return key in self.keys() or str(key) in self.keys() ➏
```
❶ StrKeyDict0 继承了 dict。
❷ 如果找不到的键本身就是字符串，那就抛出 KeyError 异常。
❸ 如果找不到的键不是字符串，那么把它转换成字符串再进行查找。
❹ get 方法把查找工作用 self[key] 的形式委托给 \_\_getitem\_\_，这样在宣布查找失败之前，还能通过 \_\_missing\_\_ 再给某个键一个机会。
❺ 如果抛出 KeyError，那么说明 \_\_missing\_\_ 也失败了，于是返回default。
❻ 先按照传入键的原本的值来查找（我们的映射类型中可能含有非字符串的键），如果没找到，再用 str() 方法把键转换成字符串再查找一次。



### 集合

像 {1, 2, 3} 这种字面量句法相比于构造方法（set([1, 2, 3])）要更快且更易读。后者的速度要慢一些



### dict和set的背后

背后的散列表

- Python 里的 dict 和 set 的效率有多高？
- 为什么它们是无序的？
- 为什么并不是所有的 Python 对象都可以当作 dict 的键或 set 里的元素？
- 为什么 dict 的键和 set 元素的顺序是跟据它们被添加的次序而定的，以及为什么在映射对象的生命周期中，这个顺序并不是一成不变的？
- 为什么不应该在迭代循环 dict 或是 set 的同时往里添加元素？





**散列表看不懂，以后再看**































To be continued... :runner: 