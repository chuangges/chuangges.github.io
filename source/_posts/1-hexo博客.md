---
title: Hexo + Github 搭建个人博客
date: 2019-02-09 02:31:41
tags: Hexo
categories: Hexo Blog
top: false
keywords:
  - hexo
description: Hexo 是一个快速、简洁且高效的博客框架
---

# 一、博客搭建

## 安装 Node.js 和 Git
> Git安装后可以创建 `ssh key` 并添加到 GitHub上, 这样配置之后就不再需要每次更新博客时都输入用户名和密码了

## 安装hexo博客框架
  ```js
  cnpm install -g hexo-cli    // window安装
  sudo cnpm install -g hexo   // mac安装
  
  cd blog            //在新建的blog文件夹打开终端
  hexo init          // 初始化

  hexo install       // window安装依赖
  sudo npm install   // mac安装依赖
  ```


## 测试本地运行（线下访问）
  ```js
  hexo g     // 生成静态文件, 即 hexo generate

  hexo s     // 启动本地服务器, 即 hexo server
  ```

## 部署到云服务器（线上访问）
> Github 是外国网站且禁止百度爬虫访问，所以会导致百度搜不到你的网站。可以做两手准备：国内采用Coding托管，国外采用GitHub托管，建议两者的用户名和密码保持一致  

### 配置步骤
  1. 新建项目
      GitHub：`username.github.io`
      Coding：https://coding.net/
  2. 配置 SSH
    * 检查：`cd ~/.ssh`
    * 生成：`ssh-keygen -t rsa -C "17621538916@163.com"`
    * 输入密码并回车
    * 查看公钥：`cat ~/.ssh/id_rsa.pub` 
  3. 添加公钥
  4. 基础配置
    * 用户名：`git config --global user.name "chuanggefighting"`
    * 邮箱：`git config --global user.email "17621538916@163.com"` 
  5. 测试连接：`ssh -T git@github.com、ssh -T git@git.coding.net`
  6. 开启 Pages 服务


### 修改配置文件
> 码云是国内的，访问速度较快，但是每次更新版本后需要手动部署

  ```yaml
  deploy:
    type: git
    repo: 
      github: https://github.com/chuanggefighting/chuanggefighting.github.io.git
      # coding: https://git.coding.net/chuanggefighting/chuanggefighting.coding.me.git     # Coding
    repository: https://gitee.com/chuangges/chuangges.git     # 码云
    branch: master
  ```


### 上传到服务器

  ```js
  cnpm install hexo-deployer-git --save  // 安装git部署插件
  
  hexo clean              // 清除缓存
  hexo n post             // 新建文章，即 hexo new post 
  hexo g                  // 编译博文生成静态文件, 即 hexo generate
  hexo d                  // 部署到github, 即 hexo deploy
  hexo hexo g -d          // 简化命令, 部署前生成静态文件
  ```



## 创建新页面

  1. __增加关于页__
    1. 取消 about 前面的 \#  
    2. hexo new page about  
    3. blog/sources/about/index.md：自定义内容

  2. __增加标签页__
    1. 首先取消 next/config.yml 文件中 tags 前面的 \#  
    2. hexo new page tags
    3. blog/sources/about/index.md：`type: tags`
    <br/>
    
  3. __增加分类页__
    1. 取消 categories 前面的 \#  
    2. hexo new page "categories"
    3. blog/sources/categories/index.md：`type: categories`
    <br/>

  4. __增加归档页__
    1. 取消 categories 前面的 \#  
    2. hexo new page "archive"
    3. blog/sources/archive/index.md：`type: archive`



# 二、博客管理

  1. __NexT主题配置文件__
   <div style="text-indent: 2em; margin-bottom: 25px">NexT主题由于频繁更新，为了避免升级报错可以另存为一份配置，然后操作这个配置文件即可。首先在 blog/source 目录下新建_data文件夹，然后去复制 blog/themes/next/_config.yml 到本地并改名为 next.yml，最后将 next.yml 放置在 _data 中即可，以后编辑next.yml即可配置主题。</div>
  2. __hexo博客源文件__ 
   <div style="text-indent: 2em; margin-bottom: 25px">hexo d 是把本地博客源文件生成的静态网页文件同步到github上而实现部署, 但是博客网站的本地源文件仍需要保存到个人电脑，为了方便在不同电脑上可以编辑管理，可以在github上另建分支，如 hexo分支 </div>



