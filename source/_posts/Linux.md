---
title: Linux
url: linux-commands
date: 2019-05-19 22:48:00
description: Linux入门
categories: Computer Science
tags: [Tools]
---

## Linux
```shell
pwd  # print working directoty
cd /  # change directory 输入文件夹部分名按tab自动补全 如果文件夹名字含有空格，需要将文件名用""括起来
mkdir  # make directory
rmdir  # remove directory
rm a.txt  # remove file
ls -l  # list files
touch filename  # create an empty file
cp lab1/original lab2/dup  # 将original复制为dup
cat a.txt  # capture
less a.txt  # q退出
diff a.txt b.txt
head a.txt -n 5  # 查看前5行
tail a.txt -n 5  # 查看后5行
wc -w a.txt  # 查看单词数目 -l行数 -c字符
mv lab1/a.txt lab2/  # 移动
mv lab1/a.txt lab1/newname.txt  # 重命名
rm filename  # delete
xterm  # open a new terminal window
clear

chmod u-r a.txt  # 作者权限删掉r g-r o-r 小组和其他人删除可读权限
chmod u+r a.txt
chmod go-r a.txt
chmod 444 a.txt  # 100 100 100对应u g o的r w x权限

grep searchword a.txt  # 包含单词searchword的内容，也可正则
grep ^Hello a.txt  # 以Hello开头的内容
grep searchword a.txt | wc  # 命令组合

ls > a.txt  # 重定向
```

脚本`test.sh`就是一坨命令，执行脚本就是按照顺序执行这些命令。
```shell
sh test.sh  # 运行脚本
```

```shell
a=10  # 赋值不能加空格
echo $a  # 使用变量时加$ echo输出
c=`expr $a + $b`  # 运算符两侧必须加空格
c=`expr $a \* $b`  # \(\)

if [ $a -gt $b ]  # 比较大小不能用 > < =
then
    echo $a
else
    echo $b
fi

for x in 1 2 3  # for x in {1 .. 3}
do
	echo $x
done

x=1
while [ $x -le 10]
do
	echo $x
	x=`expr $x + 1`
done

a="hello"
b="world"

read a
read b
c=`expr $a + $b`
echo $a + $b = $c

if [ $a = $b ]
if [ $a != $b ]
str3="$str1$str2"  # 拼接
if [ -z $str1 ]  # 是否为空 -n是否为不空

arr=(1 2 3)  # 只能用bash test.sh
echo ${arr[1]}
for i in ${arr[@]}
do
	echo $i
done
```

```shell
echo $USER  # global variable 当前登录用户
cd $HOME
cd ~
echo $PATH
# 原PATH拼接新路径 不要写成PATH=/home
PATH=$PATH:/home/ubuntu/dir  # 可执行程序只能在当前目录下执行，如果要在其他目录执行需要配置环境变量，配置后该目录下的所有可执行程序都可以在任意地方执行

zip hello.zip *  # 打包所有文件
zip hello.zip -r hello/*  # 递归打包所有子文件夹

unzip hello.zip

tar -zcvf hello.tar.gz hello/  # -z使用gzip压缩
tar -zxvf hello.tar.gz  # 解压

wget url -O newname  # 下载
```
