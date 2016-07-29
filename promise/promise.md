# Promise 
Promise简单说就是一个容器，里面保存着耨个未来才会结束的事件的结果。从语法上说，Promise是一个对象，从它可以获取异步操作的信息。Promise提供统一的API，各种异步操作都可以用同样的方法进行处理。
Promise有两个特点：
1. 对象的状态不受外界影响。它有三种状态Pending(进行中)、Resolved(已完成)、Rejected(已失败)。
2. 一旦状态改变，就不会再变。状态只能从Pending到Resolve或者Pending到Rejected两种情况

## 基本用法
```javascript
	var promise = new Promise(function(resolve,reject){
		//some code
		
		if(/*异步操作成功*/){
			resole(value);
		}else{
			reject(error);
		}
	})
```
Promise传入两个函数,如果异步操作成功，执行resolve函数，value是异步操作返回的数据。如果失败，则执行reject函数

## then方法
Promise实例生成后，可以用then方法指定Resolved状态和reject状态的回调函数
```javascript
	Promise.then(function(){
		//success
	},function(error){
		//failure
	})
```
then方法中的两个函数，第一个回调函数是Promise对象状态为Resolved时调用，第二个函数是Reject时调用。

下面来了解一下它们的执行顺序
```javascript
	let promise = new Promise(function(resolve,reject){
		console.log('Promise');
		resole();
	});

	promise.then(function(){
		console.log('Resolved');
	});

	console.log('Hi');

	//Promise
	//Hi
	//Resolved
```
最后打印出Resolved，证明then方法是最后执行的。Promise新建后马上执行，但then的回调函数会在同步任务执行完之后执行，这样JS的事件队列规则一致。

下面是一个ajax的例子
```javascript
	var getJSON = function(url){
		var promise = new Promise(function(resolve,reject){
			var client = new XMLHttpRequest();
			client.open('GET',url);
			client.onreadstatechange = hander;
			client.responseType = 'json';
			client.setRequestHeader('Accept','application/json');
			client.send();

			function hander(){
				if(this.readState !== 4){
					return;
				}
				if(this.status === 200){
					resolve(this.response);
				}else{
					reject(new Error(this.statusText));
				}
			}
		});
		return promise;
	}
	
	getJSON('/post.json').then(function(json){
		console.log('contents:'+json);
	},function(error){
		console.log('出错了',error);
	})

```
如果成功请求就执行resolve，否则执行reject

接着再看一种特殊情况，resolve或reject的参数是一个Promise实例
```javascript
	var p1 = new Promise(function(resolve,reject){
		setTimeout(()=>{reject(new Error('fail'))},3000);
	})

	var p2 = new Promise(function(resolve,reject){
		setTimeout(()=>{resolve(p1)},1000);
	})
	
	p2
	  .then(result => console.log(result))
	  .catch(error => console.log(error))

	//Error:fail
```
上面代码执行顺序是这样的：代码加载完毕，一秒之后，p2的resolve触发，但是参数是p1，因此p2的状态根据p1来判断。两秒之后，p1执行reject函数，p2的状态确认为reject，所以最后执行catch语句，抛出fail
> 关键点是，promise的resolve和reject的参数是Promise实例，最终的状态根据参数实例的状态来判断。

## then补充
then方法返回一个新的Promise实例，这个实例不是原来的实例，所以它可以用链式的写法
```javascript
	getJSON('/posts.json').then(function(json){
		return json.post;
	}).then(function(post){
		//...
	})
```
两个then方法会依次执行，第一个回调函数的返回值会传到第二个函数的参数中，即json.post == post

还有一个需要注意，如果then返回的是一个有异步操作的Promise实例，那么后面的then会等待它执行完才会执行
```javascript
	getJSON('/post/1.json').then(function(post){
		return getJSON(post.commentURL);
	}).then(function funcA(comments){
		console.log('Resolved:',comments);
	},function funcB(err){
		console.log('Rejected:',err);
	})
```
这个then方法会等待getJSON(post.commentURL)响应完才会执行

## 异常捕获--catch
catch是then方法reject状态下的令一种形态，简单来说，如果reject状态的then，可以用catch来代替
```javascript
	getJSON('/posts.json').then(function(posts){
		//...
	}).catch(function(error){
		//处理getJSON和前一个回调函数运行时发生的错误
		console.log('发生错误！',error);
	})

```
catch还有一个特点，当promise状态已经是resolved时，执行then方法发生错误，它也能成功捕获。因此，Promise后面比较正规的写法是
```javascript
	promise
		.then(function(data){
			//success
		},function(err){
			//error
		})
```
但有一点需要注意

```javascript
	var promise =  new Promise(function(resolve,reject){
		resolve('ok');
		throw new Error('test');
	})

	promise
		.then(function(value){console.log(value)})
		.catch(function(error){console.log(error)});
	//ok
```
上面代码输出ok，因为一旦promise转为resolved状态，后面的错误就不会捕获。之后的then方法里面抛出错误就可以捕获。

## Promise.all
将多个promise实例包装成一个p，它的状态由参数的promise实例决定，有两种情况
1. 只有所有参数promise实例的状态都未fulfilled，p才会变成fulfilled
2. 只要所有参数promise实例中有一个被rejected，p的状态就变成rejected
```javascript
	var promise = [2,3,5,7,11,13].map(function(id){
		return getJSON('/post/'+id+'.json');
	})

	Promise.all(promises).then(function(posts){
		//...
	},function(reson){
		//..
	})
```
上面代码，创建了6个promise实例，只有6个实例变成fulfilled才会执行第一个函数，或者有一个变为rejected，执行第二个函数

## Promise.race
这个方法与all反过来，只要有一个状态改变，就可以执行then的方法


# Promise.resolved()
把一个对象转换为Promise对象，Promise.resolve等价于下面的写法
```javscript
	Promise.resolve('foo');
	//等价于
	new Promise(resolve => resolve('foo'))
```

# Promise.reject()
这个方法和Promise.resolve的用法一样的