# 三、博客个性化配置

  1. __Hexo配置__ (blog/_config.yml)
    * Site 站点配置(网站标题、作者、语言等)
    * URL 网址配置(网址、根目录、链接格式等)
    * Extensions 扩展配置(主题、插件等)
    * 其他配置选项一般不需要修改
  2. __主题配置__ (具体在博客优化部分)
    * 安装主题 ：通过 git clone 下载到 blog/themes
    * 启动主题 ：修改 blog/_config.yml 的theme选项
    * 配置主题 ：修改 blog/themes/主题名/_config.yml
  3. __自定义域名配置__
    * 购买域名
    * 域名解析
    * 添加CNAME


# 四、NexT主题优化

  1. __实用性优化__
    * 添加RSS ：hexo-generator-feed
    * 添加标签、分类等页面
    * 设置网站icon
    * 添加侧边栏链接
    * 增加版权信息
    * 微信支付宝打赏功能
    * 底部显示建站时间和图标的修改
    * 外部链接优化 ：hexo-autonofollow 
    * 关闭网站动画 ：use_motion
    * 设置第三方JS库 ：vendors
    * 添加评论系统 ：leancloud
    * 统计站点访客和阅读量 ：busuanzi
    * 统计文章字数和阅读时间 ：symblos_count_time
    * 添加文章分享功能 ：needmoreshare2
    * 添加文章加密功能 ：hexo-blog-encrypt
    * 添加图片的懒加载 ：hexo-lazyload-image
    * 添加站内搜索功能 ：hexo-generator-searchdb
    * 添加文章置顶功能 ：hexo-generator-index-pin-top
    * 添加站点地图配置 ：hexo-generator-sitemap、hexo-generator-baidu-sitemap
    * DaoVoice在线联系
  2. __博客个性化优化__
    * 添加页面加载动画 ：pace
    * 添加背景动画 ：canvas_nest
    * 添加宠物 ：hexo-helper-live2d
    * 添加顶部阅读进度 ：reading_progress
    * 点击出现桃心效果 ：clicklove.js
    * 添加代码块复制按钮 ：clickboard.js
    * 文章末尾统一添加 “文本结束” 标记
    * 修改文章底部标签样式
    * 右上角的Github样式
    * 修改作者头像并旋转
    * 文章添加阴影效果



# 五、基于 Markdown 编写博文

## 创建文章
  * 站点目录下执行命令 hexo new "title"
  * 指定目录下直接创建 source/_post/title.md


## 初始化设置
<div style="text-indent: 2em">使用命令创建文章时，Hexo 会根据文章的模板文件 /scaffolds/post.md 对新建文件进行初始化，可以根据需要自行修改。初始化后的文章头部除了可以设置文章标题、发布日期等基础信息外，还可以对文章添加标签、分类等，常用设置如下：</div> 

  ```yaml
  ---
  title: my blog
  date: 2019-02-04 20:45:30
  tags: [Hexo, MarkDown] 
  categories: 学习笔记
  keywords:
      - Hexo
      - 加密

  # 预览文章摘要
  description: Markdown语法的格式和注意点  

  # 预览加密文章摘要
  password:        # 文章密码
  abstract: enter password to read      # 文章摘要
  message: My Birthday      # 密码提示

  # 预览文章内容
  # 在要显示的内容末尾添加more分隔符 <!-- more -->

  ---
  ```


## 编写文章（基于Markdown）

### Markdown 简介
 <div style="text-indent: 2em">Markdown是一种可以使用普通文本编辑器编写的轻量级标记语言，通过简单的标记语法使普通文本内容具有一定的格式，是一种适用于网络的书写语言，主要特点是易读易写、支持嵌入html标签和自动生成目录等。但是Hexo下使用的 Github风格的MarkDown（GFM）和 标准MarkDown（MD）在语法上稍有不同，以下主要介绍GFM语法。</div> 

