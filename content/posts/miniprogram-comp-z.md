---
title: '小程序原生组件层级过高的解决方法'
date: '2021-08-26T18:27:00'
tags: ["Miniprogram", 'Css', "JavaScript", 'Vue.js']
---
# 原生组件层级过高问题
例如input, textarea, Canvas，如果要在页面上加弹层，且页面上有这些元素的话这些元素会高于其他包括遮罩overlay，因为原生小程序的层级最高。

# 解决方法
## cover-view
没太大用，能嵌套的内容有限。

## 手动控制
在某个需要的地方添加该样式，再弹窗时通过vuex动态控制微信原生组件的状态。
```scss
&.wechat-comp-disabled {
    .at-textarea { pointer-events: none; }
    .at-input { pointer-events: none; }
    .canvas { pointer-events: none; }
    // 也可改为display: none;看效果而定
}
```