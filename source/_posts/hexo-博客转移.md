---
title: hexo 博客转移
date: 2019-01-02 10:11:24
tags: hexo
categories: 
- hexo博客系列
---

最近觉得window下的命令行实在难看，即使是gitbash也无法拯救，毕竟一些乱码着实是要逼死我这种强迫症。于是下定决心，将笔记本里的另一个linux系统deepin作为主力系统，开始各方面的迁移。代码啥的都好说，毕竟有github托管，但是hexo博客就让人头疼了，对于我这个刚入行不久的小白，实在是不知道从何处下手。

## 最蠢的办法

估计这个办法是只有我这种蠢人与懒人才能想出来吧，直接把windows下的hexo博客相关文件复制一份放到deepin下跑起来。跑之前，我猜测可能环境配置肯定会出问题，出人意料之外的，竟然成功跑起来了，没有出问题。毕竟从windows到linux，个人觉得主要还是环境配置问题吧,比如node版本，一些环境依赖，比如python版本号

<!-- more -->
## 通过github保存配置文件

基本思路：将本地所有相关配置文件上传到github，然后clone到另一台电脑或者另一个系统，相关的配置文件都有了，理论上来说就是ok的。这样唯一的好处，就是基本可以在所有电脑里写博客后直接发布了。

### 1、上传相关配置文件到github

主要是配置文件，由于我怕出问题，基本把所有的相关配置文件都上传了，我的目录如下：

```
hexo
|--scaffolds
|--source
|--themes
|--.gitignore
|--_config.yml
|--package-lock.json
|--package.json
```

### 2、在另一台电脑（系统）初始化一个hexo博客目录

因为各个电脑系统环境配置都不一样，所以首先得保证最基本的hexo博客能运行起来：
首先，安装`node`，`git`

```
npm install -g hexo-cli // 安装hexo命令工具
hexo init <folder> // 初始化hexo博客目录
cd <folder>
npm install // 安装包依赖

```

### 3、从github上`git clone`出hexo配置文件

注意clone的文件夹不要放在第二步创建的文件下，将clone的文件下的所有文件复制粘贴到第二步的初始化目录中，同名的文件选择覆盖掉。
然后，执行相关命令，如无意外即可跑起来了。

```
// 以下是个人的博客操作,全部在第二步的博客目录下操作
cd themes/next/
npm i // 安装next主题包依赖

cd ../../
npm i // 执行全局配置包依赖

hexo s // 本地测试hexo博客
```

这样配置成功后，以后只需要把写的博客、相关的主题设置文件上传到github，其它的电脑直接从github上pull就ok了。

>注意:
>千万不要将相关的环境配置文件提交，比如package.json，保持所有的电脑配置文件一致，不同的电脑本地进行配置，不然会导致不同的电脑环境相互冲突，需要重新配置环境。

## 遇到的问题

### nodejieba模块安装出错（windows）

不同系统之间免不了出问题，最近在公司趁闲点写点博客，于是打算在公司的电脑按上述方法进行部署。结果报错，卡在nodejieba分词库安装失败，node-gyp rebuild 失败。

自然第一想法，谷歌搜索报错，找到[nodejieba分词库安装问题](https://github.com/Mrminfive/hexo-theme-skapp/issues/23)相关答案，顺着找到[node-gyp官方文档](https://www.npmjs.com/package/node-gyp),原来是node-gyp的python版本只支持2.7，官方给出了前置安装的两个办法，人懒的我自然选择前一种方法
```
npm install --global --production windows-build-tools

npm install -g node-gyp
```
安装完后，删掉node_modules,重新安装依赖，万事大吉，成功运行！至此，我这篇博客就是写到一般上传到github后，回家拉取继续写，顺利完成。

### themes文件git嵌套

由于nextz主题内的_config.yml也涉及到一些配置，为避免重新配置，最好把themes下的主题文件也上传到github。但是，由于themes下主题内部也存在git仓库，造成了git嵌套，自动被git忽略，所以要先着手解决git嵌套问题。

研究了一会，目前一种简单的办法，把themes内部的`.git`本地仓库删掉，这里要注意将next文件夹移出blog，然后移入，才能重新被git发现。这样我们就实现了themes主题配置的保存。

### 执行hexo命令报错（deepin）

执行`hexo s`命令报错：`Error: ENOSPC: no space left on device,`
大概是指监听太多node_modules 嵌套了：max_user_watches，谷歌搜到相关[答案](https://github.com/npm/npm/issues/1131),最终的解决办法：[增大最大监听数量](https://github.com/guard/listen/wiki/Increasing-the-amount-of-inotify-watchers)
由于我是deepin系统，命令行执行：
`echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p`

### npm install安装环境报错（deepin）

报错：
`pngquant-bin@4.0.0 postinstall: node lib/install.js`
`Failed at the pngquant-bin@4.0.0 postinstall script.`
报错大概是指pngquant-bin这个模块运行出错。谷歌搜索答案，得知pngquant-bin在Linux下需要一些包libpng-dev，但是仅仅安装这个包是不够的。
具体参考：
[npm.js包pngquant-bin](https://www.npmjs.com/package/pngquant-bin)
[node_modules/pngquant-bin/vendor/pngquant` binary doesn't seem to work correctly](https://github.com/imagemin/pngquant-bin/issues/78)
[pngquant failed to build, make sure that libpng-dev is installed](https://blog.csdn.net/zgrbsbf/article/details/81911999)

解决方案：
运行以下命令
```
sudo apt-get install libpng-dev
sudo npm install -g pngquant-bin
```
一般而言以上命令就可以解决了，但是有时候pngquant-bin安装报错，就尝试一下命令：
`sudo apt-get install -y build-essential libpng-dev`
如过还失败，再尝试：
```
sudo apt-get update 
sudo apt-get install libtiff5-dev libtiff5 libjbig-dev
```