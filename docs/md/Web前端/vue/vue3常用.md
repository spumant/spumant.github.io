---
layout: post
cid: 27
title: Vue3
slug: 27
date: 2022/10/30 08:34:00
updated: 2022/10/30 18:33:32
status: publish
author: 翕
categories: 
  - Web前端
tags: 
abcimg: able
bimg: https://cdn.acwing.com/media/article/image/2022/10/30/192601_cb264ed858-vue.jfif
img: https://cdn.acwing.com/media/article/image/2022/10/30/192601_cb264ed858-vue.jfif
---


#配置环境#
[Vue官网][1]

终端
Linux和Mac上可以用自带的终端。
Windows上推荐用powershell或者cmd。Git Bash有些指令不兼容。

安装Nodejs
安装地址

安装@vue/cli
打开Git Bash，执行：
```
npm i -g @vue/cli
```
如果执行后面的操作有bug，可能是最新版有问题，可以尝试安装早期版本，比如：npm i -g @vue/cli@4

启动vue自带的图形化项目管理界面
```
vue ui
```
常见问题1：Windows上运行vue，提示无法加载文件，表示用户权限不足。
解决方案：用管理员身份打开终端，输入set-ExecutionPolicy RemoteSigned，然后输入y

#基本概念#
script部分
export default对象的属性：

 - name：组件的名称
 - components：存储<template>中用到的所有组件
 - props：存储父组件传递给子组件的数据
 - watch()：当某个数据发生变化时触发
 - computed：动态计算某个数据
 - setup(props, context)：初始化变量、函数
   - ref定义变量，可以用.value属性重新赋值
   - reactive定义对象，不可重新赋值
   - props存储父组件传递过来的数据
   - context.emit()：触发父组件绑定的函数

template部分

 - <slot></slot>：存放父组件传过来的children。
 - v-on:click或@click属性：绑定事件
 - v-if、v-else、v-else-if属性：判断
 - v-for属性：循环，:key循环的每个元素需要有唯一的key
 - v-bind:或:：绑定属性

style部分

 - <style>标签添加scope属性后，不同组件间的css不会相互影响。

第三方组件

 - view-router包：实现路由功能。
 - vuex：存储全局状态，全局唯一。
   - state: 存储所有数据，可以用modules属性划分成若干模块
   - getters：根据state中的值计算新的值
   - mutations：所有对state的修改操作都需要定义在这里，不支持异步，可以通过$store.commit()触发
   - actions：定义对state的复杂修改操作，支持异步，可以通过$store.dispatch()触发。注意不能直接修改state，只能通过
   - mutations修改state。
   - modules：定义state的子模块

  [1]: https://vuejs.org/