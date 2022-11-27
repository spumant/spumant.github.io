## Redis（四）整合：Redis在Jedis中如何使用和操作？

## 前言

百度的概念性解答我就不贴了，简单来说，**Jedis是Redis官方推荐的Java连接开发工具！** 虽然现在的SpringBoot2.×版本已经将Jedis换成了Lettuce，但是我觉得还是有必要了解一下Jedis的使用！

1.  如何在java项目中整合Jedis并且连接Redis数据库？  
    ①创建一个Maven项目  
    空的即可~怎么创建我就不赘述了！![在这里插入图片描述](https://img-blog.csdnimg.cn/20210120215705544.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
    ②导入Jedis和fastjson依赖，耐心等待下载完成！

```auto
<!--导入jedis的包-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.2.0</version>
</dependency> 
<!--fastjson-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.62</version>
 </dependency>
```

③连接Redis测试，此处为了方便测试，连接的是本地的Redis服务，连接远程需要更改配置文件和关闭防火墙，以后会单独弄一篇文章来介绍这个！

```java
// 1、 new Jedis 对象即可
Jedis jedis = new Jedis("127.0.0.1",6379);
// jedis 所有的命令就是我们之前的所有指令
System.out.println(jedis.ping());
```

如图所示：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210120223021388.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
返回PONG,证明连接成功！

④常用的API练习：

```java
 		// 1、 new Jedis 对象即可
        Jedis jedis = new Jedis("127.0.0.1",6379);
        jedis.flushDB();//清空当前库的所有数据
        jedis.set("name","dingyongjun");
        jedis.set("age","23");
        jedis.set("high","173");
        System.out.println("name:"+jedis.get("name")+"\nage:"+jedis.get("age")+"\nhigh"+jedis.get("high"));
```

如图所示：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210120223918886.png)

```java
		jedis.lpush("list","1","2","3","4");
        System.out.println("list: "+jedis.lrange("list",0,-1));
```

如图所示：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210120224445882.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)

⑤总结：在Jedis中连接使用Redis，和Redis控制台命令完全一致，我就不一个个命令再去重复写一遍了，后面如果有时间的话，我会把所有的命令给慢慢完善上来！这算是一篇未完待续吧。。。

Redis扩展：[Redis学习汇总](https://blog.csdn.net/weixin_43829443/article/details/112839985)

***路漫漫其修远兮，吾必将上下求索~***  
到此关于Redis在Jedis中如何使用和操作的讲解就算告一段落了，如果你认为i博主写的不错！写作不易，请点赞、关注、评论给博主一个鼓励吧\*\*转载请注明出处哦\*\*