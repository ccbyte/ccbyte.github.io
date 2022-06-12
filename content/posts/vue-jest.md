---
title: "在Vue3中使用Jest进行单元测试"
date: 2022-05-09T23:24:57
---

# JEST
## 安装
`yarn add --dev jest`

## 配置
package.json -> scripts -> "test": "jest"

## 使用
```js
export
```

## 建立测试目录
foo.spec.js
```js
import
```

## Jest不支持ESM的问题
直接使用会报错：(因为Jest默认是node下的不支持ESM)
`SyntaxError: Cannot use import statement outside a module`

### 配置Babel（ESM转为CMJ）
```js
// babel.config.js
module.exports = {
    presets: [['@babel/preset-env', {targets: {node: 'current'}}]],
};
```

### 配置Nodejs


webpack/vite/rollup打包工具处理支持Jest使用ESM
