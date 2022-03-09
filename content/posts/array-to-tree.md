---
title: "二维数组和树"
date: 2021-11-03T10:15:23
tags: ["JavaScript"]
---

## 树 -> 数组

当我们收到如下一棵树，需要转成一维数组时

```js
const nav = [
  {
    id: 1,
    subitems: [
      {
        id: 2,
        subitems: [
          {
            id: 3,
            subitems: [],
          },
          {
            id: 4,
            subitems: [],
          },
        ],
      },
      {
        id: 5,
        subitems: [],
      },
    ],
  },
  {
    id: 6,
    subitems: [],
  },
];
```

```js
export function flatten (array) {
    // return array.map(item => [].concat(item, ...item.subitems))// 只能2级
    const arr2d = array.map(item => [].concat(item, ...flatten(item.subitems)))// 多级（转成二维数组）
    return [].concat(...arr2d)// 二维转一维
}
```
> 方便我们在第一级就拿到所有元素

## 数组 -> 树
当我们收到如下一维数组，需要转成一棵树时

```js

[
  { "id": 19, "pid": 0, "title": "幼儿发展检核表" },
  { "id": 20, "pid": 0, "title": "阶段测评" },
  { "id": 21, "pid": 0, "title": "感觉统合能力发展" },
  { "id": 22, "pid": 19, "title": "1.5岁" },
  { "id": 23, "pid": 19, "title": "2岁" },
  { "id": 24, "pid": 20, "title": "A" },
  { "id": 25, "pid": 20, "title": "B" },
  { "id": 26, "pid": 21, "title": "前庭功能" },
  { "id": 27, "pid": 21, "title": "触觉功能" },
  { "id": 28, "pid": 21, "title": "本体觉功能" },
  { "id": 29, "pid": 22, "title": "艺术感知" },
  { "id": 30, "pid": 22, "title": "生活习惯" },
  { "id": 31, "pid": 22, "title": "健康运动" },
  { "id": 32, "pid": 22, "title": "情绪情感" },
  { "id": 33, "pid": 22, "title": "认知探索" },
  { "id": 34, "pid": 22, "title": "语言发展" },
  { "id": 35, "pid": 24, "title": "艺术感知" },
  { "id": 36, "pid": 24, "title": "生活习惯" },
  { "id": 37, "pid": 24, "title": "健康运动" },
  { "id": 38, "pid": 24, "title": "情绪情感" },
  { "id": 39, "pid": 24, "title": "认知探索" },
  { "id": 40, "pid": 24, "title": "语言发展" },
  { "id": 41, "pid": 25, "title": "语言发展" },
  { "id": 42, "pid": 25, "title": "认知探索" },
  { "id": 48, "pid": 23, "title": "生活习惯" },
  { "id": 49, "pid": 23, "title": "健康运动" }
]
```


```js

export function formatTreeData(orgList) {
    const treeList = []

    for (let i = 0, len = orgList.length; i < len; i++) {
        const item = queryChildren(orgList[i], orgList)

        treeList.push(item)
    }

    return treeList.filter(item => !item.pid)

    function queryChildren(parent, list) {
        const children = []

        for (let i = 0, len = list.length; i < len; i++) {
            if (list[i].pid === parent.id) {
                const item = queryChildren(list[i], list)

                children.push(item)
            }
        }

        if (children.length) {
            parent.children = children
        }

        return parent
    }
}
```
> 适用于一些需要树形结构的情况（如级联选择）