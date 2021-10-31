---
date: 2018-04-09
title: "Git使用基础"
description: "记录一下基本的Git使用方法"
tags: ["git"]
categories: ["git"]
---
# Git使用基础
## 本地仓库初始化

## 管理远端仓库
### 添加远端仓库  
🌳基本命令
```
git remote add
```   
🌰例子
```
$ git remote add origin https://github.com/user/repo.git
# Set a new remote

$ git remote -v
# Verify new remote
> origin  https://github.com/user/repo.git (fetch)
> origin  https://github.com/user/repo.git (push)
```
🏹问题解决

```
fatal: remote origin already exists.
```
方案一：重命名与远端的链接后重新链
```
$ git remote -v
# 查看远端链接信息
> origin  https://github.com/OWNER/REPOSITORY.git (fetch)
> origin  https://github.com/OWNER/REPOSITORY.git (push)

$ git remote rename origin destination
# 把远端名由 'origin' 改成 'destination'（不会改仓库名）

$ git remote -v
# 验证生效否
> destination  https://github.com/OWNER/REPOSITORY.git (fetch)
> destination  https://github.com/OWNER/REPOSITORY.git (push)
```
方案二：删除与远端的链接后重新链（推荐）
```
$ git remote -v
# 查看当前远端
> origin  https://github.com/OWNER/REPOSITORY.git (fetch)
> origin  https://github.com/OWNER/REPOSITORY.git (push)
> destination  https://github.com/FORKER/REPOSITORY.git (fetch)
> destination  https://github.com/FORKER/REPOSITORY.git (push)

$ git remote rm destination
# 删除远端（不会删除仓库，只是删除链接）
$ git remote -v
# 验证生效否
> origin  https://github.com/OWNER/REPOSITORY.git (fetch)
> origin  https://github.com/OWNER/REPOSITORY.git (push)
```
最后重新 ```add origin``` 就ok了
<br/>

> Reference     
> [Managing remote repositories](https://docs.github.com/en/get-started/getting-started-with-git/managing-remote-repositories)