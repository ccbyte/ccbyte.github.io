---
title: 'Taro/小程序中的页面传参'
date: '2021-11-12T12:10:10'
tags: ["Taro", "Vue.js", "Miniprogram", "JavaScript"]
---
# Taro/小程序中的页面传参
方法一：使用小程序跳转功能提供的传参方式（navigateTo, navigateBack）。  
方法二：使用[vuex](https://v3.cn.vuejs.org/guide/migration/introduction.html#vuex)方法。    
方法三：使用第三方封装好的Router组件（[vue-taro-router](https://taro-ext.jd.com/plugin/view/607f975f2ae2b4ed20b8f289)）。

本篇主要记录下前两种方法。

∵ 第二种方法有局限性，比如在多级页面需要控制数据何时传输的情况，如下两种情况的A/B/C页面：    

```选择教师负责班级C -> 编辑教师信息B -> 教师信息A```    
C通知B所选择的班级，通过修改store数据的话会导致直接修改到A的数据，C页/B页的保存按钮就没意义了。

```幼儿某项活动记录C -> 幼儿更多活动记录B -> 幼儿活动记录A```  
如果C中某项记录删除/修改了，更新B页，同时也要更新到A页，修改store是最方便的。

∴ 存在需要控制数据和不需要控制数据的情况，两种方法结合使用可能比较好。

另外，react版都有类似的方法（[redux](https://redux.js.org/introduction/getting-started)，[tarojs-router-next](https://taro-ext.jd.com/plugin/view/5f64832f0dd8313026e0942b)）

## 当前页往目标页传参
```js
Taro.navigateTo({
    url: `../questions/index?id=${id}`, // 由于没类似params/query，手动拼接
})
```

## 当前页往历史页传参
使用```Taro.getCurrentPages()```可以获得页面栈，栈顶```pages[pages.length - 1]```就是当前页。

获取到页面实例就可以在**上一页**用setData申明好数据/方法，在**当前页**需要时调用更新**上一页**数据。

```js
// 一般用的就是当前页和上一页之间传参，工具类里面封装好两个方法。
export function getCurrentPage() { // 当前页
  const pages = Taro.getCurrentPages();
  return pages[pages.length - 1];
}

export function getPrevPage() { // 上一页
  const pages = Taro.getCurrentPages();
  if (pages.length > 1) {
    return pages[pages.length - 2];
  }
  return undefined;
}
```
历史页面
```js
let currentPage = getCurrentPage() // 获取当前页实例
currentPage.setData({ // 提供公共方法/数据
    setClassList: (list) => {
        this.classList = list // 传进list保存到当前页classList中
    }
})
```

当前页
```js
let list = []
let prevPage = getPrevPage() // 获取上一页实例
prevPage.data?.setClassList(list)  // 调用公共方法/数据
Taro.navigateBack() // 返回上一页
```
