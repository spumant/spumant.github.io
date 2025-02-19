# 从零开始用Jenkins自动构建部署SpringBoot项目

## 前提条件

- 一台服务器

  建议配置2h4g以上（1h2g的属实有点低了）

- sspringboot项目手动用maven打包，且jar包可正常运行

  如果你的是多模块项目（如图），需要配置好打包，否则启动失败

  ![image-20210916125322578](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210916125322578.png)

  就像这样的项目，可浏览文章：[SpringBoot多模块打包方式](https://blog.csdn.net/A_zhiyuan/article/details/120326653)

  

## 安装Jenkins

安装教程可看视频：https://www.bilibili.com/video/BV1wA411c7aS



**1、安装**

```sh
docker pull jenkins/jenkins:lts
```

**2、创建目录**

```sh
[root@localhost data]# mkdir jenkins_home
[root@localhost data]# ls
docker  jenkins_home
```

**3、启动**

```sh
docker run -di --name=jenkins -p 8080:8080 -v /data/jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

启动失败：

```sh
[root@localhost data]# docker logs  d6b5baf89d6b
touch: cannot touch '/var/jenkins_home/copy_reference_file.log': Permission denied
Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?
touch: cannot touch '/var/jenkins_home/copy_reference_file.log': Permission denied
Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?
```

权限：

```sh
chown -R 1000 /data/jenkins_home
```

再重新启动，等启动工作完毕后

![image-20210914190950830](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914190950830.png)



查看容器日志：

```sh
docker logs jenkins
```

![image-20210914191244184](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914191244184.png)

箭头所指就是密码

> 方法二：

```sh
[root@localhost data]# cd jenkins_home/secrets/
[root@localhost secrets]# ll
总用量 20
drwxr-xr-x 2 www www  29 9月  14 18:45 filepath-filters.d
-rw-r----- 1 www www  33 9月  14 18:45 initialAdminPassword
-rw-r--r-- 1 www www  32 9月  14 18:45 jenkins.model.Jenkins.crumbSalt
-rw-r--r-- 1 www www 256 9月  14 18:45 master.key
-rw-r--r-- 1 www www 272 9月  14 18:45 org.jenkinsci.main.modules.instance_identity.InstanceIdentity.KEY
-rw-r--r-- 1 www www   5 9月  14 18:45 slave-to-master-security-kill-switch
drwxr-xr-x 2 www www  26 9月  14 18:45 whitelisted-callables.d
[root@localhost secrets]# cat initialAdminPassword
730edbb8adcd4ae7b58f14ac3ccd001d
```

Jenkins修改初始密码:<https://www.pianshen.com/article/7582333512/>

**5、进入页面后，选择推荐的插件**

![image-20210914191628179](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914191628179.png)

全部安装成功后：

![image-20210914191921616](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914191921616.png)

有一些会安装失败（我用的服务器，用虚拟机的由于有墙可能访问不到github）

![image-20210914192005987](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914192005987.png)

点击重试后

![image-20210914192029111](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914192029111.png)

大部分都装上了，有几个可能会装不上，到时候可以手动安装，先点确定

然后创建用户

![image-20210914192137056](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914192137056.png)

下一步，URL为，jenkins的IP:端口

![image-20210914192206581](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914192206581.png)

## 插件安装



![image-20210914192444240](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914192444240.png)

搜索插件 ssh（红色代表与jenkins版本不匹配）

![image-20210914192559032](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914192559032.png)

安装失败重新安装即可！
![image-20210914192746394](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914192746394.png)

第一行表示之前的安装的日志，等图标变成蓝色代表安装成功

## 安装jdk

全局工具配置

![image-20210914194021666](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914194021666.png)

点新增JDK，选择版本，登录下oracle账号：2696671285@qq.com Oracle123

账号为up主提供的：https://www.bilibili.com/video/BV1wA411c7aS

![image-20210914194420749](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914194420749.png)

之后安装maven（如果你的项目不是maven则安装别的插件）

## 安装maven

点击保存

![image-20210914195019228](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914195019228.png)



## 配置ssh



![image-20210914195127408](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914195127408.png)

搜索 ssh

![image-20210914195210660](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914195210660.png)

添加ssh的ip和端口 ， 点击添加

![image-20210914200115406](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914200115406.png)

添加服务器的账号和密码

![image-20210914200334869](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914200334869.png)

检测连接是否成功

![image-20210914201739436](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210914201739436.png)







## 构建项目

**1、新建项目**

![image-20210915083151955](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915083151955.png)

**2、输入任务名称，选择maven**

没有这个项目则下载maven插件（百度即可）

[Maven Integration plugin](https://plugins.jenkins.io/maven-plugin)

![image-20210915083215160](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915083215160.png)

**3、设置源码管理**

![image-20210915083258091](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915083258091.png)

记得添加你的gitee账号密码的凭据

**4、设置Maven**



![image-20210915094404192](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915094404192.png)

设置

```sh
clean package
```

![image-20210915083519329](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915083519329.png)

然后我们先构建下，等待结束

![image-20210915083658318](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915083658318.png)

但是maven下载依赖速度太慢，我们可以手动关闭构建，把镜像配置为阿里云镜像

**5、修改maven配置**

配置文件在你的jenkins数据目录下，例如我的就是：

`/data/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation`

按照原先方法修改配置文件，把maven远程仓库地址改成阿里云的，这步就不用详细说了吧。

**6、重新构建**

点击 立即构建

这次下载速度就快了，构建好的jar包目录在：

`/data/jenkins_home/workspace/jekins-test/service/target`

jekins-test：是任务名称（我创建任务时，少打了个n）

选中查看控制台输出

![image-20210915084209903](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915084209903.png)

根据输出就能看到jar的生成目录

![image-20210915004357100](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915004357100.png)



然后我们启动看看是否正常

```sh
cd /data/jenkins_home/workspace/jekins-test/service/target
nohup java -jar service-0.0.1-SNAPSHOT.jar > /data/journalism/logs.txt &
```



## 构建后自动部署

8160是我的springboot服务端口

```sh
netstat  -nlp|grep 8160
```

强制杀死某个端口进程命令，关闭jar包

```sh
sudo fuser -k -n tcp 8160
```

请先把刚才开启的jar包服务关掉



设置**构建环境**配置，构建后操作（构建前就不用设置，测试过，填了就出现错误）

![image-20210915131245249](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915131245249.png)



## Git提交后自动构建

**1、配置URL构建触发器**

令牌随便输，URL格式就是下面那个英文说明，根据自己的修改

![image-20210915101109152](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915101109152.png)

**2、配置Gitee的WebHooks**

我这里用的Gitee（学校网进GitHub很不稳定，就选择Gitee了）

Gitee项目里设置URL和秘钥

为了安全自己写个服务用来访问触发Jenkins构建的URL，不直接输入之前的URL

例如：我用的php，代码在下方

![image-20210915101405242](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915101405242.png)

**3、添加用户token**

![image-20210915114229936](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915114229936.png)

生成，保存token信息

![image-20210915114258223](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915114258223.png)

修改url，例如我的，根据自己规则设置

`http://用户名:token@ip:端口/job/jekins-test/build?token=设置的token`

详情访问：[通过URL触发Jenkins构建](https://www.cnblogs.com/tyrionyang/p/8183819.html)

php代码如下（修改URL和秘钥）：

```php
<?php

//接收头信息
 function em_getallheaders()
{
   foreach ($_SERVER as $name => $value)
   {
       if (substr($name, 0, 5) == 'HTTP_')
       {
           $headers[str_replace(' ', '-', ucwords(strtolower(str_replace('_', ' ', substr($name, 5)))))] = $value;
       }
   }
   return $headers;
}

//发送请求
function getSslPage($url) {
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
    curl_setopt($ch, CURLOPT_HEADER, false);
    curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_REFERER, $url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
    $result = curl_exec($ch);
    curl_close($ch);
    return $result;
}

$url = "你的URL";

$headers = em_getallheaders();
//是否等于Gitee设置的秘钥
if($headers['X-Gitee-Token'] == "你设置的秘钥"){
    //调用
    getSslPage($url);
    echo "ok";
}else{
    echo "no";
}

print_r($headers['X-Gitee-Token']) ;
```

**4、完成测试**

更改代码，push到Gitee

![image-20210915132358946](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915132358946.png)

Gitee请求成功

![image-20210915132137715](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915132137715.png)

jenkins构建成功

![image-20210915132258799](https://gcore.jsdelivr.net/gh/oddfar/static/img/jenkins/image-20210915132258799.png)