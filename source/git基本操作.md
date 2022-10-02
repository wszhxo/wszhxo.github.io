---
title: git基本命令
categories:
  - git
tags:
  - git
abbrlink: 3652837138
---

<!--more-->
# 

| -                         | -                                         |
| ------------------------- | ----------------------------------------- |
| git init                  | 添加版本控制                              |
| git add 文件名            | 添加到缓存区                              |
| git add .                 | 添加全部到缓存区                          |
| git commit -m "注释"      | 提交缓存区到本地仓库                      |
| git log --pretty==oneline | 查看历史版本(只显示之前)                  |
| git reset --hard 版本号   | 回退到之前的版本                          |
| git reflog                | 查看历史操作 (用于回退回退后无法查看之前) |
| git colne  gihub链接      | 克隆到本地                                |
| git push gihub链接        | 上传到github仓库(下班第一件事)            |
| git pull                  | 上班第一件事拉取远端代码                  |
| git branch                | 查看分支                                  |
| git checkout 分支名       | 切换分支                                  |
| git merge 分支名          | 把分支名和当前分支合并                    |
| git branch -d 分支名      | 删除分支(前提当前不是该分支)              |
| touch .gitignore          | 这个文件里,写了需要忽略提交的文件         |
|                           |                                           |
|                           |                                           |
|                           |                                           |
|                           |                                           |

### push 出现403 未认证,需要到.git/config文件中修改

### .gitignore的写法

- `/文件夹/`    :表示过滤这个文件夹
- `*.zip`  :表示过滤这个类型的文件
- `/文件夹/xx.txt`:表示过滤具体的文件
- `!xx.txt`:表示不过滤该文件
- `#`:用于注释





