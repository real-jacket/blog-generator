---
title: 在deepin中安装node与npm
date: 2018-08-16 11:28:26
tags: deepin系统相关
---
最近装了deepin15.6，想安装http-server服务，结果报错，原来是npm版本太老，以下罗列几种node安装方式，这几种方式适用Linux/ubantu/deepin系统。
## 1、官方途径：通过包管理器方式安装；
node8版本：
>curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs

node10版本：
>curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
参考[官方教程](https://nodejs.org/zh-cn/download/package-manager/),但是很奇怪，会报错，猜测deepin系统的原因。
## 2、通过源码编译安装
在[node官网下载专区](https://nodejs.org/en/download/)找到系统对应的版本，鼠标右键复制下载链接。在终端中输入：
1.下载node
>wget https://nodejs.org/dist/v8.11.4/node-v8.11.4-linux-x64.tar.xz 

2.解压文件
>tar -xvf node-v8.11.4-linux-x64.tar.xz 

3.切换并查看node所在路径 
>cd node-v8.11.4-linux-x64/bin 
pwd

4.查看node版本
>node -v

5.将node和npm设置为全局(注意路径为第3步的路径)
>sudo ln /home/ubuntu/node-v8.11.4-linux-x64/bin/node /usr/local/bin/node
sudo ln /home/ubuntu/node-v8.11.4-linux-x64/bin/npm /usr/local/bin/npm
pwd

这种方法配置的环境容易出问题，在安装http-server时依然报错。
## 3、使用自带的apt安装
1.安装node与npm
>sudo apt install nodejs-legacy
sudo apt install npm

2.安装管理nodejs本身工具，n模块
>sudo npm install -g n

3.升级node到制定版本，后面接版本号
>sudo n latest //最新版本
sudo n stable //稳定版本
sudo n lts //长期支持版本

4.升级npm到最新版本
>sudo npm install npm@latest -g

此种方法便于管理node版本。
## 总结
经过测试，第三种方法在deepin上成功安装，目前没有遇到什么问题，推荐使用此种方法。
