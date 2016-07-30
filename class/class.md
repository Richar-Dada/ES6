# Class
class浓浓的后端气息，从ES6开始，Class(类)这个概念被引入，以后定义构造函数或者说类，可以是class关键字。(类跟构造函数本质上是一样东西)
```javascript
	//定义类
	class Point{
		constructor(x,y){
			this.x = x;
			this.y = y;
		}
		
		//定义方法
		toString(){
			return '(' + this.x + ',' + this.y + ')';
		}
	}
```
在类里面，会有一个constructor方法，它是用来构造实例的，如果你不写，它也会默认给你添加一个。需要定义方法，直接toString就行，不用像ES5写在原型上。这种类的写法，完全就是构造函数的令一种写法，所以说它类和构造函数本质是一样的
```javascript
	class Point(){
		//...
	}
	
	typeof Point //'function'
	Point === Point.prototype.constructor // true
```

使用时也是用new命令，和构造函数的用法是一致的
```javascript
	class Bar{
		doStuff(){
			console.log('stuff');
		}
	}
	
	var b = nwe Bar();
	b.doStuff(); //stuff
```
构造函数prototype属性，在ES6的‘类’继续存在，在类中直接定义方法会自动添加到prototype上
```javascript
	class Point(){
		constructor(){
			//...
		}
		
		toString(){}
		toValue(){}
	}

	//等同于

	Point.prototype = {
		toString(){},
		toVaule(){}
	}
```
类还提高了一种在prototype上添加方法的API--Object.assign
```javascript
	class Point{
		constructor(){
			//...
		}
	}
	
	Object.assign(Point.prototype,{
		toString(){},
		toValue(){}
	})
	
```

## costructor方法
constructor是类的默认方法，通过new命令生成实例时(**类生成实例不用new会报错**)，自动调用。如果没有定义这个方法，一个空的constructor会默认添加

constructor返回一个实例对象，当然你可以改写成令一个对象，看下面代码
```javascript
	class Foo{
		constructor(){
			return Object.create(null);
			//返回一个新创建的空对象
		}
	}
	
	new Foo() instanceof Foo
	//false
```

## 类的实例对象
类生成实例必需使用new,这与ES5的最大区别之一
```javascript
	var point = Point(2,3); //报错
	var point = new Point(2,3); //正确
```
在类中，如果定义属性不用this，那么这些属性都会添加到原型上。
```javascript
	class Point{
		constructor(x,y){
			this.x = x;
			this.y = y;
		}

		toString(){
			return '(' + this.x + ',' + this.y + ')';
		}
	}

	var point = new Point(2,3);
	
	point.toString(); //(2,3)
	
	point.hasOwnProperty('x'); //true
	point.hasOwnProperty('y'); //true
	point.hasOwnProperty('toString') //false
	point.__proto__.hasOwnProperty('toString') //true
	
```

有一个需要注意的，类的所有实例共享一个原型
```javascript
	var p1 = new Point(2,3);
	var p2 = new Point(3,2);

	p1.__proto__.printName = function(){return 'Oopes'}

	p1.printName() //'Oops'
	p2.printName() //'Oops'
	
	var p3 = new Point(4,2);
	p3.printName() //'Oops'

```
p1在原型上添加了一个printName方法，当p3实例被创建后，它也可以使用printName方法，这足以证明它们是共享一个原型的。这样的一个特性，可以说是挺恐怖，建议不要通过这种方式在原型上添加方法

## class 不存在变量提升
Class不存在变量提升，这主要是照顾到了继承时的顺序
```javascript
	{
		let Foo = class{};
		class Bar extends Foo{
			//...
		}
	}
```
如果存在变量提升，Bar会提前声明，但let不会提前声明，此时找不到Foo就会报错

## 私有方法
私有方法在ES6中并没有很好的定义，通常在命名前加_(下划线)来表示私有方法
```javascript
	class Widget{
		//公有方法
		foo(baz){
			this._bar(baz);
		}
		
		//私有方法
		_baz(baz){
			return this.snaf = baz;
		}
	}
```

## class继承
Class是通过关键字extends实现继承的
```javascript
	class ColorPoint extends Point{
		constructor(x,y,color){
			super(x,y); //调用父类的construcotr(x,y)
			this.color = color;
		}
		
		toString(){
			return this.color + '' + super.toString(); 
			//super调用父类的toString方法
		}
	}
```
constructor的super在作用是新建父类的this对象，而且super必需先执行完再定义属性，否则会报错，继承中没有super也会报错。在类中还可以使用super.xx来调用父类的属性或方法

## 类的prototype属性和__proto__属性
类同时存在prototype和__proto__两个属性，它与函数的特性有点类型，本质上应该算是一个函数

```javascript
	class A{}	
	class B extends A{}
	
	B.__proto__ === A //true
	B.prototype.__proto__ === A.prototype //true
```
- __pro__:是一个实例，指向它的原型。B继承A，相当于是A的一个实例，所以第三行代码为true。
- 第四行代码也是可以这样理解

## super关键字
上面已经说过一下，它有两种用法：
1. 作为函数调用时，super代表父类的构造函数
2. 作为对象调用是，super代表父类，此时，super可以引用父类实例的属性和方法，包括静态方法

## 原生构造函数的继承
ES6之前，原生构造函数是不可以继承的。从ES6开始可以了，
```javascript
	class MyArray extends Array{
		constructor(...args){
			super(...args);
		}
	}

	var arr = new MyArray();
	arr[0] = 12;
	arr.length //1

	arr.length = 0;
	arr[0] //undefined
	
```
上面myArray类继承了Array构造函数，把数组的一些特性都继承过来了，如果自己加一些改造，这就可以创造更多的数据格式了。

## Class的静态方法
静态方法就是类的实例不可以继承的方法，但是可以通过【类名.方法】使用，而且可以被继承。这种形式也ES5时，构造函数作为对象添加方法的效果是一样的。
```javascript
	class Foo{
		static classMethod(){
			return 'hello';
		}
	}
	
	Foo.classMethod(); //hello

	var foo = new Foo();
	foo.classMethod();
	//TypeError:foo.classmethod is not a function
```

## Class静态属性
ES6规定,类的内部是没有静态属性的，如果想添加，只能通过这种方式
```javascript
	class Foo{}
	Foo.prop = 1;
	Foo.prop //1
```
这种写法还是ES5的写法，这明显是不靠谱的。在ES7中估计会有完善
