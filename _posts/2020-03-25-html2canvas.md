---
layout:     post
title:      "使用html2canvas生成图片遇到的坑"
subtitle:   "canvas2html，跨域，canvas 归纳笔记"
date:       2020-03-26
author:     "totoro"
header-img: "img/20200326-canvas2html.jpg.jpg"
catalog: true
tags:
    - 笔记
    - Web
    - JavaScript
    - html2cavnas
    - 前端开发
---
# 使用html2canvas生成图片遇到的坑

### 背景

活动H5，需要开发一个长按保存图片的功能，我们知道在微信里面，长按一张图片，会弹窗保存或者分享的菜单。在使用htl2canvas生成图片的过程中，遇到下面的问题以及解决采用的方案。

### 遇到的问题以及解决方法

1. logo图片模糊
   【问题描述】在H5页面有通过`background-image`的形式展示logo图片，logo图片使用的是以@3x的尺寸输出的。但是通过html2canvas绘制出来的图片中，里面的logo依旧模糊。
   【问题解决】使用`<img>`标签插入图片，不要使用`background-image`
   
2. 没有生成图片
   【问题描述】同一个canvas生成图片组件，这个组件在A域名下可以生成，在B域名的分享页无法生成，在console里看到报错信息：
   
   ```
   Uncaught (in promise) DOMException: Failed to execute 'drawImage' on 'CanvasRenderingContext2D': The image argument is a canvas element with a width or height of 0.
   ```
   
   【问题解决】图片跨域的问题！！图片是在CDN的域名下，在A域名活动仓库下，由于webpack构建的把图片的limit设置得很大，此时图片都被转为base64了，所以不会存在跨域的问题。来到B域名的仓库，图片没有全部被转为base64，所以此时引用的图片链接和页面不是属于同一个域名。出现跨域。后面的解决方法：是把图片改成同域名。
   写一个`getIImg()`方法，在页面读取这些图片的url后进行域名替换。我们项目把图片发到上线的时候在本域名和CDN都可访问到。所以写一个方法替换图片url是ok的。
   
   ```javascript
   loadImg() {
     this.unloadList = [this.label.imgUrl];
     if (this.unloadList.length > 0) {
       let url = this.unloadList.pop();
       getImage(url, false).then(() => {
         this.loadImg();
       });
     }
   }
   // 增加扩展，是否要替换图片路径
   function getImage(imageUrl: any, replaceurl: boolean = true): any {
     return new Promise((resolve) => {
       let parsedImageUrl = imageUrl;
       if (replaceurl) {
         if (!/^data/.test(imageUrl) && /^https?/.test(imageUrl)) {
           parsedImageUrl = url.parse(imageUrl).path;
         }
       }
   
       const img = new Image();
       img.src = parsedImageUrl;
       img.onload = () => resolve(img);
     });
   }
   ```

3. canvas-view的工作原理

   截图的是视窗看到的内容。

   ```javascript
   <CanvasView ref="view" @ready="onCanvasViewReady" @clone="onClone" :scale="scale">
   </CanvasView>
   // 页面开始渲染的时候会为这个容器添加一个canvas-view的样式，这时候这个div才变得可见
   onClone(doc: Document) {
     doc.body.classList.add('canvas-view');
   }
   ```

   

   1. 开始执行该方法的时机是DOM渲染完成，要用`v-if`，而不能用v-show。v-show DOM结构已经渲染好了，只是被隐藏而已。
   2. 当长按保存生生图片时才希望样式展示，可以在.canvas-view里面去写样式。
