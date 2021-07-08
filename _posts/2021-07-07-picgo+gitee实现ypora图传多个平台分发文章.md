## 背景

在微信公众号编写了推文后，从推文里面复制内容出来在其他平台发布，会出现图片展示不出来的情况，原因是因为该图片是微信的链接，被限制在其他平台显示。



## 诉求

编写的推文在多个平台都能进行发布，图片在多个平台展示正常。



## 方案

目前有多种方案实现markdown文件的图床，存放图片的平台也很多。有github、SM.MS等等，如截图
<img src="https://gitee.com/totorovip/imgs/raw/master/image-20210706001951681.png" alt="image-20210706001951681" style="zoom:50%;" />

综合github在国内访问较慢，自己没有腾讯云、七牛、阿里云等的账号，我这里选择使用gitee（码云），gitee可以理解成是国内的github，访问速度可以接受。

所以我这里选择作为markdown图床的方案是***`typora+picgo+gitee`***，就这样构成我的博客写作环境。

- `Typora`：它是一款轻便简洁的Markdown编辑器，支持即时渲染技术，即所写立刻所见，少了排版的时间，专注于文章内容的编辑。
- `PicGo`:它是一个用于快速上传图片并获取图片 URL 链接的工具，支持多种图库。
- `Gitee`:目前中国最大的代码托管的工具，除了代码，还可以用作图片存储。

## 搭建步骤

（借鉴网上，图侵删，重点记录在安装过程出现的问题复盘）

### 安装`Typora`  
 下载地址：[typora.io](https://www.typora.io/)，

### 安装`PicGo`

1. 下载地址：[ Molunerfinn /PicGo](https://github.com/Molunerfinn/PicGo/releases)

   ![在这里插入图片描述](https://gitee.com/totorovip/imgs/raw/master/20201216213219959.png)

2. 为`picgo`安装gitee插件
   <img src="https://gitee.com/totorovip/imgs/raw/master/20201216215255706.png" alt="在这里插入图片描述" style="zoom:50%;" />

   这两个插件都可以用。
   安装完插件后重启picgo应用，在可选择图床那里会出现gitee的选项
   ![image-20210706003928812](https://gitee.com/totorovip/imgs/raw/master/image-202107060039288122.png)

### 配置图床

1. #### 创建公开仓库
   注册码云[gitee](https://gitee.com/)，建立图片存放的公开仓库
   ![image-20210707002806232](https://gitee.com/totorovip/imgs/raw/master/image-20210707002806232.png)
2. #### 设置私人令牌  
![image-20210706003246941](https://gitee.com/totorovip/imgs/raw/master/image-20210706003246941.png)

![image-20210706003405621](https://gitee.com/totorovip/imgs/raw/master/image-20210706003405621.png)

记录私人令牌，配置图床需用到
![image-20210706003727157](https://gitee.com/totorovip/imgs/raw/master/image-20210706003727158.png)

3. #### gitee图片配置
   ![image-20210706004218341](https://gitee.com/totorovip/imgs/raw/master/image-20210707002018298.png)

4. #### PicGo配置
   ![image-20210706004319764](https://gitee.com/totorovip/imgs/raw/master/image-20210706004319764.png)

### 图床接入
打开typora -> 偏好设置->上传服务设定
![image-20210706004537320](https://gitee.com/totorovip/imgs/raw/master/image-20210706004537320.png)
点击验证图片上传选项，成功就会显示获取成功。

- 7）拖一张图片进来typora，看到图片的gitee地址出来了，上传成功
  ![image-20210706004755031](https://gitee.com/totorovip/imgs/raw/master/image-20210706004755031.png)

  

## 问题及解决方法

   1. gitee插件一直显示安装中，最后报错无法安装成功
      【问题原因】npm源指定了公司的源，没有打开公司内网的iOA工具导致的。picgo安装gitee插件是通过npm安装，因为网络问题无法访问到npm的源，所以安装失败了。
      【解决方法】打开内网的iOA即可。
      
   2. gitee插件安装成功后，上传一直失败
         gitee图片的owner名称填写错误。这里owner的名字要填写xx.git路径里面的那个名称。
         
         ```css 
         {“success”:false,“message”:“upload error”}
         ```
         
         
         
         ![image-20210707002018298](https://gitee.com/totorovip/imgs/raw/master/image-20210707002018298.png)
         
          ![image-20210706235540197](https://gitee.com/totorovip/imgs/raw/master/image-20210706235540197.png)
         
  3. 定位gitee图床配置失败的问题，要学会看***`picgo log`***
        我遇到前面两个问题，一开始没看log，以为是插件的问题，换了另一个gitee插件还是无法上传成功，接着去找picgo的上传日志，写的清晰

        ![image-20210707000111779](https://gitee.com/totorovip/imgs/raw/master/image-20210707000111779.png) 当插件无法安装成功的时候，log很清晰指出请求npm源TimeOut👉
        
        ![image-20210707000652434](https://gitee.com/totorovip/imgs/raw/master/image-20210707000652434.png)

