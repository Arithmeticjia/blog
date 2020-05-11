---
title: MacOS下Git与GitHub
date: 2019-11-24 20:34:30
tags: [git, github]
category: Git
---
还是不知道写啥，本文是有关Mac OS上Git与GitHub相关配置的介绍说明
<!-- more -->
#### 安装git
该命令也可以升级最新的git
```shell script
brew install git
```
#### 查看git指向和版本信息
```shell script
which git
/usr/bin/git
git --version
git version 2.21.0 (Apple Git-122.2)
```
#### 配置github用户名和邮箱
```shell script
git config --global user.name "ArithmeticJia"
git config --global user.email "1097197237@qq.com"
```
#### 生成ssh密钥
```shell script
ssh-keygen -t rsa -C "1097197237@qq.com"
```
```shell script
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/Arithmetic/.ssh/id_rsa): 
/Users/Arithmetic/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/Arithmetic/.ssh/id_rsa.
```
#### 在GitHub上新增ssh密钥
复制密钥
```shell script
cat .ssh/id_rsa.pub
```
路径一般是
```shell script
/User/ArithmeticJia/.ssh
```
登录GitHub（默认你已经注册了GitHub账号），添加ssh key，点击Settings，如图
![](https://www.guanacossj.com/media/articlebodypics/E901D42E-753A-4E46-BC71-05C54C555B0D.png)
#### 链接验证
```shell script
ssh -T git@github.com
```
```shell script
Hi Arithmeticjia! You've successfully authenticated, but GitHub does not provide shell access.
```
