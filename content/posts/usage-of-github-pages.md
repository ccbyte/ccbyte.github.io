---
title: "使用github的pages功能"
date: 2021-06-25T20:13:37+08:00
draft: false
---

## 使用github的pages功能

以搭建hugo博客为例

首先下载git，官网地址

在github新建仓库，仓库名为： 
```
你的用户名.github.io
```

初始化git
```
git init
```

把当前目录环境和显示目录合并
```
git remote add https://github.com/你的用户名/你的用户名.github.io.git
```
构建项目到docs目录
```
hugo -d docs
```

```
git add
git commit
git push origin master
```

在仓库setiing中找到pages，设置页面根目录为master分支下的docs目录

最后进入页面
https://你的用户名.github.io
