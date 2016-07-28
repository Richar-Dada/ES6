# Set

ES6提供的新的数据结构Set，**特点：它类似数组，但成员的值是唯一的**
```javascript
	var s = new Set();
	
	[2,3,5,4,5,2,2].map(x=>s.add(x));
	
	for(let i of s){
		console.log(i);
	}
	//2,3,5,4
```
Set也是它的构造函数，可以传入一个数组或者类数组
```javascript
	//例一
	var set = new Set([1,2,3,4,4]);
	[...set]
	//[1,2,3,4]

	//例二
	var items = new Set([1,2,3,4,5,5,5,5]);
	items.size //5
	
	//例三
	function divs(){
		return [...document.querySelectorall('div')]
	}
	var set = new Set(divs());
	set.size //56

	//类似于
	divs().forEach(div=>set.add(div))
	set.size //56
```
有一个使用技巧
```javascript
	[...new Set(array)] //可以快速去除重复成员
```

## Set实例的属性和方法
+ Set.prototype.constructor 构造函数，默认是Set构造函数
+ Set.prototype.size 每一个Set实例都拥有这个方法，返回实例成员总数
+ add 添加某个值
+ delete 删除某个值
+ has 返回一个布尔值，表示该值是否为Set成员
+ clear 清除所有成员

```javascript
	s.add(1).add(2).add(2)
	//2被添加两次，但只有一个值
	
	s.size //2

	s.has(1) //true
	s.has(2) //true
	s.has(3) //false

	s.delete(2);
	s.has(2) //false

```

下面是对象和Set写法的一些区别
```javascript
	//对象写法
	var properties = {
		'width' : 1,
		'height' : 1
	}

	if(properties[someName]){
		//do something
	}

	//Set写法
	var properties = new Set();

	properties.add('width');
	properties.add('height');

	if(properties.has(someName)){
		//do something
	}

```

Array.from可以把Set结构转为数组
```javascript
	var items = new Set([1,2,3,4,5]);
	var array = Array.from(items);

	//一个去重的办法
	function dedupe(array){
		return array.from(new Set(array));
	}
```

## Set遍历操作
+ keys() 返回键名的遍历器
+ values() 返回键值的遍历器
+ entries() 返回键值对的遍历器
+ forEach() 使用回调函数遍历每个成员

1. keys() values() entries() 方法返回遍历器对象，因为Set机构没有键名，或者说键名和键值一样，所以key和value的行为一致
```javascript
	let set = new Set(['red','green','blue']);

	for(let item of set.keys()){
		console.log(item);
	}
	//red
	//green
	//blue
	
	for(let item of set.values()){
		console.log(item);
	}
	//red
	//green
	//blue

	for(let item of set.entries()){
		console.log(item);
	}
	//['red','red']
	//['green','green']
	//['blue','blue']

```
2. forEach()
```javscript
	let set = new Set([1,2,3]);
	set.forEach((value,key)=>console.log(value*2))
	//2
	//4
	//6
```

# WeakSet
WeakSet结构与Set类似，也不是重复值的集合，它与Set有两个区别点
1. WeakSet的成员只能是对象，而不能是其他类型的值
2. WeakSet中的对象都是弱引用，即垃圾回收机制不考虑WeakSet对改对象的引用，也就是说，如果其他对象不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于WeakSet之中，这意味着，无法引用WeakSet成员，因此Weak是不可遍历的

```javascript
	var a = [[1,2],[3,4]];
	var ws = new WeakSet(a);
```
上面是a的成员成为WeakSet的成员，而不是a本身。很奇怪的一个特性，下面可以验证
```javascript
	var b = [3,4];
	var ws = new WeakSet(b);
	//Uncaught TypeError Invalid value used in weak set()
```
WeakSet只能添加对象，像b那样，成员不是对象，添加就会报错

# Map
Javascript对象本质上市键值对的集合，而且只能用字符串当作键，这会带来很大的限制。Map就是来打破这种束缚的，它的键不限于字符串，可以是各种类型
```javascript
	var m = new Map();
	var o = {p:'Hello World'}
	
	m.set(o,'content'); //o对象作为一个键
	m.get(o); //'content'

	m.has(o); //true
	m.delete(o); //true
	m.has(o); //false
```

Map构造函数可以接受一个数组作为参数，该数组的成员是一个个表示键值对的数组
```javascript
	var map = new Map([['name','张三'],['title','Author']]);
	
	map.size //2
	map.has('name'); //true
	map.get('name'); //'张三'
	map.has('title'); //true
	map.get('title'); //Author
```
上面这个怎么转换不好看懂，其实它是通过下面的算法实现的
```javascript
	var items = [
		['name','张三'],
		['title','Author']
	];
	
	var map = new Map();
	items.forEach(([key,value])=>map.set(key,value));
```
这样看起来就明白了，同一个键多次赋值，后面的值会被覆盖

Map的方法
1. size 返回Map结构的成员总数
2. set(key,value) 向Map添加新成员，返回整个Map结构
3. get(key) 读取key对应的键值，找不到key，就返回undefined
4. has(key) 返回一个布尔值，表示键是否在Map数据中
5. delete(key) 删除成功返回true，否则返回false
6. clear() 清除所有成员

Map的遍历方法
+ keys()
+ values()
+ entries()
+ forEach()
和Set的遍历方法是一样的，参考Set就可以

# WeakMap
它与WeakSet的特点有点像，WeakMap的键名只能是对象，也是弱引用，不计入垃圾回收机制。不明白这种机制有什么作用













