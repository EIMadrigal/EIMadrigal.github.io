---
title: Golang FAQ
url: golang-faq
date: 2017-01-30 03:01:00
tags:
---

```golang
go build  // 编译
go run  // 编译运行
go fmt  // format
go install
go get
go test
```

types of package
 1. 可执行包：只有名称为main才是
 2. reusable：其它都是库

bool
string
int
float64

Go不是面向对象的语言，因此没有class/instance的概念

![在这里插入图片描述](https://img-blog.csdnimg.cn/54aa480dbe924f0a86bdb02b6af3c73a.png)

数组只能固定长度，传入变量只能创建为定义了size的切片

```go
length := 5
array := [length]int  // error: non-constant array bound length
array := make([]int, length)
```