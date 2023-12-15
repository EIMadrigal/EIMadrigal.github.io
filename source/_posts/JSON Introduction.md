---
title: JSON Introduction
url: json-introduction
date: 2018-07-14 17:48:00
description: JSON简介
categories: Computer Science
tags: [Others]
---

## 理解
JSON（JavaScript Object Notation），一种轻量级的数据交换格式，基于JS的一个子集，但其数据格式与语言无关。  
通俗来说，如果你是PHP，要和JS互相发送信息，那么这时候就可以先将PHP发的信息转为JSON，再发给JS。  
那么有人要问了，为什么自己不能直接学会PHP和JS，直接先将PHP的信息转为JS，不就OK了？  
没错，但是如果你要发给C++，发给Python，发给其他各种各样的语言呢？难道你要学会所有语言，再去发信息？显然不可能。  
所以：
> You are now able to learn only one programming language, in addition to the communications language, JSON, in order to communicate with ANY other programming language.

但要注意：JSON并不是编程语言，只是一种规定的数据格式，这种格式的数据便于计算机处理。  
JSON比较规范的定义是：
> JSON is the text grammer/format for the information that is being sent between programming language.

除了JSON以外，还有一种用于交流的数据格式，XML（Extensiable Markup Language）。但是JSON更加流行。

## 格式
JSON有两种结构：  
1，Object：对象用`{`开始，用`}`结束，对象中的一系列非排序的pair中，名称和值之间用`:`分开；  
2，Array：数组用`[`开始，用`]`结束，数组成员之间用`,`分开。  
名称（name）是字符串；  
值（value）可以是：字符串、数值、对象、布尔值、数组或者`null`。  
字符串：用`""`表示；  
数值：可以是小数或负数，也可用`e`、`E`表示为指数格式；  
对象：就是上述的Object；  
布尔值：`true`或`false`；  
数组：就是上述的Array。  
举个栗子：
```json
//Object & Array
{
    "name": "Andrew",
    "age": "36",
    "number":
    [
        {
            "mobile": "12345678"
        },
        {
            "fax": "87654321"
        }
    ],
    "address":
    {
        "city": "Beijing",
        "code": "10000"
    }
}
```

## 参考
[quora](https://www.quora.com/What-is-JSON-2/answers/50464172?share=8534699f&amp;srid=5OZ0m)  
[wiki](https://zh.wikipedia.org/wiki/JSON)
