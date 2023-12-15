---
title: Bomb Lab
url: bomb-lab
date: 2020-10-04 17:09:00
description: CSAPP Lab
categories: Computer Science
tags: [System]
---

给了`bomb.c`和`bomb`二进制可执行目标程序，`bomb.c`不能直接编译和运行，只是有一些提示，但是程序大致结构是：有6个关卡，每个都需要输入（stdin/文件）一个字符串，运行后判断是否输入了正确的字符串。我们需要反汇编`bomb`，找到这6个正确的字符串。  
我是在Amazon的云服务器上完成的，64位Red Hat。

第一步把汇编代码扔到一个文件中，方便调试。

```bash
objdump -d bomb > bomb.asm
```
## phase 1

```clike
0000000000400ee0 <phase_1>:
  400ee0:	48 83 ec 08          	sub    $0x8,%rsp
  400ee4:	be 00 24 40 00       	mov    $0x402400,%esi
  400ee9:	e8 4a 04 00 00       	callq  401338 <strings_not_equal>
  400eee:	85 c0                	test   %eax,%eax
  400ef0:	74 05                	je     400ef7 <phase_1+0x17>
  400ef2:	e8 43 05 00 00       	callq  40143a <explode_bomb>
  400ef7:	48 83 c4 08          	add    $0x8,%rsp
  400efb:	c3                   	retq
```
当参数少于7个时， 参数从左到右放入寄存器: rdi, rsi, rdx, rcx, r8, r9。  
我们的input作为第一个参数存入rdi，第二个参数0x402400存入rsi，传入函数处理，  
调用了`401338`处的函数`strings_not_equal`：

在400ee4设个断点，看看402400里是啥：

```clike
gdb
file bomb
b *0x400ee4
run
x/s Addr// 显示内存值为字符串
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703101513705.png)  
为了确认这就是我们要的答案，再去看看调用的函数：
```clike
0000000000401338 <strings_not_equal>:
  401338:	41 54                	push   %r12
  40133a:	55                   	push   %rbp
  40133b:	53                   	push   %rbx
  40133c:	48 89 fb             	mov    %rdi,%rbx # 第一个参数(地址)存入rbx
  40133f:	48 89 f5             	mov    %rsi,%rbp # 第二个参数(地址)存入rbp
  401342:	e8 d4 ff ff ff       	callq  40131b <string_length> # 用rdi的值调用string_length
  401347:	41 89 c4             	mov    %eax,%r12d # 返回值存入r12d
  40134a:	48 89 ef             	mov    %rbp,%rdi # 第二个参数作为入参调用string_length
  40134d:	e8 c9 ff ff ff       	callq  40131b <string_length>
  401352:	ba 01 00 00 00       	mov    $0x1,%edx
  401357:	41 39 c4             	cmp    %eax,%r12d # 比较两个字符串的长度
  40135a:	75 3f                	jne    40139b <strings_not_equal+0x63> # 不相等跳转
  40135c:	0f b6 03             	movzbl (%rbx),%eax # 将rbx地址中的值(input的第一个字母)存入eax
  40135f:	84 c0                	test   %al,%al
  401361:	74 25                	je     401388 <strings_not_equal+0x50>
  401363:	3a 45 00             	cmp    0x0(%rbp),%al # 比较rbp地址中的值(待比较的第一个字母)
  401366:	74 0a                	je     401372 <strings_not_equal+0x3a> # 相等跳转
  401368:	eb 25                	jmp    40138f <strings_not_equal+0x57> # 不相等跳转
  40136a:	3a 45 00             	cmp    0x0(%rbp),%al
  40136d:	0f 1f 00             	nopl   (%rax)
  401370:	75 24                	jne    401396 <strings_not_equal+0x5e>
  401372:	48 83 c3 01          	add    $0x1,%rbx # 指针+1
  401376:	48 83 c5 01          	add    $0x1,%rbp # 指针+1
  40137a:	0f b6 03             	movzbl (%rbx),%eax
  40137d:	84 c0                	test   %al,%al
  40137f:	75 e9                	jne    40136a <strings_not_equal+0x32> # 跳回循环
  401381:	ba 00 00 00 00       	mov    $0x0,%edx
  401386:	eb 13                	jmp    40139b <strings_not_equal+0x63>
  401388:	ba 00 00 00 00       	mov    $0x0,%edx
  40138d:	eb 0c                	jmp    40139b <strings_not_equal+0x63>
  40138f:	ba 01 00 00 00       	mov    $0x1,%edx
  401394:	eb 05                	jmp    40139b <strings_not_equal+0x63>
  401396:	ba 01 00 00 00       	mov    $0x1,%edx
  40139b:	89 d0                	mov    %edx,%eax
  40139d:	5b                   	pop    %rbx
  40139e:	5d                   	pop    %rbp
  40139f:	41 5c                	pop    %r12
  4013a1:	c3                   	retq
