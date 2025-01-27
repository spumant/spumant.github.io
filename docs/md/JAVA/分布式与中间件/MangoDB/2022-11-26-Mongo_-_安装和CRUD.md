## MongoDB安装

### 参考文档

官网下载：<https://www.mongodb.com/try/download/community>

官网文档：<https://docs.mongodb.com/v3.6/administration/install-community/>

菜鸟教程：<https://www.runoob.com/mongodb/mongodb-window-install.html>

### 以Docker为例安装

1、拉取镜像

```sh
docker pull mongo:latest
```

注意：有的服务器不支持新版本，会导致Docker启动失败，具体原因看报错内容

可选择其他版本（我使用的服务器5.0及以上版本都不可使）

```sh
docker pull mongo:4.4.8
```

2、创建和启动容器

以 4.4.8 版本为例 <Badge text="建议加上密码，原因看下方"/>

<code-group>
  <code-block title="不带密码启动" active>

  ```bash
docker run -d --restart=always  --name mymongo \
	-p 27017:27017 \
	-v /data/docker/mongodb:/data/db \
	mongo:4.4.8
  ```

  </code-block>

  <code-block title="带密码启动">

  ```bash
docker run -d --restart=always --name mymongo \
	-p 27017:27017  \
    -e MONGO_INITDB_ROOT_USERNAME=mymongoadmin \
	-e MONGO_INITDB_ROOT_PASSWORD=root123456 \
	-v /data/docker/mongodb:/data/db \
    mongo:4.4.8
  ```

  </code-block>
</code-group>

使用账户密码启动的，需要给数据库创建用户权限：

```sh
db.createUser(
    {
        user:"admin",
        pwd:"root123456",
        roles:[
            {role:"dbOwner",db:"yygh_hosp"}
        ]
    }
)
```

之后sping中MongoDB地址改成：

```properties
spring.data.mongodb.uri=mongodb://admin:root123456@IP:端口/yygh_hosp
```

角色权限说明(role)：

Built-In Roles（内置角色）：

1. 数据库用户角色：read、readWrite; 
2. 数据库管理角色：dbAdmin、dbOwner、userAdmin；
3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager； 
4. 备份恢复角色：backup、restore； 
5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase 
6. 超级用户角色：root 



> 这里还有几个角色间接或直接提供了系统超级用户的访问
>
> （dbOwner 、userAdmin、userAdminAnyDatabase）



<Badge text="带密码原因：" type="error" vertical="middle"/>

