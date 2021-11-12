---
title: 自定义小程序checkbox样式'
date: '2021-07-12T'
tags: ["Miniprogram", "Scss", "Css"]
---
# 自定义小程序原生checkbox样式
由于项目有设计稿，要精确还原设计稿的话，本来是打算自己写一个，写是写了，大概原理就是
1. 隐藏原来的checkbox；
2. 做自己的checkbox样式（比如一个圆圈⚪），通过:active选择器触发样式的更改；
3. 内部元素（比如一个对勾✔），通过伪元素来添加；

后来网上找了一下发现，微信的实现貌似也是类似啊，我直接用下面代码覆盖它的就行了。
```scss
  /*checkbox 整体大小  */
  checkbox {
  }
  /*checkbox 选项框大小  */
  checkbox .wx-checkbox-input {
    width: 40rpx;
    height: 40rpx;
    border-radius: 50%;
    border: 3px solid #ccc;
  }
  /*checkbox选中后样式  */
  checkbox .wx-checkbox-input.wx-checkbox-input-checked {
    border: 3px solid #8dc21f;
    background: #8dc21f;
  }
  /*checkbox选中后图标样式  */
  checkbox .wx-checkbox-input.wx-checkbox-input-checked::before {
    width: 28rpx;
    height: 28rpx;
    line-height: 28rpx;
    text-align: center;
    font-size: 26rpx;
    color: #fff;
    background: transparent;
    transform: translate(-50%, -50%) scale(1);
    -webkit-transform: translate(-50%, -50%) scale(1);
  }
```