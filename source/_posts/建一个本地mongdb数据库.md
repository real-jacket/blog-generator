---
title: 建一个本地mongdb数据库
date: 2019-01-09 11:55:02
tags: mongdb
categories:
- node
- 数据库
---

## 安装mongodb数据库

### 安装包安装

进入[官网](https://www.mongodb.com/cn)，点击左上角Get MongoDB下载，进入中间的server界面，会自动匹配出对应的系统，下载对应的安装包，安装即可。
如果想看更详细的安装步骤，下载旁的侧边栏有Install Instructions。

### 命令行安装

由于我是deepin系统，是基于debian的，而且个人更喜欢用命令行进行安装,于是找到了[debian的安装指导](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-debian/#install-mongodb-community-edition)

1. 导入mongodb 公共 GPG 密钥
   ```
  sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
  ```

2. 创建列表文件
  deepin是基于最新的debian，所以我这个是Debian 9的 
  ```
  echo "deb http://repo.mongodb.org/apt/debian stretch/mongodb-org/4.0 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list
  ```
  
3. 更新一下本地安装包数据
  `sudo apt-get update`

    >注意：此步不可省略，不然会导致报错，找不到mongodb包！

4. 安装mongoDB 包
  `sudo apt-get install -y mongodb-org`

至此，mongodb数据库算是安装成功了。官方文档还介绍了使用.tgztr包安装的方式，感兴趣的可以去官网看看。

## 运行mongodb数据库

- 启动mongodb
  `sudo service mongod start`

- 检验mongodb是否启动成功
  `sudo cat /var/log/mongodb/mongod.log`
  这个文件纪录着mongodb的启动历史，所以当我们在最后一行看到一下内容，便是启动成功了
  `[initandlisten] waiting for connections on port 27017`

- 运行mongodb
  `mongo`
  其实这个也可以作为mongodb是否启动成功的条件

- 停止mongodb运行
  `sudo service mongod stop`

- 重启mongodb
  `sudo service mongod restart`

## robo 3T -mongodb可视化工具

## 基本的数据库操作

## 遇到的问题