### MD 与 GFM 的区别
  * 斜体 ：MD 使用 \_ 或 \*，GFM 只支持 \*
  * 自动链接 ：MD 使用 <URL>，GFM 可直接使用 URL
  * 代码块 ：MD 使用 4个空格开头，GFM 还可以使用 \`\`\` 格式
  * 其他 ：GFM 可以指定语言高亮，而且增加了 删除线、表格、锚点等


### 常用语法
  * 标题 ：根据 \# 的数量显示几级标题（1～6）
  * 引用 ：根据 > 的数量显示几级引用文本
  * 转义 ：使用 \\ 显示文本中的一些字符
  * 强调 ：使用 \* 或 \_ 显示 斜体、粗体、粗斜体（1～3），~~ 显示删除
  * 链接 ：行内式 \[名字]\(地址 "描述")，参考式 \[名字]\[网址变量]
  * 列表 ：无序列表使用 \-、\+ 或 \*，有序列表则使用数字加 \.
  * 代码 ：行内代码使用 \`，代码块则使用 4个空格 或 \`\`\`
  * 表格 ：\- 和 | 分割行和列，: 控制对其方式
  * 图片 ：链接方法前面加 \!


  ```
  *斜体文本*  _斜体文本_  **粗体文本**  __粗体文本__  ***粗斜体文本***  ___粗斜体文本___
  ~~删除一段文本~~

  > 动物
  >> 水生动物

  [my blog](https://chuanggefighting.github.io/)   
  [Google][1] and [Baidu][2]

  [1]: http://google.com/   "Google" 
  [2]: http://baidu.com/    "Baidu"

  Python
  #!/usr/bin/env python
  # -*- coding: utf-8 -*-
  print 'Hello World!
  ```
  *斜体文本*  _斜体文本_  **粗体文本**  __粗体文本__  ***粗斜体文本***  ___粗斜体文本___
  ~~删除一段文本~~

  > 动物
  >
  > > 水生动物

  [my blog](https://chuanggefighting.github.io/)   
  [Google][1] &#160;and&#160; [Baidu][2]

  [1]: http://google.com/   "Google"
  [2]: http://baidu.com/    "Baidu"

  ```Python
  #!/usr/bin/env python
  # -*- coding: utf-8 -*-
  print 'Hello World!
  ```


### 次常用语法
  * 分段：两个空格
  * 分隔线 ：在一行中使用三个以上的 \*、\-、\_
  * 换行：两个空格 \+ 回车 （引用中换行省略回车）
  * 首行缩进：使用转义字符代替空格，或者使用html标签
  * 脚注 ：使用 \[^name] 定义，用来解释专业词汇等


### 内嵌 Html 标签  
  <div style="text-indent: 2em">Markdown本身不支持修改字体、字号与颜色等功能，但是可以通过内嵌Html标签使普通文本内容具有一定的格式，常用如下：</div> 

  * <font face="微软雅黑" color="red" size="3">字体及字体颜色和大小</font>
  * 换行<br/>
  * <u>下划线文本</u>
  * <span align="left">文本对齐</span>
  * <span style="text-indent: 2em">首行缩进</span> 



## 插入图片方式

* Markdown 语法
  * 本地图片：`![图片描述](/images/icon.png)`，注意需要将图片统一放在主题目录 source/images
  * 网络图片：`![图片描述](http://baidu.com/pic/doge.png)`
    * 国内访问 github 速度较慢，所以将图片上传到一些国内图床 (专门提供存储图的免费 CDN 服务器)。
    * 图床可以分为 __公共图床__ (利用公共服务的图片上传接口，比如新浪微博)、__自建图床__ (利用各大云服务商提供的存储空间或者自己在 VPS 上使用开源软件来搭建图床，存储图片并生成外链提供访问，比如七牛、Lychee 开源自建图床方案)。
* Hexo 语法
  * 直接插入：`{% asset_img blog.jpg 图片描述 %}`，注意须使用相对路径、hexo3 以上版本。
  * 通过插件：`npm install hexo-asset-image –save、![图片描述](file/icon.png)`，注意安装插件后 hexo 新建博文时会新增 md 文件的同名文件夹用于存放图片。


