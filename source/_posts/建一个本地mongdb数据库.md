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
<!-- more -->

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

虽然个人喜欢用命令行进行安装，因为这样方便简单，但是用命令行查看数据库就比较不方便了毕竟，查询条件多且复杂的话，命令行界面就不够友好了，这里介绍使用robo 3T，一个mongdb数据库可视化GUI工具。

### 安装

1、首先在[robo 3T官网](https://robomongo.org/download)下载相应安装包,然后使用tar命令进行解压：

```
tar -xzf robo3t-1.2.1-linux-x86_64-3e50a65.tar.gz
```

2、创建一个命令将所有文件全部拷贝过去

```
sudo mkdir /usr/local/bin/robomongo

sudo mv robo3t-1.2.1-linux-x86_64-3e50a65.tar.gz /usr/local/bin/robomongo
```

3、进入文件robomongo文件目录，给robo 3T添加权限

```
cd /usr/local/bin/robomongo/bin

sudo chmod +x robo3t
```

4、给robo 3T创建快捷启动命令

```
sudo vi ~/.bashrc // 用vim编辑.bashrc文件

alias robomongo="/usr/local/bin/robomongo/bin/robo3t" // 在最后一行添加该命令,保存退出

source ~/.bashrc // 使命令生效

```

### 连接mongdb数据库

1、首先按文章前面说的运行数据库，本地数据库运行：

```
sudo service mongod start

mongo
```

2、启动robo 3T进行连接

```
robomongo
```

3、输入i地址与端口连接指定数据库
可以连接远程数据库


## 基本的数据库操作

## 遇到的问题