---
layout: post
title: "markdown测试页面"
date: 2012-07-19 18:43
comments: true
categories: markdown 测试
author: admin
published: true
---

0.    前言
=======================

这是一篇**测试**文档

- - - - -

<!--more -->

1.   代码示例
========================

Plain
```
$ sudo make me a sandwich
```

Example With Syntax Highlighting a Caption and Link

``` ruby Discover if a number is prime http://www.noulakaz.net/weblog/2007/03/18/a-regular-expression-to-check-for-prime-numbers/ Source Article
class Fixnum
  def prime?
    ('1' * self) !~ /^1?$|^(11+?)\1+$/
  end
end
```

``` python 选择合适的so
import sys

sub_version = sys.version_info[1]  
if sub_version == 4:
    import setproctitle24 as setproctitlelib
elif sub_version == 6:
    import setproctitle26 as setproctitlelib
else:
    setproctitlelib = None

def setproctitle(title):
    if setproctitlelib:
        setproctitlelib.setproctitle(title)

    def getproctitle():
        if setproctitlelib:
            return setproctitlelib.getproctitle()
        else:
            return None
```

{% include_code ping host /home/lili/src/tmp/ping.cpp %}

Add an optional URL to enable downloading or linking to source.

{% codeblock Javascript Array Syntax lang:js http://j.mp/pPUUmW MDN Documentation %}
var arr1 = new Array(arrayLength);
var arr2 = new Array(element0, element1, ..., elementN);
{% endcodeblock %}

2.   其它插件
===========================

图片：

{% img https://a248.e.akamai.net/assets.github.com/images/modules/header/logo_gist.png?1338945075 #3 %}

> markdown区块
> 这是一个区块
>> 子区块

Quote from a printed work.(区块插件)

{% blockquote Douglas Adams, The Hichhikers Guide to the Galaxy http://octopress.org/docs/plugins/blockquote/ 区块参考链接 %}
Flying is learning how to throw yourself at the ground and miss.
{% endblockquote %}

{% pullquote %}
Surround your paragraph with the pull quote tags. Then when you come to
the text you want to pull, {" surround it like this "} and that's all there is to it.
{% endpullquote %}

正常文字，我们是*正常*文字



3. 正常语法示例
================================================


- - - - -

I am ``tail``
