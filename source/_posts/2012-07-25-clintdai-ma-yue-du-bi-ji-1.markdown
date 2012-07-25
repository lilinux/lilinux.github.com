---
layout: post
title: "clint代码阅读笔记"
date: 2012-07-25 17:18
comments: true
categories: 笔记 Python clint 管道输入 列表解析
author: lilinux
published: true
---

一直认为自己的水平还有待提高，所以尝试在[github](http://www.github.com)上找一些有意思的开源代码阅读，即可以陶冶情操，又可以学到一些在实践中各种闪光的东西，如果能参与进去那就更好了。

最先看的是```clint```, 原托管地址[在此](https://github.com/kennethreitz/clint)

<!--more-->

clint是一个命令行程序的辅助库，它做了一些繁琐的事，截一断介绍

```
C ommand L ine IN terface T ools .

Clint is awesome. Crazy awesome. It supports colors, but detects if the session is a TTY, so doesn't render the colors if you're piping stuff around. Automagically.

Awesome nest-able indentation context manager. Example: (with indent(4): puts('indented text')). It supports custom email-style quotes. Of course, it supports color too, if and when needed.

It has an awesome Column printer with optional auto-expanding columns. It detects how wide your current console is and adjusts accordingly. It wraps your words properly to fit the column size. With or without colors mixed in. All with a single function call.

The world's easiest to use implicit argument system w/ chaining methods for filtering. Seriously.

```

确实看到了一些亮点，做笔记如下：

获取管道传进来的数据
-----------------------------------

管道或重定向将数据发送到当前进程的标准输入。首先需要确定标准输入是否连接了管道然后再读出。

```python piped_in 
if sys.stdin.isatty():
    return sys.stdin.read()
else:
    return None
```
原理很简单，但不容易想到

progressbar
------------------------------------

在命令行下的进度条。原来在一些安装或检测脚本的执行结果中看到，进度条会在更新时覆盖掉原有的状态，原来一直以为是和终端相关的很帅气的功能，看到代码原来是很少见的回车符```\r```，它只回到行首而不换行。

```python update state in this line
for i in range(100, 0, -1):
    sys.stderr.write('time left: %3d\r'%i)
    time.sleep(1)
```

性能优化
--------------------------------------

发现clint的代码中对list的处理不够优雅，有些代码可以改得更快更简单。

比如```tsplit```函数和```schunk```函数

```python tsplit
def tsplit(string, delimiters):
    """Behaves str.split but supports tuples of delimiters."""

    delimiters = tuple(delimiters)
    stack = [string,]

    for delimiter in delimiters:
        for i, substring in enumerate(stack):
            substack = substring.split(delimiter)
            stack.pop(i)
            for j, _substring in enumerate(substack):
                stack.insert(i+j, _substring)

    return stack
```

```python schunk
def schunk(string, size):
    """Splits string into n sized chunks."""

    stack = []

    substack = []
    current_count = 0

    for char in string:
        if not current_count < size:
            stack.append(''.join(substack))
            substack = []
            current_count = 0

        substack.append(char)
        current_count += 1

    if len(substack):
        stack.append(''.join(substack))

    return stack
```

这两段代码具有明显的C程序员风格，而我一直认为**Python代码的好坏可以从缩进层次上看出**，如果缩进得扭扭捏捏，那多半是可以优化的，**尤其是对列表的处理**

我尝试优化这两个函数

```python tsplit2
def tsplit2(string, delimiters):
    delimiters = tuple(delimiters)
    if len(delimiters) < 1:
        return [string,]
    final_delimiter = delimiters[0]
    for i in delimiters[1:]:
        string = string.replace(i, final_delimiter)
    return string.split(final_delimiter)
```

```python schunk2
def schunk2(string, size):
    return [string[i:i+size] for i in range(0, len(string), size)]
```

不管是从缩进上还是从行数上，是不是都要漂亮一些呢。

好，我们来做一下性能对比：

```python tsplit性能对比
In [35]: timeit -n 10000 utils.tsplit(s, ('abc', 'def', 'for', 'xx', 'oo', ' '))  
10000 loops, best of 3: 1.62 ms per loop

In [36]: timeit -n 10000 utils.tsplit2(s, ('abc', 'def', 'for', 'xx', 'oo', ' '))
10000 loops, best of 3: 101 us per loop
```

```python schunk性能对比
In [27]: timeit -n 10000 utils.schunk2(s, 8) 
10000 loops, best of 3: 66.3 us per loop

In [28]: timeit -n 10000 utils.schunk(s, 8) 
10000 loops, best of 3: 956 us per loop
```

竟然都有近10倍以上的性能提升，当然这个比值并不一定准备，我只是随便找了个参数测试。

其实这也印证了另外一条Python和C/C++不同的结论：

**要尽量使用Python的标准库，自己写的代码即使再优化也难以达到和库函数媲美的效率**

The End
----------------------------------

clint的代码很朴实有力，通过亮点的叠加实现了一个非常有用的扩展库。以后写对外的命令行程序一定要考虑使用它。

关于最后那个性能的改良，也许有点画蛇添足，就没有去pull代码了，只发了个issue看开发者怎么说。
