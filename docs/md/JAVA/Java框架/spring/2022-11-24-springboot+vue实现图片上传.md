---
layout: post
cid: 348
title: springboot+vue实现图片上传
slug: 348
date: 2022/11/24 20:05:03
updated: 2022/11/24 20:05:03
status: publish
author: 翕
categories: 
  - Java框架
tags: 
customSummary: 
mathjax: auto
noThumbInfoEmoji: 
noThumbInfoStyle: default
outdatedNotice: no
parseWay: auto
reprint: standard
thumb: 
thumbChoice: default
thumbDesc: 
thumbSmall: 
thumbStyle: default
---


#方式一
##点击Vue图标，选择电脑中图片，选中后把图片替换为选中图片：
```
<template>
  <div>
    <!-- 选择图片后展示 -->
    <img :src="f.src" width="100px" height="100px" v-if="f" />
    <!-- 选择图片之前展示 -->
    <img src="../assets/logo.png" @click="add()" width="100px" height="100px" v-else />
    <input
      class="value"
      style="display:none"
      type="file"
      ref="file"
      accept="image/*"
      multiple="multiple"
      @change="getFile($event)"
    />
    <span @click="submitForm($event)">上传</span>
    <img :src="loadImage" width="100px" height="100px" />
  </div>
</template>

```
```
import axios from "axios";
export default {
  props: {},
  data() {
    return {
      file: null,
      f: null,
      loadImage: ""
    };
  },
  methods: {
    add() {
      this.$refs.file.click();
    },
    getFile(event) {
      this.file = event.target.files[0];
      const item = {
        name: this.file.name,
        size: this.file.size,
        file: this.file
      };
      this.html5Reader(this.file, item);
      this.f = item;
    },
    // 将图片文件转成BASE64格式
    html5Reader(file, item) {
      const reader = new FileReader();
      reader.onload = e => {
        this.$set(item, "src", e.target.result);
      };
      reader.readAsDataURL(file);
    }
  }
};

```
##点击上传，通过文件流的方式把图片上传至服务器，服务器将图片保存至指定位置，并根据时间生成指定名称，上传成功，返回新生成文件名。：
```
/**
     * 上传图片接口
     * */
    @PostMapping("/upload")
    public String singleFileUpload(@RequestParam("file") MultipartFile file,
                                   HttpServletRequest request) {
        try {
            byte[] bytes = file.getBytes();
            String imageFileName = file.getOriginalFilename();
            String fileName = UpPhotoNameUtils.getPhotoName("img",imageFileName);
            Path path = Paths.get(UPLOAD_PATH + fileName);
            Files.write(path, bytes);
            System.out.println(fileName+"\n");
            return fileName;
        } catch (IOException e) {
            e.printStackTrace();
        }

        return "";
    }
/**
 * 处理图片名的工具类
 * */
public class UpPhotoNameUtils {
    public static String getPhotoName(String name,String imageFileName){
        String fileName = name;
        Calendar cal=Calendar.getInstance();
        //如果年的目录不存在，创建年的目录
        int year=cal.get(Calendar.YEAR);
        fileName=fileName + "_" + year;
        //如果月份不存在，创建月份的目录
        int month=cal.get(Calendar.MONTH)+1;
        fileName=fileName+"_";
        if(month<10)
        {
            fileName=fileName+"0";
        }
        fileName=fileName+month;
        //生成文件名的日部分
        int day=cal.get(Calendar.DAY_OF_MONTH);
        fileName=fileName+"_";
        if(day<10)
        {
            fileName=fileName+"0";
        }
        fileName=fileName+day;
        //生成文件名的小时部分
        int hour=cal.get(Calendar.HOUR_OF_DAY);
        if(hour<10)
        {
            fileName=fileName+"0";
        }
        fileName=fileName+hour;
        //生成文件名的分钟部分
        int minute=cal.get(Calendar.MINUTE);
        if(minute<10)
        {
            fileName=fileName+"0";
        }
        fileName=fileName+minute;
        //生成文件名的秒部分
        int second=cal.get(Calendar.SECOND);
        if(second<10)
        {
            fileName=fileName+"0";
        }
        fileName=fileName+second;
        //生成文件名的毫秒部分
        int millisecond=cal.get(Calendar.MILLISECOND);
        if(millisecond<10)
        {
            fileName=fileName+"0";
        }
        if(millisecond<100)
        {
            fileName=fileName+"0";
        }
        fileName=fileName+millisecond;
        //生成文件的扩展名部分,只截取最后单位
        String endName = imageFileName.substring(imageFileName.lastIndexOf("."));//截取之后的值
        fileName=fileName+ endName;
        return fileName;
    }
}

```
##服务器自定义接口，通过需要的文件名，返回指定文件流：
```
  //使用流将图片输出
    @GetMapping("/getImage/{name}")
    public void getImage(HttpServletResponse response, @PathVariable("name") String name) throws IOException {
        response.setContentType("image/jpeg;charset=utf-8");
        response.setHeader("Content-Disposition", "inline; filename=girls.png");
        ServletOutputStream outputStream = response.getOutputStream();
        outputStream.write(Files.readAllBytes(Paths.get(UPLOAD_PATH).resolve(name)));
        outputStream.flush();
        outputStream.close();
    }

```
##上传图片成功后，在旁边显示上传成功的图片：
```
submitForm(event) {
      event.preventDefault();
      let formData = new FormData();
      formData.append("file", this.file);
      let config = {
        headers: {
          "Content-Type": "multipart/form-data"
        }
      };
      axios
        .post("http://.../test/upload", formData, config)
        .then(res => {
          console.log(res);
          if (res.status === 200) {
            try {
              console.log(".../getImage/" + res.data);
              if (typeof this.loadImage !== "undefined") {
                this.loadImage =
                  "http:.../getImage/" + res.data;
              }
            } catch (e) {
              console.log("捕获到异常：", e);
            }
          } else {
            console.log("失败" + 400);
          }
        });
    }

```
#方式二简化版
##前端代码
```
 <form method="post" action="/upload" enctype="multipart/form-data">
                    <input type="file" name="file"><br>
                    <button type="submit" style="position: absolute;width:80px;height:40px;top:185px;left:305px;">提交</button>
                </form>

```
这个代码可以放在任何地方，比如element ui的提示框，代码如下：
```
 <el-dialog title="上传头像提示框" :visible.sync="uploadVisible" width="30%">
            <div class="container-fluid">
                <form method="post" action="/upload" enctype="multipart/form-data">
                    <input type="file" name="file"><br>
                    <button type="submit" style="position: absolute;width:80px;height:40px;top:185px;left:305px;">提交</button>
                </form>
            </div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="uploadVisible = false">取 消</el-button>
              </span>
        </el-dialog>

```
##后端代码（控制层）：以SpringBoot为例接收
```
在这里插入代码片@PostMapping("/upload")  //上传头像
    public String upload(@RequestParam("file") MultipartFile file,HttpServletRequest request) {
        HttpSession session = request.getSession();
        User user=(User) session.getAttribute("user");
        int begin = file.getOriginalFilename().indexOf(".");
        int last = file.getOriginalFilename().length();
        //获得文件后缀名: a
        String a = file.getOriginalFilename().substring(begin, last);
        String fileName=user.getAccount()+a;  //我存储的文件名是该用户的账号加文件后缀名，比如201521.jpg
        String filePath = "C:/Users/lenovo/IdeaProjects/police/src/main/resources/static/image/";
        //这个是你的存储路径，为了方便，我用的是绝对路径，直接把文件放在项目的资源文件夹里
        File dest = new File(filePath + fileName);
        try {
            file.transferTo(dest); //存储！
            //数据库存放的地址，存的是项目相对路径，这个看你们自己
            User user1=new User(user.getAccount(),"../static/image/"+fileName,1); 
            userService.updateUserHeadImage(user1);
        } catch (IOException e) {
        }
        return "userInfo";
    }

```