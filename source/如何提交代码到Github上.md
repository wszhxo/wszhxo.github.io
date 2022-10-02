---
title: 如何提交代码到Github上 ?
abbrlink: 1888452186
categories:
  - 配置
---




# 如何提交代码到Github上
<!--more-->

## 上传项目

- 本地新建文件夹,右键git bash here,前提你新建了GitHub仓库

- git init

- git status,查看状态此时是红色的

- git add --all不行的话就git add -A  (添加该文件夹所有东西到GitHub)

- git commit -m "第一次提交,图书crud,图书类别crud"  ,上传说明

- git remote add origin https://github.com/wszhxo/SSM-books-management-system.git(关联到GitHub仓库)

- git push -u origin master

- 上传到GitHub 完成





## 更新

### 第一种方法

- git status   

- git add -A   

- git commit -a -m "first commit"  



### 第二种方法

- git status

- git pull origin master + 仓库地址

- 删除本地文件

- git commmit -m “更新说明”

- git push origin master



