# Spring Session
引用 ： http://mp.weixin.qq.com/s?__biz=MzI5MDEzMzg5Nw==&mid=2660393171&idx=1&sn=04bf46635e2c58993fe5c77ac5c8192e&scene=1&srcid=0829Pza9Yc3NnB9clsh464P6##

---
## 应用架构
### 单体架构
![单体架构应用][1]
单体架构中，Session管理方案是在用户进行登录的时候将Session存放在应用服务器的内存中，由于只有一个应用服务器节点，用户的所有请求都是这个唯一节点进行响应处理，所以能够轻松的达到保持用户状态的目的。

### 分布式架构
![分布式架构应用][2]
在分布式架构中，必须保证用户在一个服务器节点进行登陆后，不仅该节点需要在内存中保存用户Session，还需要让其他应用服务器节点也能共享到用户Session。

分布式架构进行Session共享的常用方案有如下几种：

（1）存放在Cookie中
当用户Session中需要存放的数据很小的时候，可以选择将Session对象存放在浏览器的Cookie中来实现Session共享。该方案实现方便，但是由于Cookie的存储容量不大，所以只适用于Session数据量小的场景。

（2）Session复制
部分Web服务器能够支持Session复制功能，例如Tomcat。用户可以通过修改Web服务器的配置文件，让Web服务器进行Session复制，保持每一个服务器节点的Session数据都能达到一致。

但是这种方案的实现依赖于Web服务器，需要Web服务器有Session复制功能。当Web应用中Session数量较多的时候，每个服务器节点都需要有一部分内存用来存放Session，将会占用大量内存资源。同时大量的Session对象通过网络传输进行复制，不但占用了网络资源，还会因为复制同步出现延迟，导致程序运行错误。

（3）Session粘滞
Session粘滞是通过负载均衡器，将同一用户的请求都分发到固定的一个服务器节点上，让固定服务器进行响应处理，如此一来，只需要这个节点上保存了用户Session，即可保持用户的状态一致性。

但是Session粘滞方案依赖于负载均衡器，而且只能满足水平扩展的集群场景，无法满足进行应用分割后的分布式场景。

### 微服务架构
![微应用][3]
在微服务架构下，对Session的管理应该另辟蹊径，不再将Session对象保存在Web服务器内存中，而是在应用服务器架构中引入独立的中间存储介质，将企业应用中的Session对象进行统一管理。

一个好的Session集中管理方案应该具备以下特点：

 A、中间存储介质的读写速度要快。之前的Session管理方案将Session对象存放在服务器内存中，有着很高的读写速度，进行Session集中管理后将会在Session读写中引入网络传输，速度会有所降低，所以必须保证中间存储介质的读写速度。

B、中间存储介质要保证高可用。进行Session集中管理后，整个企业应用的Session都会存放在中间存储介质中，如果存储介质是不稳定的，那整个企业应用都将不稳定。

C、对Session的使用者来说，Session管理方案应该是透明的，切换成集中管理方案后用户无感知。

D、Session管理方案不该和某一Web服务器耦合，应该适用于所有常规Web服务器。
## 微服务架构下分布式Session管理
### 存储介质
| --- | 数据一致性 | 设置10万个值用时 | 读取10万个值用时 |
| --- | ---------- | ---------------- | ---------------- |
| Redis | 使用单线程模型，保证了数据提交顺序 | 16.8817s | 17.3401s |
| Memcache |       需要使用CAS保持一致性     | 15.257s | 15.6892s |

| --- | 支持的数据类型 | 持久性 | 分布式存储 |
| --- | ---------- | ---------------- | ---------------- |
| Redis | 支持的类型丰富，支持set和list | 支持 | 可以使用一致性Hash做分布式 |
| Memcache |支持简单类型,需要客户端自己处理复杂对象 |不支持|支持Master-Slave模式 |
![Redis方案][4]


  [1]: http://mmbiz.qpic.cn/mmbiz_jpg/icQbWvrFMeJVFwIuYH0dicz9BCRIASbzLosBKSUd5eDibfYiaJTnpOsiaGovgIguGcNkDmrdEBOTnXib6HWvf7RzMHKQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1
  [2]: http://mmbiz.qpic.cn/mmbiz_jpg/icQbWvrFMeJVFwIuYH0dicz9BCRIASbzLoa2X1MDsC2Q264OGGwBB3bWhhAWFctSu2PsR5XIYfO3WBVq8VTbibJNQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1
  [3]: http://mmbiz.qpic.cn/mmbiz_jpg/icQbWvrFMeJVFwIuYH0dicz9BCRIASbzLoX2wnVSRP0BX4T5uXbH3bcp8P5yFXxoPicMd3vFAENTBmOdu6IE5nT0w/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1
  [4]: http://mmbiz.qpic.cn/mmbiz_jpg/icQbWvrFMeJVFwIuYH0dicz9BCRIASbzLovgEGZLO075aGGiawpjutEJHlvfwCnZRPwJtaNjCTHXWBKM1vgWUUaAA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1
