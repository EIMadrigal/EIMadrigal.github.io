---
title: Design Twitter
url: design-twitter
date: 2023-10-21 23:24:00
description: System Design Interview
categories: Computer Science
tags: [System]
---

## Background

Twitter是一个社交网站，用户可以进行post tweet/timeline/follow等多种操作，国内的类似产品有微博、小红书等。

## Clarify Requirements

首先需要和面试官讨论系统的requirements，并且确定重点要focus的几个feature。

requirements通常包括functional req和non-functional req。functional req指的是常规功能/业务逻辑，例如create/delete tweet，home/user timeline，follow user, like/dislike tweet, search tweet等。non-functional req主要指以下几点：

- Consistency: 每次read都要读到most recent write也就是最新的内容。
- Availability: 每次request都能够得到non-error的响应，但无需保证读到most recent write。关键在于scalability，就是说traffic不断增加时如何保证响应。
- Partition tolerance(Fault tolerance): 节点间通信失败时不影响整个系统的工作。

上面3点其实也就是分布式系统的CAP理论。

## Capacity Estimation

明确requirements后就需要对系统承载的traffic size进行讨论和估计。假设Twitter的traffic size如下：

- 200 million DAU(Daily Active User), 100 million new tweets
- each user: visit home timeline 5 times, visit user timeline 3 times
- each timeline/page has 20 tweets
- each tweet is 280B(140 characters), metadata 30B.
- each image is 200KB, 20% tweets have image
- each video is 2MB, 10% tweets have video, 30% video will be watched

首先估算一下Storage，每天写进来的数据主要有：

- text：100M*(280+30)B, 31GB/day
- image: 100M\*20%\*200KB, 4TB/day
- video: 100M\*10%\*2MB, 20TB/day

因此总共的存储大约24TB。

再来看下bandwidth，用户daily read的量是200M\*(5+3)\*20，大概是32B条tweet。所以daily read bandwidth：

- text：32B*(280+30)B/86400, 110MB/s
- image: 32B\*20%\*200KB/86400, 14GB/s
- video: 32B\*10%\*30%\*2MB/86400, 20GB/s

所以总带宽大概35GB/s.

## System APIs

根据之前确定的feature定义API：

```java
postTweet(userToken, tweet)
deleteTweet(userToken, tweetId)
likeOrUnlikeTweet(userToken, tweetId, bool like)
// pageToken指读第几页, 默认最新页
readHomeTimeline(userToken, int pageSize, opt pageToken)
readUserTimeline(userToken, int userId, int pageSize, opt pageToken)
```

## High-level System Design

可以先从简单的feature开始设计整个系统的数据流向，如从post tweet开始：客户端的请求首先打到Load Balancer(LB)，然后打到writer服务器，负责将tweet写到DB和cache。

访问user timeline的feature: 客户端请求过LB后，打到timeline service服务器，负责读取某个用户A的主页。最直接的方式就是service去DB查询A最近的tweets返回给用户，问题在于这个请求的latency要很低(~200ms)，所以读DB就太慢了。所以加速方案就是把用户最近的tweets写到cache(post tweet时候写就可以)，然后从cache读。

最复杂的就是访问home timeline, 因为涉及到不同用户tweet的merge。客户端请求过LB打到timeline service服务器后，最直接的方式是service去DB查该用户关注的人(N个)，再查这些人最近的tweets，然后merge起来返回用户。同样的问题：太慢了，要读N次DB。

所以可以把每个用户的home timeline存到cache，那么用户A发推时，writer service会从DB找出A的follower，然后更新这些follower在cache里的home timeline，就是把A发的推插入到每个follower的home timeline，这就是所谓的fan out on write。这样的好处在于用户并不care系统write花的这些时间，只要不影响访问home timeline的速度就行。一般是通过async task更新每个follower的home timeline，所以每个follower被更新的延迟不同，就可能导致展示latest tweet时会有delay，不过用户对这个并不敏感，延迟几十秒才能看到最新的tweet并没有什么问题，最终都会看到的，这就是所谓的eventual consistency。

那么fan out on write解决了read latency的问题，带来了什么新问题呢？显然这对于大V很不友好，因为要更新几百万follower的home timeline还是很有压力的，何况还有很多僵尸粉，你更新了人家也不看。

因此可以采用一种hybrid solution，对于普通用户发推仍然采用fan out on write的方式，大V发推时并不进行fan out on write，而是当关注者刷新home timeline时再去fetch这些大V的tweets(fan in on read)，然后和普通用户的tweets合并在一起返回给用户。

## Data Storage

DB首先要有User表，包括userId, name, email, createTime, lastLogin, isHotUser等字段。还要有Tweet表，包括tweetId, userId, createTime, content等。另外，还要有Follower表，包括userId1, userId2。

像User表这种结构化数据可以用SQL DB，对于timeline cache这种非结构化数据可以用NoSQL，image/video等多媒体文件可以用file system来存。

## Scalability

当用户数量越来越多，系统怎么scale up？基本就是做data sharding，LB以及cache。

## Reference

[How to Design Twitter](https://www.youtube.com/watch?v=PMCdWr6ejpw)