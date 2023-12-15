---
title: Flow Control & Congestion Control
url: flow-and-congestion-control
date: 2021-02-25 17:43:00
description: 流量控制和拥塞控制
categories: Computer Science
tags: [Network]
---

In order to make sure that every packet reaches its destination, we use Retransmit. There are 3 approaches: Stop-and-Wait, Go Back N and Selective Repeat.

 - Stop-and-Wait  
Sender transmits packet one by one, label each with a sequence number and sets timer after transmitting. If receive ACK, send next. If timer goes off, resend the previous packet.  
When receive packet, send ACK. If packet is corrupted, ignore it and sender will resend.
 - SR  
Send packets from the window and set timeout for each packet. On receiving ACK for left side of the window, slide forward and send packets that have now entered the window. On timeout, resend only the timed out packet.  
Receiver keeps a buffer of size of the window. On receiving packets, send ACK. If packet comes in out of order, just store it in the buffer and send ACK anyway.

## How big should we size the sender's window
Don't overload the receiver. Sender cannot send as fast as possible since it will overflow the receiver's buffer.   
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200417151819468.png)  
The solution is Advertised Window (W): tell the sender how much space the receiver's buffer has through ACK. So the window size of the sender: the size <= W. Thus we won't overload the receiver. This is Flow Control.

But if we set the size to W, we cannot solve the problem thoroughly:  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022516554078.png)  
Sender's window contains a set of packets that have been transmitted but not yet acked. But some packets will get dropped at router and sender will never receive ACKs for these packets. The result is these packets will remain buffered in the window. It means that we cannot set the size to W, we only want to send at 50Mbps. It will take a RTT(200ms) to receive an ACK back for the first packet. We will send 50*200=1.25MB data and that's exactly the definition of the sender's window.

The window size of the sender should <= bandwidth-delay product (BDP). Thus we won't overload the network. This is Congestion Control. BDP is the "volume" of the link, the amount of data that can be "in flight" at any time.

## How should we determine the BDP
Things are much harder to calculate the BDP:

 - We don't know the bandwidth or RTT
 - My share of bandwidth is dependent on the other user on the network, so the window size will change as other users start or stop sending
 - The router will stall the excess packets in the bottleneck queue instead of dropping, so you can overshoot the size a little bit 

There are many algorithms to solve the problem given the prior constraints. The old one is Reno, although no one uses it anymore, sigh!!

Use Multiplicative Increase at startup to find the right sending rate quickly, this process is called "slow start";  
Then uses Additive Increase/Multiplicative Decrease (AIMD) to adjust the sending rate over time.  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210225174103869.png)

## Reference
[CMU 15-441 TCP Part 2](https://computer-networks.github.io/sp19/lectures.html)