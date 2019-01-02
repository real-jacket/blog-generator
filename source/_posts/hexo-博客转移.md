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

## 通过github保存配置文件

基本思路：将本地所有相关配置文件上传到github，然后clone到另一台电脑或者另一个系统，相关的配置文件都有了，理论上来说就是ok的。这样唯一的好处，就是基本可以在所有电脑里写博客后直接发布了。

### 1、上传相关配置文件到github

主要是配置文件，由于我怕出问题，基本把所有的相关配置文件都上传了，我的目录如下：

```
scaffolds
source
themes
.gitignore
_config.yml
package-lock.json
package.json
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
>千万不要将相关的环境配置文件提交，比如package.json，不然会导致不同的电脑环境相互冲突，需要重新配置环境。

## 遇到的一个问题

### nodejieba模块安装出错

