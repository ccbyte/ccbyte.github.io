---
title: "时间处理库date-fns的常见应用"
date: 2021-07-25 20:13:03
---
# 时间处理库Date-fns (v2.25.0)
这个库功能十分齐全，可能需要科学上网，这里记录一下之前做项目中常用的方法。
```shell
npm i date-fns
```     
## [📚](https://date-fns.org/v2.25.0/docs/format)格式化(format)
``` js
import { format } from 'date-fns'
format(new Date(), 'yyyy-MM-dd HH:mm:ss') // <String> 2021-07-25 20:13:03
```

## [📚](https://date-fns.org/v2.25.0/docs/intervalToDuration)日期差(intervalToDuration)
### 根据出生日期得到几岁
```js
import { intervalToDuration } from 'date-fns'
let birthday = new Date('1997-07-01')
const { years, months, days } = intervalToDuration({
    start: new Date(birthday),
    end: new Date()
}) // year即年龄，months和day可以再精确到天
```

## [📚](https://date-fns.org/v2.25.0/docs/differenceInMinutes)时间差(differenceInMinutes)
### 比较开始时间和结束时间
```js
if(differenceInMinutes(formData.end, formData.start) < 0) {
    console.log('开始时间小于结束时间')
}
```

## [📚](https://date-fns.org/v2.25.0/docs/I18n)本地化/I18n(locale)
只有format相关函数支持
```js
import format from 'date-fns/format'
import { zhCN } from 'date-fns/locale'
format(new Date(), "yyyy'年'M'月'", {locale: zhCN}) // 单个M在1~9月不补0
```

## [📚](https://date-fns.org/v2.25.0/docs/addMonths) 日期计算(addDays, subDays, addMonts, subMonth...)
这里太多了，还有[📚](https://date-fns.org/v2.25.0/docs/addWeeks)addWeeks等等。
### 获取当前周开始日期和结束日期
```js
import { format, addDays, subDays, addMonths } from 'date-fns'
let weekDiff = new Date().getDay()
let endDate = format(addDays(nowDate, 7 - weekDiff), 'M.d')
let startDate = format(subDays(nowDate, weekDiff - 1), 'M.d')
console.log(`${startDate}~${endDate}`) // 7.19~7.25
```

## [📚](https://date-fns.org/v2.25.0/docs/isValid)判断时间是否规范
不是Date对象的话，该方法会再用[📚](https://date-fns.org/v2.25.0/docs/toDate)toDate方法进行转换，还是转不了就返回false。

<br/>
……
<br/><br/>

## 日期的小细节
### IOS上日期无效问题
```new Date('2021-7-25 20:13:03')``` 在IOS会报错，而在安卓上可以，new Date('2021-7-25')则都可以。

原因是因为该日期格式不符合ES5(2019)新增的规则，如果想继续使用需要进行手动转换，或者学习新的[ECMA规则](https://262.ecma-international.org/5.1/#sec-15.9.1.15)。

📝 手动转换
```js
**
 * 将日期字符串转成标准日期(ios上无法识别格式：yyyy-MM-dd HH:mm:ss)
 * @param {String} str
 * @returns {Date} 标准日期
 */
export const parseDate = (str) => {
  var parts = str.split(" ");
  var dateparts = parts[0].split("-");
  var timeparts = (parts[1] || "").split(":");
  var year = +dateparts[0];
  var month = +dateparts[1];
  var day = +dateparts[2];
  var hours = timeparts[0] ? +timeparts[0] : 0;
  var minutes = timeparts[1] ? +timeparts[1] : 0;
  var seconds = timeparts[2] ? +timeparts[2] : 0;
  // return new Date(Date.UTC(year, month - 1, day, hours, minutes, seconds));
  return new Date(year, month - 1, day, hours, minutes, seconds);
}
```

[📝参考资料](https://stackoverflow.com/questions/13363673/javascript-date-is-invalid-on-ios)