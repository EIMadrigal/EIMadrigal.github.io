---
title: Mobile Communication
url: mobile-communication
date: 2019-04-03 17:12:00
description: 移动通信
categories: Math
tags: [Information Engineering]
---

最近面试有被问到LTE，感觉说得不太清楚，重新整理一遍。

## 第一代移动通信系统
1G，诞生于1980年左右。**模拟通信系统**，抗干扰性能差，使用FDMA技术，主要用来传输**话音**信号，最拉风的就是“大哥大”。  
采用蜂窝组网，又叫小区制移动通信。将网络服务区划分为许多cell，每个cell设置一个基站，移动站的发送和接收都要通过基站进行。  
1G的制式有很多种：瑞士、荷兰、俄罗斯等使用的NMT，美国、澳大利亚使用的AMPS，英国使用的TACS等。

## 第二代移动通信系统
2G是**数字通信系统**，因此抗干扰能力大大增强。同时引入了CDMA和TDMA技术，提供了**低速数字通信**（短信）服务。  
2G的制式主要是欧洲的GSM(Global System for Mobile Communication)。  
不久，2G就演变为了支持**数据服务**的2.5G（能上网）。2.5G包括了GPRS(General Packet Radio Service)和EDGE(Enhanced Data rate for GSM Evolution)。

## 第三代移动通信系统
3G主要采用了CDMA技术，使用混合的交换机制（电路交换、分组交换），可以提供丰富的多媒体服务（话音、数据、视频等）。  
3G的标准主要有三种：欧洲的WCDMA（中国联通采用）、美国的CDMA2000（中国电信使用）、中国的TD-SCDMA（中国移动使用）。  
制定标准的过程也是利益冲突与妥协的过程，欧洲这边成立了3GPP组织，美国主导了3GPP2组织，中国当然是加入了3GPP。

## 第四代移动通信系统
国际电信联盟（ITU）提出了4G的需求，4G的大名是IMT-Advanced(International Mobile Telecommunications-Advanced)。  
4G标准的制定者，一个是3GPP，一个是IEEE。  
3GPP提出了LTE(Long-Term Evolution)和LTE-Advanced，IEEE提出了WirelessMAN-Advanced。  
LTE是3G和4G之间的过渡技术，也被称为3.9G。由于高通放弃了3GPP2转而投向LTE的怀抱，所以LTE应用得很广泛。

LTE相比于2G/3G频率变高、频段变宽，根据香农定理，提高频谱带宽或者信噪比可以提高信道容量，但是无线通信中要提升信噪比是比较困难的。高频段频谱资源丰富，相对低频段来说比较纯净，干扰较小，可分配的带宽比较大，同频段的连续频谱进行载波聚合也比较容易。

缺点的话：高频段路径损耗大、绕射能力差，因此覆盖距离和覆盖深度都不如低频段，当然频段过低可能造成越区覆盖，进而信噪比恶化，切换失败导致掉话。因此需要频段适中来组网。

EPC(Evolved Packet Core)：[演进的分组核心网](https://www.zhihu.com/question/22365275/answer/21805286)是4G的核心网，

PTN(Packet Transport Network)：3G/4G的分组传送网，用于传输IP包和以太网帧，

数据通信网络：

## 部分部门内网禁止访问互联网
![网络拓扑结构](https://img-blog.csdnimg.cn/b40f033d64a74b91bb7c24677814398a.png)
假设一台PC机为普通部门，可以接入外网；另一台PC机为涉密部门，不能接入外网。两台PC机与三层交换机连接，三层交换机与路由器1连接，路由器通过PTN传输与能连接公网的路由器2连接。  
可接入外网的PC机在三层交换机当中有路由表，可以通过PTN传输与公网连接，禁止接入外网的PC机无法查到路由表，不能与公网连接。  
配置三层交换机与两个路由器。路由器通过PTN传输与另一台能访问公网的路由器相连接。

同一部门位于相同的子网段设置为同一VLAN互通，不同的部门位于不同的网段通过三层交换机的路由功能，实现不同VLAN间互通。

在配置三层交换机与路由器R1的OSPF协议时，私密部门的网段不加入路由协议中。
