---
title: "实现简单的表单数据校验功能"
date: 2021-10-15T19:33:03
tags: ["javascript"]
---
# 实现表单数据校验功能
因为项目用的UI库功能太少，表单不具备校验功能，所以自己写了一个，只有一个文件。

## 使用
```js
import { required, email, useValidate } from 'validate'
let rules = {
    username: { name:'邮箱号', validate: [required, email] }, // name用于错误提示，validate是需要符合的校验规则列表
    password: { name:'密码', validate: [required] }
}
useValidate(formData, rules)
```

## 源文件
📝 validate.js
``` js
// 校验的主函数
export const useValidate = (formData, rules) => {
  return Promise.all(
    Object.entries(rules).map(([key, rule]) => {
      return Promise.all(
        rule.validate.map(func => func(formData[key]))
      ).catch((err) => {
        console.log(rule.name + err) // 错误提示（打印或弹窗）
        return Promise.reject()
      })
    })
  )
}

// 以下都是一些常见的校验方法
// 是否必填
export const required = (value) => {
  return isEmpty(value) ? Promise.reject('未填写') : Promise.resolve()
}

// 数字
export const number = () => {
  const reg = /^[0-9]*$/;
  if (!reg.test(value)) {
    return Promise.reject('应输入数字')
  } else {
    return Promise.resolve()
  }
}

// 邮箱
export const email = (value) => {
  const reg = /^[A-Za-z\d]+([-_.][A-Za-z\d]+)*@([A-Za-z\d]+[-.])+[A-Za-z\d]{2,4}$/;
  if (reg.test(value)) {
    Promise.resolve()
  } else {
    return Promise.reject('格式错误')
  }
}

// 大陆手机号
export const phone = (value) => {
  const reg = /^(?:\+?86)?1(?:3\d{3}|5[^4\D]\d{2}|8\d{3}|7(?:[235-8]\d{2}|4(?:0\d|1[0-2]|9\d))|9[0-35-9]\d{2}|66\d{2})\d{6}$/
  if (reg.test(value)) {
    return Promise.resolve()
  } else {
    return Promise.reject('格式错误')
  }
}

// URL
export const https = (value) => {
  const reg = /^((https?|ftp|file):\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$/;
  if (reg.test(value)) {
    return Promise.resolve()
  } else {
    return Promise.reject('格式错误')
  }
}

// 特殊字符
export const specialChar = () => {
  const regEn = /[`~!@#$%^&*()_+<>?:"{},.\/;'[\]]/im;
  const regCn = /[·！#￥（——）：；“”‘、，|《。》？、【】[\]]/im;
  if (regEn.test(value) || regCn.test(value)) {
    return Promise.reject('不能包含特殊字符')
  } else {
    return Promise.resolve()
  }
}

const isEmpty = (v) => { // 为空的逻辑，可以自己看需求实现
  switch (typeof v) {
    case 'undefined' : return true;
    case 'string' : if(v.trim().length == 0) return true; break;
    case 'boolean' : if(!v) return true; break;
    case 'number' : if(0 === v) return false; break;
    case 'object' :
      if(null === v) return true;
      if(undefined !== v.length && v.length==0) return true;
      for(var k in v) { return false; } return true;
      break;
    default: break;
  }
  return false;
};
```

