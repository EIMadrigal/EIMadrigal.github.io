---
title: MIT Operating System Engineering#0 Booting a PC
url: mit-os-0
date: 2020-09-16 09:25:00
description: MIT 6.828
categories: Computer Science
tags: [System,OS]
---

*最近在学MIT的OS课程，lab绝对业界良心。  
XJTU的操作系统课就是写一个系统调用，改下进程软中断通信的代码，代码量不足500。。。  
MIT上课用xv6来教学，lab是做一个完整的小型操作系统JOS。*

---
## 配置环境
虽然没什么技术含量，但是这真的是令人头疼的事。附：[图文教程](https://blog.csdn.net/eye_water/article/details/80638463)  
需要一台x86机器，一般的Linux发行版应该都可以，可以使用`unabenlme -a`查看，如果显示`xxx GNU/Linux`就行，MIT的学生可以使用配置好的远程Server。  
本来打算白嫖下Harvard的服务器，但是没有`root`权限，只能可怜巴巴地在Win环境用虚拟机。  
平台：Vmware Player15，[Ubuntu-14.04.5-desktop-i386.iso](http://old-releases.ubuntu.com/releases/14.04.5/)

 - 检验编译链  
 `objdump -i`：第二行显示elf32-i386；  
 `gcc -m32 -print-libgcc-file-name`：打印出/usr/lib/gcc/i686-linux-gnu/4.8/libgcc.a
 - 安装git：`sudo apt-get install git`
 - 下载qemu  
 建议不要作死，安装MIT Patch过的版本：`git clone https://github.com/mit-pdos/6.828-qemu.git qemu`
 - 安装依赖库  
 官方说要装5个库，但其实libtool-bin好像找不到，不过不影响后续：
```powershell
sudo apt-get install libsdl1.2-dev
sudo apt-get install libglib2.0-dev
sudo apt-get install libz-dev
sudo apt-get install libpixman-1-dev
```
 - 配置qemu  
 切到qemu目录，`./configure --disable-kvm --disable-werror --target-list="i386-softmmu x86_64-softmmu"`  
 `[--prefix=PFX]`可选参数是选择安装路径，这里就默认在`/usr/local`
 - 安装qemu：`sudo make && sudo make install`
 - 下载实验代码  
 根目录下新建6.828目录，切到该目录，`git clone https://pdos.csail.mit.edu/6.828/2018/jos.git lab`，切到`lab`目录，`make`就可以了。

## PC Bootstrap
物理地址空间：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200523082913317.png)  
以前的PC内存只有1MB，Low Memory是PC唯一能用的RAM。VGA Display是VGA缓冲区和固件，BIOS以前都在ROM中，不过现今都在闪存中，做完系统初始化（PCI总线等重要设备），寻找bootable设备(硬盘)，读取boot loader加载OS，将控制权转OS。后来内存远远超过1MB，可用的也就有Extended Memory，不过为了后向兼容，还是保留了Low Memory，这样可用的RAM就被分为了两部分。如果64位系统，那么内存更大，这样为了兼容，32-bit memory mapped devices还是要保留，就又被割裂了。

## The Boot Loader
硬盘的第一个分区存放启动程序，包括一个汇编文件`boot/boot.S`和一个C文件`boot/main.c`，启动程序将CPU从实模式转为32位保护模式，通过特殊的I/O指令读取内核。

## The Kernel