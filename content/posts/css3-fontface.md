---
title: "CSS3引用自定义字体"
date: 2022-3-10T19:33:03
tags: ["CSS"]
---

## @font-face规则
使得不仅可以使用计算机上安装的字体。    
方法一：fonts目录（内含css+woff2组合）拷贝到public中；[下载地址：fonts.googleapis.com/css?family=字体名称]    
在index.html引用web字体（即引用它的css文件，注意加<%=BASE_URL=>）   
方法二：将fonts移动到src/assets目录下，在main.js中引用    
方法三：引入js中，可以动态切换，可以加载到iframe中

