#ECMAScript 6

## ECMAScript和JavaScript的关系
javascript的创造者是Netscape公司，后来被提交给国际标准化组织ECMA。ECMA制定了标准后将它命名为EMCAScript。

ECMAscript是一个种规范，javascript是一种实现，这就是它们的关系

## let命令

### 基本用法
let命令与var是差不多的，但是let命令有块级作用域
```javascript	
	{
		let a = 10;
		var b = 1;
		console.log(b); // 2
	}
	console.log(a) //ReferenceError:a is no defined
	console.log(b) //1
```
用let声明的变量只能在声明的代码块中有效,它在for循环中可以完美表现出这个特性
```
	var arr = [];
	for(var i=0;i<5;i++){
		arr[i] = function(){
			console.log(i);
		}
	}
	arr[2](); //5 

	var arr2 = [];
	for(let j=0;j<5;j++){
		arr2[j] = function(){
			console.log(j);
		}
	}
	arr2[2](); //2
```
var声明的变量没有块级作用域，所以打印的i值每次都会被新的i值覆盖，而let声明的，因为存在块级作用域，所以它的i不受影响

### 没有变量提升
```javascript
	console.log(foo); //undefined
	console.log(bar); //报错ReferenceError
	var foo = 2;
	let bar = 2;
```
因为javascript是脚本语言，它存在变量提升的情况，但是用let声明的变量不存在这种情况

### 暂时性死区

```javscript
	var temp = 123;
	if(true){
		tem = 'abc'; //ReferenceError
		let tem;
	}
```
let声明的变量只能在本代码块内使用，并且不能在声明前使用，这种语法称为“暂时性死区”

### 不允许重复声明

```javascript
	function(){
		let a = 10;
		var a = 1;
	}
```
这样声明会直接报错，因为let允许在一个块级作用域内重复声明

## 块级作用域

### 没有块级作用域--内层变量可能会覆盖外层变量
```javascript
	var tmp = new Date();
		
	function f(){
		console.log(tmp);
		if(false){
			var tmp = "hello world";
		}
	}
	f();//undefined
```
因为tmp使用var声明，它会变量提升，并且因为不存在块级作用域的原因，tmp的值被覆盖，此时它的值为undefined；

### 没有块级作用域--用来计算的循环变量泄漏为全局变量
```javascript
	var s = 'hello';
	for(var i=0;i<s.length;i++){
		console.log(i);
	}
	console.log(i); //5
```
在这个循环中，i值使用完应该保留在循环内，但是真实中，它会暴露到全局作用域中，这不合理！

在ES6中，使用let和const来增加块级作用域。

## const命令

const声明一个<font color=red>只读</font>的变量。一旦声明，常量的值就不能改变。
```javascript
	const PI = 3.1415;
	PI //3.1415
	PI = 3; //TypeError:Assignment to constant variable	
```
const 一旦初始化就不能改变值，其他用法和let一样，不会变量提升，存在块级作用域

有一点需要注意的是，如果const定义的是一个对象，那么变量指向的是地址，指向的地址永远不会变，但是对象的属性是可以改变的。
```javascript
	const foo = {};
	foo.prop = 123;
	console.log(foo.prop); //123
	
	foo = {};
```
foo已经存储了一个地址，想让它指向令一个地址是不允许的

## 全局对象属性

在ES6之前，全局变量基本等于全局window的属性，在全局变量定义属性会自动添加到window对象上，这样会使开发人员无意中创建了全局变量（打错字）
为了改变这种情况，ES6新了定义方式let、const、class定义的变量都不会添加到全球对象上