之前学习 [尚硅谷-尚医通项目](https://www.bilibili.com/video/BV1V5411K7rT?p=163) 用到了MongoDB

我安装到了服务器上，没换端口地址且没设密码

后来某一天，数据库让淦了，所有数据都没了，只剩下个勒索数据：

![image-20210911115120977](https://gcore.jsdelivr.net/gh/oddfar/static/img/MongoDB/image-20210911115120977.png)

翻译后：

![image-20210911115316142](https://gcore.jsdelivr.net/gh/oddfar/static/img/MongoDB/image-20210911115316142.png)

还好只是学习的测试数据，不然就完犊子了

百度了一下参考了 [mongoDB 数据莫名其妙的没了](https://blog.csdn.net/u013513053/article/details/105785980/) 这篇文章，才知道数据库是被黑了

## 连接和建库

默认保留的数据库

- **admin**: 从权限角度考虑, 这是 `root` 数据库, 如果将一个用户添加到这个数据库, 这个用户自动继承所有数据库的权限, 一些特定的服务器端命令也只能从这个数据库运行, 比如列出所有的数据库或者关闭服务器
- **local**: 数据永远不会被复制, 可以用来存储限于本地的单台服务器的集合 (部署集群, 分片等)
- **config**: Mongo 用于分片设置时, `config` 数据库在内部使用, 用来保存分片的相关信息



进入容器 

```sh
docker exec -it mymongo /bin/bash 
```

连接mongo


```sh
mongo -u mymongoadmin -p root123456
```

基本操作：

```sh
#查看所有数据库
show dbs
#使用数据库，没有这个数据则自动创建
use testdb
```

当 `use testdb` 的时候. `testdb ` 其实存放在内存之中,

当 `testdb ` 中存在一个 document 之后, mongo 才会将这个数据库持久化到硬盘之中.

## 文档基本 CRUD

::: tip 官方文档

 https://docs.mongodb.com/manual/crud/

:::

### 插入

- 图例

`users` 为要插入的"表"

![img](https://gcore.jsdelivr.net/gh/oddfar/static/img/MongoDB/mongo-x-usage-3.png)

```sh
#向集合中添加一个文档
db.inventory.insertOne(
   { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" } }
)
#向集合中添加多个文档
db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], size: { h: 14, w: 21, uom: "cm" } },
   { item: "mat", qty: 85, tags: ["gray"], size: { h: 27.9, w: 35.5, uom: "cm" } },
   { item: "mousepad", qty: 25, tags: ["gel", "blue"], size: { h: 19, w: 22.85, uom: "cm" } }
])
```

注：当我们向 `inventory` 中插入 `document` 文档时, 如果没有给文档指定 `_id` 属性, 那么数据库会为文档自动添加 `_id` field, 并且值类型是 `ObjectId(blablabla)`, 就是文档的唯一标识, 类似于 relational database 里的 `primary key`

这个数据库工具叫 **Navicat**

> “Navicat”是一套可创建多个连接的数据库管理工具，用以方便管理 MySQL、Oracle、PostgreSQL、SQLite、SQL Server、MariaDB 和/或 MongoDB 等不同类型的数据库，并支持管理某些云数据库，例如阿里云、‎腾讯云。

![image-20210915173925342](https://gcore.jsdelivr.net/gh/oddfar/static/img/MongoDB/image-20210915173925342.png)

>- mongo 中的数字, 默认情况下是 double 类型, 如果要存整型, 必须使用函数 `NumberInt(整型数字)`, 否则取出来就有问题了
>- 插入当前日期可以使用 `new Date()`

如果某条数据插入失败, 将会终止插入, 但已经插入成功的数据**不会回滚掉**. 因为批量插入由于数据较多容易出现失败, 因此, 可以使用 `try catch` 进行异常捕捉处理, 测试的时候可以不处理.如：

```sh
try {
  db.comment.insertMany([
    {"_id":"1","articleid":"100001","content":"我们不应该把清晨浪费在手机上, 健康很重要, 一杯温水幸福你我 他.","userid":"1002","nickname":"相忘于江湖","createdatetime":new Date("2019-0805T22:08:15.522Z"),"likenum":NumberInt(1000),"state":"1"},
    {"_id":"2","articleid":"100001","content":"我夏天空腹喝凉开水, 冬天喝温开水","userid":"1005","nickname":"伊人憔 悴","createdatetime":new Date("2019-08-05T23:58:51.485Z"),"likenum":NumberInt(888),"state":"1"},
    {"_id":"3","articleid":"100001","content":"我一直喝凉开水, 冬天夏天都喝.","userid":"1004","nickname":"杰克船 长","createdatetime":new Date("2019-08-06T01:05:06.321Z"),"likenum":NumberInt(666),"state":"1"},
    {"_id":"4","articleid":"100001","content":"专家说不能空腹吃饭, 影响健康.","userid":"1003","nickname":"凯 撒","createdatetime":new Date("2019-08-06T08:18:35.288Z"),"likenum":NumberInt(2000),"state":"1"},
    {"_id":"5","articleid":"100001","content":"研究表明, 刚烧开的水千万不能喝, 因为烫 嘴.","userid":"1003","nickname":"凯撒","createdatetime":new Date("2019-0806T11:01:02.521Z"),"likenum":NumberInt(3000),"state":"1"}

]);

} catch (e) {
  print (e);
}
```

### 查询

![img](https://gcore.jsdelivr.net/gh/oddfar/static/img/MongoDB/mongo-x-usage-4.png)

- 使用 `db.<collection_name>.find()` 方法对集合进行查询, 接受一个 json 格式的查询条件. 返回的是一个**数组**
- `db.<collection_name>.findOne()` 查询集合中符合条件的第一个文档, 返回的是一个**对象**

查询全部数据：

```sh
db.inventory.find()
```

- 数据准备：

先删除之前的数据：

```sh
db.comment.deleteMany({})
```

我们先往 comment 插入一些数据：

```sh
db.comment.insertMany([
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" }
]);
```

- 可以使用 `$in` 操作符表示：查看状态是 A 和 D 的

```sh
db.comment.find( { status: { $in: [ "A", "D" ] } } )
```

- 多个查询条件用逗号分隔, 表示 `AND` 的关系

```
db.comment.find( { status: "A", qty: { $lt: 30 } } )
```

等价于sql : `SELECT * FROM comment WHERE status = "A" AND qty < 30`

- 使用 `$or` 操作符表示后边数组中的条件是OR的关系

```sh
db.comment.find( { $or: [ { status: "A" }, { qty: { $lt: 30 } } ] } )
```

等价于sql : `SELECT * FROM inventory WHERE status = "A" OR qty < 30`

- 可以使用 `pretty()` 来帮助阅读

```sh
db.comment.find().pretty()
```

创建索引

```sh
db.posts.createIndex({
  { title : 'text' }
})

// 文本搜索
// will return document with title "Post One"
// if there is no more posts created
db.posts.find({
  $text : {
    $search : "\"Post O\""
  }
}).pretty()

```





### 更新

![img](https://gcore.jsdelivr.net/gh/oddfar/static/img/MongoDB/mongo-x-usage-5.png)



- 使用 `db.<collection_name>.updateOne(<filter>, <update>, <options>)` 方法修改一个匹配 `<filter>` 条件的文档
- 使用 `db.<collection_name>.updateMany(<filter>, <update>, <options>)` 方法修改所有匹配 `<filter>` 条件的文档
- 使用 `db.<collection_name>.replaceOne(<filter>, <update>, <options>)` 方法**替换**一个匹配 `<filter>` 条件的文档
- `db.<collection_name>.update(查询对象, 新对象)` 默认情况下会使用新对象替换旧对象

其中 `<filter>` 参数与查询方法中的条件参数用法一致.

如果需要修改指定的属性, 而不是替换需要用“修改操作符”来进行修改

- `$set` 修改文档中的制定属性

其中最常用的修改操作符即为`$set`和`$unset`,分别表示**赋值**和**取消赋值**.



先加入一些数据：

```sh
db.inventory.insertMany( [
   { item: "canvas", qty: 100, size: { h: 28, w: 35.5, uom: "cm" }, status: "A" },
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "mat", qty: 85, size: { h: 27.9, w: 35.5, uom: "cm" }, status: "A" },
   { item: "mousepad", qty: 25, size: { h: 19, w: 22.85, uom: "cm" }, status: "P" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "P" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" },
   { item: "sketchbook", qty: 80, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "sketch pad", qty: 95, size: { h: 22.85, w: 30.5, uom: "cm" }, status: "A" }
] );
```

更新数据：

```sh
db.inventory.updateOne(
    { item: "paper" },
    {
        $set: { "size.uom": "cm", status: "P" },
        $currentDate: { lastModified: true }
    }
)

db.inventory.updateMany(
    { qty: { $lt: 50 } },
    {
        $set: { "size.uom": "in", status: "P" },
        $currentDate: { lastModified: true }
    }
)
```

`db.<collection_name>.replaceOne()` 方法替换除 `_id` 属性外的**所有属性**, 其`<update>`参数应为一个**全新的文档**.



```sh
db.inventory.replaceOne(
    { item: "paper" },
    { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 40 } ] }
)
```



> 修改前：
> { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" }
>
> 修改完后：
> {  "item" : "paper", "instock" : [ { "warehouse" : "A", "qty" : 60 }, { "warehouse" : "B", "qty" : 40 } ] }

**批量修改**

```sh
// 默认会修改第一条
db.document.update({ userid: "30", { $set {username: "guest"} } })

// 修改所有符合条件的数据
db.document.update( { userid: "30", { $set {username: "guest"} } }, {multi: true} )
```

### 删除

- 使用 `db.collection.deleteMany()` 方法删除所有匹配的文档.
- 使用 `db.collection.deleteOne()` 方法删除单个匹配的文档.
- `db.collection.drop()`
- `db.dropDatabase()`

```sh
db.inventory.deleteMany( { qty : { $lt : 50 } } )
```

> Delete operations **do not drop indexes**, even if deleting all documents from a collection.
>
> 一般数据库中的数据都不会真正意义上的删除, 会添加一个字段, 用来表示这个数据是否被删除



## 参考文章

- https://zhenye-na.github.io/2020/01/27/intro-to-mongodb.html
- https://www.pdai.tech/md/db/nosql-mongo/mongo-x-usage-1.html

