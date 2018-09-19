---
layout: post
title: '建立个人网站'
subtitle: ''
date: 2018-08-17
categories: 网站
cover: ''
tags: 建站
---
## 安装Ruby
jekyll使用Ruby编写的,所以首先需要安装Ruby编程语言

网址:https://rubyinstaller.org/downloads/

最好选带有Devkit,免得单独安装,安装的时候有一个Add to path选项,就是添加到环境变量,最好勾选,免得自己还要去添加

Ruby编程语言发行版本是托管在github上的,下载速度非常慢,我复制链接使用迅雷速度很快

## 测试ruby是否安装成功
在命令行中使用ruby --version


## 更换包的源
gem的源默认是国外的,源就类似于软件商店服务器的意思,从国外的软件商店下载比较慢,需要更换为国内的软件商店,我更换为淘宝的源

命令:gem source -l

gem sources --remove http://rubygems.org

gem sources --a https://ruby.taobao.org


## 安装Jekyll和bundle
命令
gem install jekyll
gem install bundle



## 验证jekyll是否安装成功
jekyll --version

## 注册github账号
有的可以忽略,然后建立一个仓库名字取

`用户名.github.io`



## 安装git或安装集成了git的工具
我推荐安装集成了git的工具,比如我用的这一框软件Cmder就非常好用,里面自带了很多Liunx的工具,包括git


## 生成网站
jekyll new 名字

如:

jekyll new my_blog



## 预览网站
进入到生成的网站
如:

`cd my_blog`

运行命令

`jekyll serve`打开浏览器预览

## 上传到github
新建文件夹克隆github仓库
git clone 地址

将生成的所有文件复制到克隆的仓库

运行命令将复制的文件添加到仓库

git add .

提交更改

git commit -a -m "说明"

最后推送

git push origin master

推送的时候会提示你输入github的用户名和密码

