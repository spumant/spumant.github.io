<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [什么是 DockerFile](#%E4%BB%80%E4%B9%88%E6%98%AF-dockerfile)
- [DockerFile 构建过程](#dockerfile-%E6%9E%84%E5%BB%BA%E8%BF%87%E7%A8%8B)
- [DockerFile 指令](#dockerfile-%E6%8C%87%E4%BB%A4)
- [CMD 和 ENTRYPOINT 的区别](#cmd-%E5%92%8C-entrypoint-%E7%9A%84%E5%8C%BA%E5%88%AB)
- [实战测试](#%E5%AE%9E%E6%88%98%E6%B5%8B%E8%AF%95)
  - [CentOS](#centos)
  - [Tomcat](#tomcat)
  - [SpingBoot](#spingboot)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



大家想想，Nginx，tomcat，mysql 这些镜像都是哪里来的？官方能写，我们不能写吗？

我们要研究自己如何做一个镜像，而且我们写的微服务项目以及 springboot 打包上云部署，Docker就是最方便的。

微服务打包成镜像，任何装了Docker的地方，都可以下载使用，极其的方便。

流程：开发应用=>DockerFile=>打包为镜像=>上传到仓库（私有仓库，公有仓库）=> 下载镜像 => 启动 运行。

还可以方便移植！

## 什么是 DockerFile

dockerfile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本

构建步骤：

1、编写DockerFile文件 

2、docker build 构建镜像 

3、docker run

查看之前拉取的 centos ：https://hub.docker.com/_/centos

![image-20210606163702268](https://gcore.jsdelivr.net/gh/oddfar/static/img/Docker.assets/14.Docker-DockerFile.assets/image-20210606163702268.png)

查看`Dockerfile` 文件

![image-20210606163720066](https://gcore.jsdelivr.net/gh/oddfar/static/img/Docker.assets/14.Docker-DockerFile.assets/image-20210606163720066.png)

## DockerFile 构建过程

**基础知识：**

1、每条保留字指令都必须为大写字母且后面要跟随至少一个参数

2、指令按照从上到下，顺序执行

3、# 表示注释

4、每条指令都会创建一个新的镜像层，并对镜像进行提交

**流程：**

1、docker从基础镜像运行一个容器

2、执行一条指令并对容器做出修改

3、执行类似 docker commit 的操作提交一个新的镜像层

4、Docker再基于刚提交的镜像运行一个新容器

5、执行dockerfile中的下一条指令直到所有指令都执行完成！

**说明：**

从应用软件的角度来看，DockerFile，docker镜像 与 docker容器 分别代表软件的三个不同阶段。

- DockerFile 是软件的原材料 （代码）
- Docker 镜像则是软件的交付品 （.apk）
- Docker 容器则是软件的运行状态 （客户下载安装执行）

DockerFile 面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可！

![img_72](https://gcore.jsdelivr.net/gh/oddfar/static/img/Docker.assets/14.Docker-DockerFile.assets/img_72.png)

DockerFile：需要定义一个DockerFile，DockerFile定义了进程需要的一切东西。DockerFile涉及的内容 包括执行代码或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进 程和内核进程（当引用进行需要和系统服务和内核进程打交道，这时需要考虑如何设计 namespace的权 限控制）等等。

Docker镜像：在DockerFile 定义了一个文件之后，Docker build 时会产生一个Docker镜像，当运行 Docker 镜像时，会真正开始提供服务；

Docker容器：容器是直接提供服务的。

## DockerFile 指令



| 关键字     | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| FROM       | 基础镜像，当前新镜像是基于哪个镜像的                         |
| MAINTAINER | 镜像维护者的姓名混合邮箱地址                                 |
| RUN        | 容器构建时需要运行的命令                                     |
| EXPOSE     | 当前容器对外保留出的端口                                     |
| WORKDIR    | 指定在创建容器后，终端默认登录的进来工作目录，一个落脚点     |
| ENV        | 用来在构建镜像过程中设置环境变量                             |
| ADD        | 将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包 |
| COPY       | 类似ADD，拷贝文件和目录到镜像中！                            |
| VOLUME     | 容器数据卷，用于数据保存和持久化工作                         |
| CMD        | 指定一个容器启动时要运行的命令，dockerFile中可以有多个CMD指令，但只有最后一个生效！ |
| ENTRYPOINT | 指定一个容器启动时要运行的命令！和CMD一样                    |
| ONBUILD    | 当构建一个被继承的DockerFile时运行命令，父镜像在被子镜像继承后，父镜像的 ONBUILD被触发 |

![img_74](https://gcore.jsdelivr.net/gh/oddfar/static/img/Docker.assets/14.Docker-DockerFile.assets/img_74.png)

![img_75](https://gcore.jsdelivr.net/gh/oddfar/static/img/Docker.assets/14.Docker-DockerFile.assets/img_75.png)







## CMD 和 ENTRYPOINT 的区别

我们之前说过，两个命令都是指定一个容器启动时要运行的命令

- CMD

  Dockerfile 中可以有多个CMD 指令，但只有最后一个生效，CMD 会被 docker run 之后的参数替换！

- ENTRYPOINT

  docker run 之后的参数会被当做参数传递给 ENTRYPOINT，之后形成新的命令组合！



::: tip 测试 CMD 命令

:::

1、编写文件

```sh
[root@VM-0-6-centos dockerfile-test]# vim dockerfile-cmd-test
[root@VM-0-6-centos dockerfile-test]# cat dockerfile-cmd-test
FROM centos 
CMD [ "ls", "-a" ]
```



2、构建并运行

```sh
docker build -f dockerfile-cmd-test -t cmdtest .

docker run cmdtest
```

![image-20210606171411821](https://gcore.jsdelivr.net/gh/oddfar/static/img/Docker.assets/14.Docker-DockerFile.assets/image-20210606171411821.png)

3、如果我们希望用 -l 列表展示信息，我们就需要加上 -l参数

```sh
[root@VM-0-6-centos dockerfile-test]#  docker run cmdtest -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:380: starting container process caused: exec: "-l": executable file not found in $PATH: unknown.
```

问题：我们可以看到可执行文件找不到的报错，executable file not found。

之前我们说过，跟在镜像名后面的是 command，运行时会替换 CMD 的默认值。

因此这里的 `-l` 替换了原来的 CMD，而不是添加在原来的 `ls -a` 后面。

而 -l 根本不是命令，所以自然找不到。

那么如果我们希望加入 `-l `这参数，我们就必须重新完整的输入这个命令：

```sh
docker run cmdtest ls -al
```



::: tip 测试 ENTRYPOINT 命令

:::

1、编写文件

```sh
[root@VM-0-6-centos dockerfile-test]# vim dockerfile-entrypoint-test
[root@VM-0-6-centos dockerfile-test]# cat dockerfile-entrypoint-test
FROM centos
ENTRYPOINT [ "ls", "-a" ]
```

2、构建并运行

```sh
docker build -f dockerfile-entrypoint-test -t entrypointtest .

docker run entrypointtest
```

3、测试 -l 参数，发现可以直接使用，这里就是一种追加

我们可以明显的知道 CMD 和 ENTRYPOINT 的区别了

```sh
docker run entrypointtest -l
```

![image-20210606171953764](https://gcore.jsdelivr.net/gh/oddfar/static/img/Docker.assets/14.Docker-DockerFile.assets/image-20210606171953764.png)





## 实战测试

### CentOS

官方默认的 CentOS 的情况不支持 `vim` 和 `ifconfig` 指令

我们自己构建一个支持这些指令的镜像

**1、编写文件**

```sh
[root@VM-0-6-centos home]# cd dockerfile-test
[root@VM-0-6-centos dockerfile-test]# ls
my-centos
[root@VM-0-6-centos dockerfile-test]# cat my-centos
FROM centos
MAINTAINER zhiyuan<oddfar@163.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools


EXPOSE 80

CMD echo $MYPATH
CMD echo "----end-----"
CMD /bin/bash
```

**2、构建**

命令最后有一个 `.` 表示当前目录

```sh
docker build -f my-centos -t mycentos:1.1 .
```

成功后：

![image-20210606165936963](https://gcore.jsdelivr.net/gh/oddfar/static/img/Docker.assets/14.Docker-DockerFile.assets/image-20210606165936963.png)

**3、运行**

```sh
docker run -it mycentos:1.1
```

测试后，可以看到，我们自己的新镜像已经支持 vim 和 ifconfig的命令了



- 列出镜像的变更历史：

  `docker history 镜像名\镜像id`



### Tomcat

步骤：

- 将 JDK 和 tomcat 安装的压缩包拷贝`/home/build` 目录下

  下载地址：

  链接：https://pan.baidu.com/s/1QZEmvAgyh4-8DbebVz7koQ 

  提取码：spfo 

- 新建一个 read.txt 文件

- 新建一个 Dockerfile 文件

```sh
[root@VM-0-6-centos home]# cd build
[root@VM-0-6-centos build]# ls
apache-tomcat-9.0.46.tar.gz  Dockerfile  jdk-8u11-linux-x64.tar.gz  read.txt  tomcat
```

Dockerfile  内容

```sh
FROM centos

MAINTAINER zhiyuan<test@qq.com>
#把宿主机当前上下文的read.txt拷贝到容器/usr/local/路径下
COPY read.txt /usr/local/cincontainer.txt
#把java与tomcat添加到容器中
ADD jdk-8u11-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.46.tar.gz /usr/local/
#安装vim编辑器
RUN yum -y install vim
#变量
ENV MYPATH /usr/local
#设置工作访问时候的WORKDIR路径，登录落脚点
WORKDIR $MYPATH
#配置java与tomcat环境变量
ENV JAVA_HOME /usr/local/jdk1.8.0_11
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.46
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.46
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
#容器运行时监听的端口
EXPOSE 8080
#启动时运行tomcat
# ENTRYPOINT ["/usr/local/apache-tomcat-9.0.46/bin/startup.sh" ]
# CMD ["/usr/local/apache-tomcat-9.0.46/bin/catalina.sh","run"]
CMD /usr/local/apache-tomcat-9.0.46/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.46/bin/logs/catalina.out
```

**构建镜像：**

```sh
docker build -t diytomcat .
```

在此目录下默认构建 `Dockerfile` 文件，所以不需要带上文件名

**启动：**

```sh
docker run -d  -p 9090:8080 --name mydiytomcat -v /home/build/tomcat/test:/usr/local/apache-tomcat-9.0.46/webapps/test -v /home/build/tomcat/logs/:/usr/local/apache-tomcat-9.0.46/logs --privileged=true diytomcat
```

备注：Docker挂载主机目录Docker访问出现cannot open directory Permission denied

解决办法：在挂载目录后多加一个--privileged=true参数即可

**写个测试网站扔到test目录：**

web.xml

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
  version="4.0"
  metadata-complete="true">

  <display-name>Test</display-name>

</web-app>
```



a.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>测试</title>
</head>
<body>
	Hello World!<br/>
	<% System.out.println("-------my docker tomcat-------");%>
</body>
</html>
```

**查看日志：**

```sh
[root@VM-0-6-centos tomcat]# cd logs
[root@VM-0-6-centos logs]# cat catalina.out
```

![image-20210606174533465](https://gcore.jsdelivr.net/gh/oddfar/static/img/Docker.assets/14.Docker-DockerFile.assets/image-20210606174533465.png)





### SpingBoot



1、使用 IDEA 构建一个 SpringBoot 项目

2、编写 Controller

```java
@RestController
public class HelloController {
    @GetMapping("/hello")
    public String hello(){
	    return "hello,world";
    }
}
```

打包成 jar 包

3、构建镜像

将打包好的 jar 包拷贝到 Dockerfile 同级目录，编写 Dockerfile文件

```sh
FROM java:8

# 服务器只有dockerfile和jar在同级目录
COPY *.jar /app.jar

CMD ["--server.port=8080"]

# 指定容器内要暴露的端口
EXPOSE 8080

ENTRYPOINT ["java","-jar","/app.jar"]
```

构建运行

```sh
# 构建镜像
docker build -t idea-ks .

# 运行
docker run -d -P --name idea-ks idea-ks
```

最后测试访问