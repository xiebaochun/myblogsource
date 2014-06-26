---
layout: post
title: "关于octopress建立博客的若干问题"
date: 2013-09-05 17:38
comments: true
categories:
---
#### 最近在搞octopress的博客真是是快要崩溃了，对于不懂ruby的我来说确实遇到了很多的问题

##第一：就是master 和source代表什么意思
master 和source其实是github仓库的分支，master是github默认的一个主分支，而source是自己添加的一个分支（其目的是用进行博客的备份的)

##第二：rake generate和rake deploy
rake generate是将source/post中的markdown文件与某些关联文件（i.gyml）解析成标准的html格式的文件.rake deploy是将接卸生成的网页文件部署到你的github中仓库中master分支上。

##第三：merge conflic的问题 
有时候在rake deploy或者git push的时候会出现推送优先级的问题，就是在上执行了git add .但是有没有push成功，上一次的推送任务还会存在service中，那么这次你又执行了push那么就发生了冲突，解决这个冲突，可以使用git pull将上一次的推送和这次的推送任务打包一起push上去，这么在执行rake deploy或者git push就不会有冲突了。

##第四：解决中文问题
在默认情况下，如果你在markdown文件中编辑中文文档，那么rake generate的时候会出错，因为win7的编码格式是GBK,要想解决这个问题，首先在win7的系统变量添加LANG=zh_CN.UTF-8和AL_ll=zh_CN.UTF-8这两个键值对，然后在ruby安装目录下找到什么相关文件进行修改，具体的网上很多。然后就可以generate成功了。