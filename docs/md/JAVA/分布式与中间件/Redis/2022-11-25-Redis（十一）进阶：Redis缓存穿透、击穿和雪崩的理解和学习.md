## Redis（十一）进阶：Redis缓存穿透、击穿和雪崩的理解和学习

## 前言

## 一、Redis的缓存穿透

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126195904746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
1、**概念：**  
用户需要查询一个数据，但是redis中没有（比如说mysql中id=-1的数），直接去请求MySQL，当很多用户同时请求并且都么有命中！于是都去请求了持久层的数据库，那么这样会给持久层数据库带来非常大的压力。一般出现这样的情况都不是正常用户，基本上都是恶意用户！  
2、**解决方案**  
①**布隆过滤器：**  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126201036751.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行校验，不符合则  
丢弃，从而避免了对底层存储系统的查询压力；  
因为篇幅原因，在此不展开讲，后面会出单独的文章来仔细讲解布隆过滤器！  
②**缓存空对象：**  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126200926925.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
当存储层查不到，即使是空值，我们也将其存储起来并且在Redis中设置一个过期时间，之后再访问这个数据将会从Redis中访问，保护了持久层的数据库！  
③**存在的问题：**  
1）如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多  
的空值的键；  
2）即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于  
需要保持一致性的业务会有影响。  
**注意：缓存穿透前提是：Redis和MySQL中都没有，然后不停的直接请求MySQL。**

## 二、Redis的缓存击穿

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126201412391.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
1、**概念：**  
是指一个非常热点的key，在不停的扛着大并发，当这个key失效时，一瞬间大量的请求冲到持久层的数据库中，就像在一堵墙上某个点凿开了一个洞！  
2、**解决方案：**  
①**设置热点key永不过期：**  
从缓存层面来看，没有设置过期时间，所以不会出现热点 key 过期后产生的问题。  
②**加互斥锁：**  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126201645155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
在查询持久层数据库时，保证了只有一个线程能够进行持久层数据查询，其他的线程让它睡眠几百毫秒，等待第一个线程查询完会回写到Redis缓存当中，剩下的线程可以正常查询Redis缓存，就不存在大量请求去冲击持久层数据库了！  
③**缺点：**  
其实设置**永不过期**不合理！

## 三、Redis的缓存雪崩

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126201940972.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
1、**概念：**  
在某一个时间段，缓存的key大量集中同时过期了，所有的请求全部冲到持久层数据库上，导致持久层数据库挂掉！  
范例：双十一零点抢购，这波商品比较集中的放在缓存，设置了失效时间为1个小时，那么到了零点，这批缓存全部失效了，而大量的请求过来时，全部冲过了缓存，冲到了持久层数据库！  
2、**解决方案：**

①**Redis高可用：**  
搭建Redis集群，既然redis有可能挂掉，那我多增设几台redis，这样一台挂掉之后其他的还可以继续工作，其实就是搭建的集群。（异地多活！）  
②**限流降级：**  
在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对  
某个key只允许一个线程查询数据和写缓存，其他线程等待。  
③**数据预热：**  
数据加热的含义就是在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中。在即将发生大并发访问前手动触发加载缓存不同的key，设置不同的过期时间，**让缓存失效的时间点尽量均匀** 。

Redis扩展：[Redis学习汇总](https://blog.csdn.net/weixin_43829443/article/details/112839985)

***路漫漫其修远兮，吾必将上下求索~***  
到此关于Redis缓存穿透、击穿和雪崩的理解的讲解就算告一段落了，如果你认为博主写的不错！写作不易，请点赞、关注、评论给博主一个鼓励吧\*\*转载请注明出处哦\*\*