```
所以这个函数就是比较两个字符串是否相同，先比较长度，再比较每个字符。故我们的第一个key就是Border relations with Canada have never been better.

## phase 2
```clike
0000000000400efc <phase_2>:
  400efc:	55                   	push   %rbp
  400efd:	53                   	push   %rbx
  400efe:	48 83 ec 28          	sub    $0x28,%rsp # 栈指针-40
  400f02:	48 89 e6             	mov    %rsp,%rsi # 栈指针作为第二个参数
  400f05:	e8 52 05 00 00       	callq  40145c <read_six_numbers>
  400f0a:	83 3c 24 01          	cmpl   $0x1,(%rsp) # 检查是否相等
  400f0e:	74 20                	je     400f30 <phase_2+0x34> # 相等跳转
  400f10:	e8 25 05 00 00       	callq  40143a <explode_bomb>
  400f15:	eb 19                	jmp    400f30 <phase_2+0x34>
  400f17:	8b 43 fc             	mov    -0x4(%rbx),%eax # (rbx-4)赋给eax
  400f1a:	01 c0                	add    %eax,%eax
  400f1c:	39 03                	cmp    %eax,(%rbx) # 比较当前数与下一个数
  400f1e:	74 05                	je     400f25 <phase_2+0x29> # 相等跳转
  400f20:	e8 15 05 00 00       	callq  40143a <explode_bomb>
  400f25:	48 83 c3 04          	add    $0x4,%rbx
  400f29:	48 39 eb             	cmp    %rbp,%rbx # 是否比完了6个数
  400f2c:	75 e9                	jne    400f17 <phase_2+0x1b>
  400f2e:	eb 0c                	jmp    400f3c <phase_2+0x40>
  400f30:	48 8d 5c 24 04       	lea    0x4(%rsp),%rbx # 栈指针+4赋给rbx
  400f35:	48 8d 6c 24 18       	lea    0x18(%rsp),%rbp # 栈指针+24赋给rbp
  400f3a:	eb db                	jmp    400f17 <phase_2+0x1b>
  400f3c:	48 83 c4 28          	add    $0x28,%rsp
  400f40:	5b                   	pop    %rbx
  400f41:	5d                   	pop    %rbp
  400f42:	c3                   	retq
```
第一次是1，第二次是2，4，8，16，32

## phase 3
```clike
0000000000400f43 <phase_3>:
  400f43:	48 83 ec 18          	sub    $0x18,%rsp # 栈指针-24
  400f47:	48 8d 4c 24 0c       	lea    0xc(%rsp),%rcx # 栈指针+12赋给rcx
  400f4c:	48 8d 54 24 08       	lea    0x8(%rsp),%rdx # 栈指针+8赋给rdx
  400f51:	be cf 25 40 00       	mov    $0x4025cf,%esi
  400f56:	b8 00 00 00 00       	mov    $0x0,%eax
  400f5b:	e8 90 fc ff ff       	callq  400bf0 <__isoc99_sscanf@plt> # 返回值存入eax
  400f60:	83 f8 01             	cmp    $0x1,%eax
  400f63:	7f 05                	jg     400f6a <phase_3+0x27> # 大于跳转
  400f65:	e8 d0 04 00 00       	callq  40143a <explode_bomb> # 否则爆炸
  400f6a:	83 7c 24 08 07       	cmpl   $0x7,0x8(%rsp) #(rsp+8)即输入的第一个整数与7比较
  400f6f:	77 3c                	ja     400fad <phase_3+0x6a> # 大于爆炸
  400f71:	8b 44 24 08          	mov    0x8(%rsp),%eax # 输入的第一个整数赋给eax
  400f75:	ff 24 c5 70 24 40 00 	jmpq   *0x402470(,%rax,8) # 跳转8*rax+0x402470
  400f7c:	b8 cf 00 00 00       	mov    $0xcf,%eax
  400f81:	eb 3b                	jmp    400fbe <phase_3+0x7b>
  400f83:	b8 c3 02 00 00       	mov    $0x2c3,%eax
  400f88:	eb 34                	jmp    400fbe <phase_3+0x7b>
  400f8a:	b8 00 01 00 00       	mov    $0x100,%eax
  400f8f:	eb 2d                	jmp    400fbe <phase_3+0x7b>
  400f91:	b8 85 01 00 00       	mov    $0x185,%eax
  400f96:	eb 26                	jmp    400fbe <phase_3+0x7b>
  400f98:	b8 ce 00 00 00       	mov    $0xce,%eax
  400f9d:	eb 1f                	jmp    400fbe <phase_3+0x7b>
  400f9f:	b8 aa 02 00 00       	mov    $0x2aa,%eax
  400fa4:	eb 18                	jmp    400fbe <phase_3+0x7b>
  400fa6:	b8 47 01 00 00       	mov    $0x147,%eax
  400fab:	eb 11                	jmp    400fbe <phase_3+0x7b>
  400fad:	e8 88 04 00 00       	callq  40143a <explode_bomb>
  400fb2:	b8 00 00 00 00       	mov    $0x0,%eax
  400fb7:	eb 05                	jmp    400fbe <phase_3+0x7b>
  400fb9:	b8 37 01 00 00       	mov    $0x137,%eax
  400fbe:	3b 44 24 0c          	cmp    0xc(%rsp),%eax
  400fc2:	74 05                	je     400fc9 <phase_3+0x86>
  400fc4:	e8 71 04 00 00       	callq  40143a <explode_bomb>
  400fc9:	48 83 c4 18          	add    $0x18,%rsp
  400fcd:	c3                   	retq
```
400f51将地址0x4025cf赋给esi，作为第二个参数，看下这个地址有啥：为了方便，我们把前面问题的答案仍在一个文件ans.txt中，  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703164446264.png)  
输入是2个整数，第一个不能大于7，基于第一个整数（0-7）跳转......
