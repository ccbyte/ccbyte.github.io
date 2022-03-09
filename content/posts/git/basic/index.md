---
date: 2018-04-09
title: "Git使用基础"
description: "记录一下基本的Git使用方法"
tags: ["git"]
categories: ["git"]
---
# Git使用基础
本篇以常用例子为主以点带面各个命令的使用，不具体写名各个方法的所有用途、参数等。
## 本地仓库初始化
```
git init
```
### 配置
```
git clone <git地址>
git remote -v
git rev-list --all -count
git config --list
git config --global user.username <用户名>
git config --global user.email <邮箱>
git config
# --local是对当前项目配置
```

## 分支
### 切换分支
```
$ git checkout 分支名称
> Switched to branch '分支名称'
```
### 新增分支
1.检查分支
```
# 本地
$ git branch 
* master

# 远程
$ git branch -r
* origin/HEAD -> origin/master
* origin/master
```
2.新建分支（并切换到该分支）
```
# 本地
$ git checkout -b dev_branch
> Switched to a new branch 'dev_branch'

# 远程
git push origin dev_branch:dev_branch
```

### 拉取分支
拉取分支（并切换到该分支）
```
# 远程
> git checkout -b 本地分支名 origin/远程分支名
```
### 删除分支
```
# 本地
> git branch --delete dev_branch

# 远程
> git push origin --delete dev_branch
```

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

# 把远端名由 'origin' 改成 'destination'（不会改仓库名）
$ git remote rename origin destination

# 验证生效否
$ git remote -v
> destination  https://github.com/OWNER/REPOSITORY.git (fetch)
> destination  https://github.com/OWNER/REPOSITORY.git (push)
```
方案二：删除与远端的链接后重新链（推荐）
```
# 查看当前远端
$ git remote -v
> origin  https://github.com/OWNER/REPOSITORY.git (fetch)
> origin  https://github.com/OWNER/REPOSITORY.git (push)
> destination  https://github.com/FORKER/REPOSITORY.git (fetch)
> destination  https://github.com/FORKER/REPOSITORY.git (push)

# 删除远端（不会删除仓库，只是删除链接）
$ git remote rm destination

# 验证生效否
$ git remote -v
> origin  https://github.com/OWNER/REPOSITORY.git (fetch)
> origin  https://github.com/OWNER/REPOSITORY.git (push)
```
最后重新 ```add origin``` 就ok了
<br/>

> Reference     
> [Managing remote repositories](https://docs.github.com/en/get-started/getting-started-with-git/managing-remote-repositories)

### 修改远端仓库
```
git remote set-url origin https://xxxxx.git
```

## Commit相关
### 撤回
1.获得版本号
```shell
git log --pretty=oneline
```
2.退回到上一个版本号
```
git reset --soft <版本号>
```
tips: 如果参数改成--hard不仅撤回还会清空掉之前commit的缓存（慎用）
