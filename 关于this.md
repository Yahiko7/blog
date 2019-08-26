## this是什么？
面向对象语言中 this 表示当前对象的一个引用。
但在 JavaScript 中 this 不是固定不变的，它会随着执行环境的改变而改变。this的指向取决于函数的调用方式，谁调用它就指向谁。
## 如何判断this ？
### 默认绑定 -- 独立函数调用
指向全局window或者undefined。严格模式（strict mode）， this为undefined，非严格模式下，this为window。

demo1：
```
function fn(){
	console.log(this)
};
fn();

< Window
```
demo2：
```
var obj = {
	name: 'test',
	say: function(){
		console.log(this.name)
	}
};
var fn = obj.say;
fn();

< undefined
```
严格模式
```
"use strict";
function fn(){
	console.log(this)
};
fn()

< undefined
```
### 隐式绑定 --- 作为方法属性被调用
```
var obj = {
	name: 'test',
	say: function(){
		console.log(this.name)
	}
};
obj.say();

< test
```
### 显式绑定 --- call、apply、bind
this指向call、apply、bind传入的对象。
```
var obj1 = {
	name: 'test1',
	say: function(){
		console.log(this.name)
	}
};
var obj2 = {
	name: 'test2'
};
obj1.say.call(obj2);

< test2
```
### es6 箭头函数
this 指向取决于外层的作用于，下面的例子中obj的外层是window，所以打印出test。
```
window.name = 'test'
var obj = {
	name: 'test1',
	say: () => {
		console.log(this.name)
	}
};
obj1.say()

< test
```
### new绑定
this指向new所创建的对象，当使用new来调用函数，会执行以下操作。
1. 创建一个全新的对象
2. 这个新对象会被执行[[原型]]连接
3. 执行函数操作，函数中的this会被绑定到这个新对象
4. 如果函数没有返回其他值，那么new表达式中的函数调用会自动返回这个新对象