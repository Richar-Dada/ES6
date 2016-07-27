# Symbol
ES6引入的一种新的原始数据类型，表示独一无二的值，Javascript的第七种数据类型。

Symbol值通过Symbol函数生成，但不需要使用new关键字
```javascript
	let s = Symbol();
	
	typeof s //'symbol'
```
它还可以接受一个字符串作为参数，表示对Symbol实例的描述，主要是为了在显示台显示，或者转为字符串时比较容易区别，**并不会影响symbol的值**
```javascript
	//没有参数的情况
	var s1 = Symbol();
	var s2 = Symbol();

	s1 === s2 //false

	//有参数的情况
	var s1 = Symbol('foo');
	var s2 = Symbol('foo');
	
	s1 === s2 //false
```
Symbol不能和其他类型的值进行运算，但可以显示转为字符串，或者转为布尔型，但不能转为数值型
```javascript
	var sym = Symbol('My symbol');
	'your symbol is ' + sym //typeError:can't convert symbol to string
	`your symbol is ${sym}` //typeError:can't convert symbol to string

	String(sym); //'Symbol(My symbol)'
	sym.toString() // 'Symbol(My symbol)'

	var sym = Symbol();
	Boolean(sym); //true
	!sym //false
	
	if(sym){
		//...
	}

	Number(sym); //TypeError
	sym + 2 //TypeError
```

## 作为属性名的Symbol
感觉Symbol就是为了属性名而生的，因为它每个值都是不相等的，能够保证不会出现同名属性。下面是三种定义属性的方法
```javascript
	var mySymbol = Symbol();
	
	//第一种写法
	var a = {};
	a[mySymbol] = 'Hello!';

	//第二种写法
	var a = {
		[mySymbol] : 'Hello!'
	}

	//第三种写法
	var a = {}
	Object.defineProperty(a,mySymbol,{value:'Hello!'});

	a[mySymbol] //'Hello'

```

> 注意，Symbol值作为对象属性名时，不能用点运算符

```javascript
	var mySymbol = Symbol();
	var a = {};

	a.mySymbol = 'Hello!';
	a[mySymbol] // undefined
	a['mySymbol'] //'Hello'
```
上面的代码中，因为点运算符后面总是字符串，所以不会读取mySymbol作为标识名所指代的那个值，导致a的属性名实际上市一个字符串，而不是一个Symbol值

同理，在对象内部定义属性时，Symbol必须放在方括号中

## 属性名的遍历

Symbol作为属性名，它是一个不可枚举属性，一般的遍历方法无法获取，需要使用Object.getOwnPropertySymbols这个方法，它返回的是一个数组
```javascript
	var obj = {};
	var a = Symbol('a');
	var b = Symbol('b');

	obj[a] = 'Hello';
	obj[b] = 'World';

	var objectSymbols = Object.getOwnPropertySymbols(obj);
	
	objectSymbols //[Symbol(a),Symbol(b)]
```
另外一个API，Reflect.ownKeys方法可以返回所有类型的键名，包括常规键名和Symbol键名


## Symbol.for(),Symbol.keyFor()
Symbol的值是唯一的，当我们想重用一个Symbol值时就需要使用Symbol.for()。使用Symbol.for时，它会检查给定的key是否已经存在，如果不存在就新建一个，存在就直接返回相同的值

## Symbol

Symbol这值感觉还是比较奇怪，不知应用场景在哪


# 二进制数组
就是把数据以二进制的格式来传输，这样可以提高脚本的性能，它有三个对象组成：ArrayBuffer、TypedArray、DataView。

这些对象的操作和数组操作很类似，但是二进制数组不是真正的数组，主要用于大数据通信的API，
+ File API
+ XMLHttpRequest
+ Fetch API
+ Canvas
+ WebSockets
