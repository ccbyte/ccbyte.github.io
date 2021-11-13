---
title: "You Don't Know JS阅读笔记"
date: 2021-11-10T12:01:50
tags: ['JavaScript']
---
# YDKJS Note
在阅读[YDKJS](https://github.com/getify/You-Dont-Know-JS)复习JS时做的一些摘录&笔记，只记录一些重要部分。
# 进入JavaScript
入门与进阶第二章
## 值与类型
- string
- number
- boolean
- null 和 undefined
- object
- symbol （ES6新增类型）
## 值的比较
### Truthy与Falsy
在JavaScript中“falsy”的明确列表如下：
- "" （空字符串）
- 0, -0, NaN （非法的number）
- null, undefined
- false
  
任何不在这个“falsy”列表中的值都是“truthy”，例如：
- "hello"
- 42
- true
- \[ \], [ 1, "2", 3 ] （数组）
- { }, { a: 42 } （对象）
- function foo() { .. } （函数）

### 类型强制转换
```js
let a = "42"
// 显式
let b = Number(a);
// 隐式
let b = a * 1;
// 两者结果都是
a;                // "42" 字符串
b;                // 42   数字
```

### 等价性中的强制转换
📕 准确描述     

 ```==```在允许强制转换的条件下检查值的等价性，而```===```是在不允许强制转换的条件下检查值的等价性。

<br/>
🍽️ 具体使用     

使用```==```大部情况都会进行合理的类型转换，不要怕，怕的话看下
[ECMA规定的```==```转换规则](https://262.ecma-international.org/5.1/#sec-11.9.3)。

可以总结为如下的情况避免```==```而使用```===```：
- 如果一个比较的两个值之一可能是**true或false值**。
- 如果一个比较的两个值之一可能是**这些具体的值（0，""，[ ]）**。

两个非基本类型值检查的是**引用**是否相同，而非**底层的值**是否相同。
```js
var a = [1,2,3];
var b = [1,2,3];
var c = "1,2,3";
// array默认情况下会通过使用逗号（,）连接所有值来被强制转换为string
a == c;		// true
b == c;		// true
a == b;		// false
```

在所有其他情况下，<u>使用```==```是安全的</u>，而且它可以简代码并改善可读性。

<br/>

🏷️提示&备注  
```!=``` 不等价形式对应```==```，而```!==```形式对应```===```，强制转换性对应适用。

<br/>

### 不等价性中的强制转换
```js
41 < 42 // true

"41" < 42 // true 字符串 转数字后 进行比较

41 < "foo" // false 其中foo被强制转换成了NaN无法参与比较 （但NaN!==NaN为true）
41 > "foo" // false
41 == "foo" // false

"axx" < "bxx" // true 字符串直接 按字母序 进行比较（ASCII）
```

## 函数作为值

### 立即被调用的函数表达式（IIFE）
∵ 函数可以创建变量 作用域   
∴ 以这样的风格使用一个IIFE经常被用于定义变量，而这些变量将不会扩散到IIFE外面
```js
// 带返回值的IIFE
var x = (function IIFE(){
	return 42;
})();

x;	// 42
```
### 闭包
📕 描述  
即使函数已经完成了运行，它依然可以“记住”并持续访问函数的作用域的一种方法。

<br/>
🍽️ 使用

```js
function makeAdder(x) {
	// `x` 是一个内部变量
	function add(y) { // 内部函数 `add()` 
		return y + x; // 使用 x，所以它对 x 拥有一个“闭包”（记得外部给进来的x）
	};

	return add;
}
// `plusOne` 得到一个指向内部函数 `add(..)` 的引用
var plusOne = makeAdder( 1 );

// `plusTen` 得到一个指向内部函数 `add(..)` 的引用
var plusTen = makeAdder( 10 );

plusOne( 3 );		// 4  <-- 1 + 3
plusOne( 41 );		// 42 <-- 1 + 41

plusTen( 13 );		// 23 <-- 10 + 13
```

这段代码的工作方式是：
1. 申明plusOne(..)：调用makeAdder(1)时，得到一个指向**它内部的add(..)的引用**，它记住了x是1。
2. 申明plusTen(..)：调用makeAdder(10)时，得到了另一个指向**它内部的add(..)引用**，它记住了x是10。
3. 调用plusOne(3)：它在3（它内部的y）上加1（被x记住的），得到结果4。
4. 调用plusTen(13)：它在13（它内部的y）上加10（被x记住的），得到结果23。

#### 模块（闭包的常见应用）
实现用户登录逻辑。
```js
function User(){
	var username, password;
	function doLogin(user,pw) {
		username = user;
		password = pw;
		// 做登录的工作
	}
	var publicAPI = {
		login: doLogin
	};
	return publicAPI;
}
// 创建一个 `User` 模块的实例
var fred = User(); // 创建一个新作用域，不需要new。
fred.login( "fred", "12Battery34!" );
```

## this标识符
```js
function foo() {
	console.log( this.bar );
}
var bar = "global";
var obj1 = {
	bar: "obj1",
	foo: foo
};
var obj2 = {
	bar: "obj2"
};
// -------
foo();				// "global"
obj1.foo();			// "obj1"
foo.call( obj2 );	// "obj2"
new foo();			// undefined
```
关于this如何被设置的四个规则：
1. foo()
   1. 非strict模式中，this设置为全局对象
   2. 在strict模式中，this将会是undefined而且你会在访问bar属性时得到一个错误
   3. 所以this.bar的值是global
2. obj1.foo()
   1. 将this设置为对象obj1。
3. foo.call(obj2)
   1. 将this设置为对象obj2。
4. new foo()
   1. 将this设置为一个新的空对象。

## 原型


## 非JavaScript
如浏览器中的document全局变量，alert()方法，console.log()等

## 填补和转译
填补（Polyfilling）：兼容旧版本（写旧版本的实现方式）   
```js
if (!Number.isNaN) {
	Number.isNaN = function isNaN(x) {
		return x !== x;
	};
}
```
转义（transpiling）
```js
function foo(a = 2) {
	console.log( a );
}
foo();		// 2
foo( 42 );	// 42
```
常用的转义器：  
Babel (https://babeljs.io) (前身为 6to5): 将 ES6+ 转译为 ES5    
Traceur (https://github.com/google/traceur-compiler): 将 ES6，ES7，和以后特性转译为 ES5

# 什么是作用域？
作用域与闭包第一章

## 编译器理论
编译的三个步骤：
1. 分词/词法分析：将一连串字符打断成有意义的<font color=red>**token（记号）**</font>；
2. 解析：将token流（数组）转换成一个嵌套元素的树，即<font color=red>**AST（抽象语法树）**</font>（Abstract Syntax Tree）；
3. 代码生成：将抽象语法树转成<font color=red>**可执行代码**</font>（视平台而定）。

## 引擎 编译器 作用域
1. 引擎：负责**编译和执行**JavaScript程序；
2. 编译器：处理所有的解析和**代码生成**；
3. 作用域：收集并维护一张所有被声明的**标识符（变量）列表**，并按规则限制当前执行中的代码如何访问这些变量。

## LHS RHS
编译器术语