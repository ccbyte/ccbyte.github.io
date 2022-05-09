---
title: "axios常用参数"
date: 2021-11-03T10:15:23
tags: ["JavaScript"]
---


## 下载进度
```js
axios.create({ // 下载文件
    baseURL: process.env.VUE_APP_EPUB_URL,
    method: 'get',
    responseType: 'blob',
    timeout: 180 * 1000, // 3分钟超时
    onDownloadProgress: progressEvent => { // axios监听下载进度
        if (onProgress) onProgress(progressEvent)
    }
}).get(`www/abc.xlsx`)
```

## 取消请求
