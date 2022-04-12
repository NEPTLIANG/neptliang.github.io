---
layout:       post
title:        "B站ES6教程笔记"
subtitle:     "null"
date:         2022-02-28 20:45:43
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
	- FrontEnd
---


# `0x00` 前言

最近看了B站的ES6教程，整理一下其中一些示例及提到的容易出错的地方

由于之前看过部分阮一峰大佬的《ECMAScript 6 入门教程》，故本文不会是完整教程笔记

---


# `0x01` `let`

**`var`声明变量存在的问题**：`for`循环中索引变量i的作用域是**全局**，三次循环后i自增至3，这时事件回调再通过i访问元素会**索引越界**

```js
let items = document.getElementsByClassName('item');

for (var i=0; i<items.length; i++>) {
	items[i].onclick = function() {
		// Uncaught TypeError: Cannot read property 'style' of undefined
		items[i].style.background = 'pink';  	

		// 回调中可通过`this`访问，指向事件源
		// this.style.background = 'pink';  	
	}
}
```

解决：

1. 采用`let`
2. 回调中可通过`this`访问

---


# `0x02` 箭头函数

* **特点**

	1. `this`是**静态**的（指向无法改变），且**没有自己的`this`**，即`this`始终指向函数**声明时所在作用域**下的`this`的值

		```js
		function getName() {
			console.log(this.name);
		}

		let getName2 = () => {
			console.log(this.name);
		}

		window.name = '奸商黄';
		const hrx = {
			name: 'Jason Wong'
		}

		getName();  	//结果：“奸商黄”，普通函数this指向window
		getName2();  	//结果：“奸商黄”，该箭头函数声明时所在作用域的this在本例中指向window
		getName.call(hrx);  	//结果：“Jason Wong”，普通函数可用call、apply等方式改变this指向
		getName2.call(hrx);  	//结果：“奸商黄”，箭头函数的this指向无法改变
		```
		
	2. 不能作为构造函数来实例化对象

		```js
		let Person = (name, age) => {
			this.name = name;
			this.age = age;
		}

		let he = new Person('hrx', 30);  	//报错
		```

	3. 不能使用`arguments`变量

		```js
		let fn = () => {
			console.log(arguments);
		}

		fn(1, 2, 3);  	//报错
		```

* **应用场景**

	普通函数中`this`指向`window`，访问`this.style`报错

	```js
	let ad = document.getElementById('ad');

	ad.addEventListener('click', function() {
		setTimeout(function () {
			console.log(this);
			this.style.background = 'pink';  	//报错
		}, 2000);
	});
	```

	以前的解决办法：在回调外用变量存储`this`指向，通常起名 `_this` / `that` / `self`

	```js
	ad.addEventListener('click', function() {
		let _this = this;  	//保存this指向
		setTimeout(function () {
			// 访问保存下来的this
			_this.style.background = 'pink';  	
		}, 2000);
	});
	```

	有了箭头函数后：

	```js
	ad.addEventListener('click', function() {
		setTimeout(() => {  	// 箭头函数this指向函数声明时所在作用域下的this，在此例中即回调函数中this所指向的事件源
			this.style.background = 'pink';  	
		}, 2000);
	});
	```

	**不适用的地方**：箭头函数不适合用作**事件回调**函数，因事件回调的`this`应指向事件源
	
	```js
	ad.addEventListener('click', function() {  	//事件回调应用普通函数
		setTimeout(() => {
			this.style.background = 'pink';
		}, 2000);
	});
	```

	也**不适合**用作**对象的方法**，因对象中的`this`应指向对象本身

	```js
	{
		name: 'hrx',

		getName: () => {
			this.name;  	//往往会出错
		}
	}
	```
	
	**总结**：

	* 箭头函数**适合**与`this`**无关**的回调，如定时器、数组方法的参数回调
	* 箭头函数**不适合**与`this`**有关**的回调，如事件回调、对象的方法

---


# `0x03` 参数默认值

参数默认值一般位置要靠后

```js
// function add(a, b, c=10) {  	//盒里
function add(a, b=10, c) {  	//布盒里
	return a + b + c;
}

let result = add(1, 2);  	//这时候a为1、b为2，c还是undefined，返回NaN
```

---


# `0x04` rest参数

ES6引入rest参数，用于获取函数的实参，用来代替`arguments`

* ES5获取实参的方式：
	
	```js
	function fn() {
		console.log(arguments);  	//获取到的是一个可遍历的Arguments对象
	}

通过rest参数获取实参：

```js
function fn(...args) {
	console.log(args);  	//获取到的是数组
}
```

**优势**：获取到的是数组，可直接使用`filter`、`some`、`every`、`map`等数组API进行处理

**注意**：rest参数必须要放到形参列表**最后面**

```js
function fn(a, ...args, b) {  	//报错：Uncaught SyntaxError: Rest parameter must be last formal parameter
	console.log(args);
}
```

---


# `0x05` 拓展运算符

应用：

1. 数组合并

	```js
	const a = [1, 2];
	const b = [2, 3];
	const c = [...a, ...b];
	```

2. 数组克隆

	```js
	const d = [1, 2, 3];
	const e = [...d];
	```

3. 将伪数组转为数组

	```js
	const divs = document.querySelectorAll('div');
	const divArr = [...divs]
	```

---


# `0x06` Symbol

特点：

1. Symbol的值是唯一的，用来解决命名冲突的问题
2. Symbol值不能与其他数据进行运算
3. Symbol定义的对象属性**不能使用for...in循环遍历，但是可以使用`Reflect.ownKeys`来获取对象的所有键名**

创建Symbol的三种方式：

1. 直接调用函数

	```js
	let s = Symbol();
	```

2. 传入字符串参数，用于描述其作用，**相同参数创建出的值不同**

	```js
	let s2 = Symbol('sss');
	let s3 = Symbol('sss');
	console.log(s2 !== s3);  	//true
	```

3. `Symbol.for('描述字符串')`，**相同参数创建出的值相同**

	```js
	let s4 = Symbol.for('sss');
	let s5 = Symbol.for('sss');
	console.log(s4 === s5);  	//true
	```

**Symbol的应用**：

```js
// 假设是个内部结构错综复杂的对象
let game = { ...intricateInternalStructure };  	

let methods = {
	up: Symbol('up'),
	down: Symbol('down')
};

// 使用Symbol值避免对象属性命名冲突
game[methods.up] = () => console.log('上移');  	
game[methods.down] = () => console.log('下移');
```

**Symbol的内置属性**：

* `Symbol.hasInstance`：进行`instanceof`操作时自动触发的静态方法

	```js
	class Person {
		// 进行instanceof操作时会自动触发，可用于自行处理类型判断逻辑
		static [Symbol.hasInstance](param) {  	//被判断的对象被作为参数传进来
			console.log('param:', param);
			return false;  	//返回值作为instanceof的结果（转换成bool值）
		}
	}

	const o = {}
	// 进行instanceof操作自动触发上面的静态方法，上面静态方法的返回值作为instanceof的结果（转换成bool值）
	const result = o instanceof Person;  	
	console.log('result:', result);
	```

* `Symbol.isConcatSpreadable`：设置`concat`时不展开

	```js
	const arr1 = [1, 2, 3];
	const arr2 = [4, 5, 6];

	//设置concat时不展开
	arr2[Symbol.isConcatSpreadable] = false;  	
	const result = arr1.concat(arr2);  	// [ 1, 2, 3, [ 4, 5, 6 ] ]
	```

---


# `0x07` 迭代器

通过`Symbol.iterator`**方法**可访问可迭代对象中的迭代器：

```js
const arr = ['甲', '乙', '丙'];

// 通过Symbol.iterator方法获取可迭代对象中的迭代器
let iterator = arr[Symbol.iterator]();  	

// 通过迭代器的next方法遍历内容
console.log(iterator.next());  	//{ value: '甲', done: false }
console.log(iterator.next());  	//{ value: '乙', done: false }
console.log(iterator.next());  	//{ value: '丙', done: false }
console.log(iterator.next());  	//{ value: undefined, done: true }
console.log(iterator.next());  	//{ value: undefined, done: true }
```

**需要自定义遍历数据的时候，要想到迭代器**：

* 修改可迭代对象的迭代器

	```js
	const arr = ['甲', '乙', '丙'];

	// 通过迭代器自定义迭代过程
	arr[Symbol.iterator] = () => ({  	//返回一个迭代器对象
		next: () => ({ value: '丁', done: false })  	//迭代器对象的next方法返回遍历结果
	});

	console.log(iterator.next());  	// { value: '丁', done: false }
	console.log(iterator.next());  	// { value: '丁', done: false }
	console.log(iterator.next());  	// { value: '丁', done: false }
	console.log(iterator.next());  	// { value: '丁', done: false }
	console.log(iterator.next());  	// { value: '丁', done: false }

	// 会无限循环返回'丁'（会导致浏览器卡死）
	for (let ele of arr) { console.log(ele); }
	```

* 定义普通对象的迭代器

	```js
	const student = {
		name: 'hrx',
		course: [
			'Algorithm and Data Structor',
			'Principles of Computer Composition',
			'Operating System'
		],
		
		// 通过迭代器定义迭代过程
		[Symbol.iterator]() {
			let index = 0;
			return {
				next: () => ({
					value: this.course[index],
					done: index++ < this.course.length ?
						false : true
				})
			};
		}
	}

	for (let course of student) { console.log(course); }
	```

---


# `0x08` 生成器函数

多个异步操作对**时序性**有要求时，直接依次调用无法保证查询结果返回的顺序，故须通过Generator等方式保证其时序性，即所谓的“异步变同步”

示例：模拟获取**用户**数据 -> **用户**的**订单**数据 -> **订单**中的**商品**数据

```js
// 模拟异步获取用户数据
let getUsers = () => setTimeout(() => {  	//setTimeout模拟异步操作
	let users = '用户数据';  	//模拟获取用户数据过程
	// 3. 通过下方的Iterator的next方法触发下方的Genterator函数的第二段（第一个yield语句到第二个yield语句）代码执行，并把用户数据通过next方法的参数传出
	iterator.next(users);
}, 1000);

// 模拟异步获取订单数据
let getOrders = () => setTimeout(() => {
	let orders = '订单数据';
	// 5. 把订单数据通过next方法的参数传出，并触发下方的Genterator函数的下一段代码执行
	iterator.next(orders);
}, 1000);

// 模拟异步获取商品数据
let getGoods = () => setTimeout(() => {
	let goods = '商品数据';
	// 7. 把商品数据通过next方法的参数传出，并触发下方的Genterator函数的下一段代码执行
	iterator.next(goods);
}, 1000);

function * gen() {
	// 4. 获取通过next方法参数传出的用户数据
	let users = yield getUsers();  	
	// 6. 获取通过next方法参数传出的订单数据
	let orders = yield getOrders();
	// 8. 获取通过next方法参数传出的商品数据
	let goods = yield getGoods();
	console.log([ users, orders, goods ]);  	// [ '用户数据', '订单数据', '商品数据' ]
}

// 1. 调用Generator函数获取Iterator
let iterator = gen();
// 2. 调用一次Iterator的next方法，触发Generator函数的首段（从函数开头到第一个yield语句）代码执行
iterator.next();
```

---


# `0x09` Promise

回调方式异步操作存在的问题：

```js
const fs = require('fs');

// 1. 回调地狱
fs.readFile('./file1.txt', (err, buffer) => {
	fs.readFile('./file2.txt', (err, buffer2) => {
		fs.readFile('./file3.txt', (err, buffer3) => {  	// 2. 变量名容易冲突，出错了也不好排查
			console.log(`
				${buffer.toString()}
				${buffer2.toString()}
				${buffer3.toString()}
			`);
		});
	});
});
```

promise实现依次读取多个文件示例：

```js
const fs = require('fs');

// 封装Promise，读取文件1
const readFile = new Promise((resolve, reject) => {
	fs.readFile('./test-file/file1.txt', (err, buffer) => {
		if (err) { 
			reject({ location: 1, err }); 
		}
		resolve([ buffer.toString() ]);
	});
})

// 调用
readFile.then(data => {
	console.log('readedFile1: ', data);
	// 读取文件2 	
	return new Promise((resolve, reject) => {
		fs.readFile('./test-file/file2.txt', (err, buffer) => {
			if (err) { reject({ location: 2, err}); }
			resolve([
				...data,
				buffer.toString()
			]);
		});
	})
}).then(data => {
	console.log('readedFile2:', data);
	// 读取文件3
	return new Promise((resolve, reject) => {
		fs.readFile('./test-file/file3.txt', (err, buffer) => {
			if (err) { reject({ location: 3, err}); }
			resolve([ ...data, buffer.toString() ]);
		});
	})
}).then(results => {
	console.log('results:', results.join(''));
	return results;
}).catch(err => {
	throw err;
});
```

发送请求示例：

```js
const getJoke = () => new Promise((resolve, reject) => {
	const xhr = new XMLHttpRequest();
	xhr.open('GET', 'https://api.apiopen.top/getJoke');
	xhr.onreadystatechange = () => {
		if (xhr.readyState === 4) {
			const { status = 400 } = xhr;
			if ((status >= 200 && status < 300) || status === 302) {
				resolve(xhr.response);
			} else {
				reject(xhr);
			}
		}
	}
	xhr.send();
});

// then、catch方法的返回值是Promise对象，
// 对象状态由回调函数的执行结果决定 
const result = getJoke().then(response => {
	console.log('thenMethodParameter:', response);
	switch (Math.floor(Math.random() * 1000) % 3) {
		case 0:
			// 1. 如果回调函数返回值为非promise类型的属性，
			// 	则状态为成功，返回值为对象成功的值
			return response;
		case 1:
			// 2. 若返回promise对象，则状态取决于返回的promise
			// 	对象的状态，[[PromiseValue]]为 
			// 	resolve / reject 函数的参数
			return new Promise((resolve, reject) => {
				// resolve('ok');
				rejcet('error');
			});
		case 2: 
			// 3. 抛出错误，则返回的
			// 	Promise对象[[PromiseStatus]]为rejected，
			// 	[[PromiseValue]]为抛出的Error
			throw new Error('出错了');
	}
}).catch(xhr => console.error(xhr.status, xhr.reason));

console.log('promiseReturnValue:', result);
```

---


# `0x0A` Set

示例：

```js
// 创建
let s0 = new Set();
let s = new Set([1, 2, 3]);

// 1. size: 元素个数
console.log(s.size);  	//3

// 2. add: 添加元素
s.add(4);

// 3. delete: 删除元素
s.delete(2);

// 4. has: 检测元素
let has4 = s.has(4);
let has2 = s.has(2);

console.log({ has4, has2 });

// 5. for...of: 遍历元素
for (let ele of s) { console.log('tarversing:', ele); }

// 6. clear: 清空元素
s.clear();

console.log('cleared:', s);
```

应用：

```js
let arr1 = [1, 2, 3, 2, 1];
let arr2 = [2, 3, 4, 3, 2];

// 1. 数组去重
let duplicationEliminatingResult = [ ...new Set(arr1) ];

// 2. 取并集
let union = [ ...new Set([...arr1, ...arr2])];

console.log({ 
	duplicationEliminatingResult,
	union
});  	// { "duplicationEliminatingResult": [ 1, 2, 3 ], "union": [ 1, 2, 3, 4 ] }
```

---


# `0X0B` Map

示例：

```js
// 声明
let m = new Map();

let key2 = { a: 1 };
// 1. set: 添加属性
m.set('key', 'val');
m.set(key2, () => console.log('called'));

// 2. delete: 删除属性
m.delete('key');

// 3. get: 读取属性
let val2 = m.get(key2);
let valOfNonexistentKey = m.get('key3');

console.log({ val2, valOfNonexistentKey });  	// { valOfNonexistentKey: undefined, val2: () => console.log('called') }

// 4. size: 属性数量
console.log(m.size);  	//1

// 5. for...of: 遍历属性
for (let keyValues of m) { console.log('tarversing:', keyValues); }

// 6. clear: 清空属性
m.clear();

console.log('cleared:', m);
```

---


# `0x0C` 类

类的**ES5**实现：

```js
// 父类声明与属性、方法配置
function Human(height, weight) {
	this.height = height;
	this.weight = weight;
}
// 静态属性
Human.number = '1.4 billion';
// 静态方法
Human.unite = () => console.log('Proletarians of the world, unite!');
// 实例属性
Human.prototype.hometown = 'earth';
// 实例方法
Human.prototype.eat = () => console.log('A man is eating...');

// 子类声明
function Student(height, weight, school, classroom) {
	// 调用父类的构造方法，通过call方法传递this
	Human.call(this, height, weight);
	this.school = school;
	this.classroom = classroom;
}
// 设置原型
Student.prototype = new Human;

Student.number = '260 million';
Student.increase = () => console.log('Schools are expanding and the number of students is increasing...');
Student.prototype.task = 'Study';
Student.prototype.study = () => console.log('A student is studying...');

// 实例化
const teacherWong = new Human(180, 65);  
teacherWong.workNumber = '39473';
teacherWong.afterWork = () => console.log('Miss Wong has gone off work');
const xiaoMing = new Student(170, 60, 'LNU', 'CS1');
xiaoMing.studentId = '2017654321';
xiaoMing.classIsOver = () => console.log("Xiaoming's class is over");

// 父类测试
console.log({ Human });
console.log({
	'Human.number': Human.number,
	'Human.hometown': Human.hometown
});  	// { Human.number: '1.4 billion', Human.hometown: undefined }
Human.unite();  	//Proletarians of the world, unite!
Human.eat?.();
console.log({ teacherWong });  	// { teacherWong: Human { height: 180, weight: 65, workNumber: '39473', afterWork: ƒ } }
console.log({
	'teacherWong.number': teacherWong.number,
	'teacherWong.hometown': teacherWong.hometown
});  	// { teacherWong.number: undefined, teacherWong.hometown: 'earth' }
teacherWong.unite?.();
teacherWong.eat();  	//A man is eating...
teacherWong.afterWork();  	//Miss Wong has gone off work

// 子类测试
console.log({ Student });
console.log({
	'Student.number': Student.number,
	'Student.task': Student.task
});  	// { Student.number: '260 million', Student.task: undefined }
Student.increase();  	//Schools are expanding and the number of students is increasing...
Student.study?.();
console.log({ xiaoMing });  	// { xiaoMing: Student {height: 170, weight: 60, school: 'LNU', classroom: 'CS1', studentId: '2017654321', classIsOver: ƒ } }	
console.log({
	'xiaoMing.number': xiaoMing.number,
	'xiaoMing.task': xiaoMing.task
})  	// { xiaoMing.number: undefined, xiaoMing.task: 'Study' }
xiaoMing.increase?.();
xiaoMing.study();  	//A student is studying...
xiaoMing.classIsOver();  	//Xiaoming's class is over
```

类的ES6实现：
```js
// 父类
class Human {
	// 静态属性
	static number = '1.4 billion';
	// 实例属性
	hometown = 'earth';

	constructor(height, weight) {
		this.height = height;
		this.weight = weight;
	}

	// 静态方法
	static unite() {
		console.log('Proletarians of the world, unite!');
	}

	// 实例方法
	eat() {
		console.log('A man is eating...');
	}
}

// 子类
class Student extends Human {
	static number = '260 million';
	task = 'Study';

	constructor(height, weight, school, classroom) {
		// 调用父类的构造方法
		super(height, weight);
		this.school = school;
		this.classroom = classroom;
	}

	static increase() {
		console.log('Schools are expanding and the number of students is increasing...');
	}

	// 可重写父类同名方法
	eat() {
		console.log('A student is eating...');
	}

	study() {
		// super();  	//Uncaught SyntaxError, 构造函数需调用父类的构造方法，但是成员方法不允许调用
		console.log('A student is studying...');
	}
}

// 实例化
const teacherWong = new Human(180, 65);  
teacherWong.workNumber = '39473';
teacherWong.afterWork = () => console.log('Miss Wong has gone off work');
const xiaoMing = new Student(170, 60, 'LNU', 'CS1');
xiaoMing.studentId = '2017654321';
xiaoMing.classIsOver = () => console.log("Xiaoming's class is over");

// 父类测试
console.log({ Human })
console.log({
	'Human.number': Human.number,
	'Human.hometown': Human.hometown
});  	// { Human.number: '1.4 billion', Human.hometown: undefined }
Human.unite();  	//Proletarians of the world, unite!
Human.eat?.();
console.log({ teacherWong });  	// { teacherWong: Human { hometown: 'earth', height: 180, weight: 65, workNumber: '39473', afterWork: ƒ } }
console.log({
	'teacherWong.number': teacherWong.number,
	'teacherWong.hometown': teacherWong.hometown
});  	// { teacherWong.number: undefined, teacherWong.hometown: 'earth' }
teacherWong.unite?.();
teacherWong.eat();  	//A man is eating...
teacherWong.afterWork();  	//Miss Wong has gone off work

// 子类测试
console.log({ Student })
console.log({
	'Student.number': Student.number,
	'Student.task': Student.task
});  	// { Student.number: '260 million', Student.task: undefined }
Student.increase();  	//Schools are expanding and the number of students is increasing...
Student.study?.();
console.log({ xiaoMing });  	// { xiaoMing: Student { hometown: 'earth', height: 170, weight: 60, task: 'Study', school: 'LNU', classroom: 'CS1', studentId: '2017654321', classIsOver: ƒ } }	
console.log({
	'xiaoMing.number': xiaoMing.number,
	'xiaoMing.task': xiaoMing.task
})  	// { xiaoMing.number: undefined, xiaoMing.task: 'Study' }
xiaoMing.increase?.();
xiaoMing.study();  	//A student is studying...
xiaoMing.eat();  	//A student is eating...
xiaoMing.classIsOver();  	//Xiaoming's class is over
```

`get`与`set`：

```js
class Student {
	t = 'Study'

	// get
	get task() {
		console.log('task属性被读取');
		return this.t;
	}

	// set
	set task(task) {  	//必须有形参否则报错
		this.t = task;
		console.log('task属性被修改');
	}
}
const xiaoMing = new Student();

// 获取getter值
console.log('gotten:', xiaoMing.task);  	//gotten: Study

console.log('Before the set value:', xiaoMing.t);  	//Before the set value: Study
// 给setter赋值
xiaoMing.task = 'Sleep';
console.log('After the value is set:', xiaoMing.t);  	//After the value is set: Sleep
```

---


# 0x0D 数值的扩展

1. `Number.EPSILON`：JavaScript表示的最小精度

	```js
	const floatEqual = (a, b) => Math.abs(a - b) < Number.EPSILON;

	console.log(0.1 + 0.2 === 0.3);  	//false
	console.log(floatEqual(0.1 + 0.2, 0.3));  	//true
	```

2. 二进制、八进制、十六进制

	```js
	const bin = 0b1010;
	const oct = 0o77;
	const dec = 99;
	const hex = 0xff;

	console.log({ bin, oct, dec, hex });  	// { bin: 10, oct: 63, dec: 99, hex: 255 }
	```

3. `Number.isFinite`: 判断数值是否为有限数

	```js
	console.log({
		100: Number.isFinite(100),
		'100/0': Number.isFinite(100 / 0),
		'Infinity': Number.isFinite(Infinity)
	});  	// { 100: true, 100/0: false, Infinity: false }
	```

4. `Number.isNaN`（判断是否为NaN）：原为全局函数，现添加为`Number`的方法

	```js
	console.log({
		'undefined+1': Number.isNaN(undefined + 1),
		'123+1': Number.isNaN(123 + 1)
	});  	// { undefined+1: true, 123+1: false }
	```

5. `Number.parseInt`、`Number.parseFloat`：原为全局函数，现添加为`Number`的方法

	```js
	console.log([
		Number.parseInt('111aaa'),
		Number.parseFloat('111.111aaa')
	]);  	// [ 111, 111.111 ]
	```

6. `Number.isInteger`: 判断是否为整数

	```js
	console.log({
		2: 		Number.isInteger(2),
		'2.0': 	Number.isInteger(2.0),
		2.5: 	Number.isInteger(2.5)
	});  	// { 2: true, 2.0: true, 2.5: false }
	```

7. `Math.trunc`: 截掉数值的小数部分

	```js
	console.log({
		3.1: Math.trunc(3.1),
		3.9: Math.trunc(3.9),
		'-3.1': Math.trunc(-3.1),
		//Math.floor是向负无穷方向取整，trunc是直接抹掉小数部分
		'-3.9': Math.trunc(-3.9),
		'Math.floor(-3.9)': Math.floor(-3.9),  	
		'-0.9': Math.trunc(-0.9),
		Infinity: Math.trunc(Infinity),
		//对于非数值，Math.trunc内部先使用Number方法将其转为数值
		"'3.9'": Math.trunc('3.9'),  	
		'3.9aaa': Math.trunc('3.9aaa'),
		'true': Math.trunc(true),
		'false': Math.trunc(false),
		'': Math.trunc(''),
		'null': Math.trunc(null),
		//对于空值和无法截取整数的值，返回NaN
		'NaN': Math.trunc(NaN),
		'undefined': Math.trunc(undefined),
		'()': Math.trunc(),
		'aaa': Math.trunc('aaa')
	});  	// {3.1: 3, 3.9: 3, -3.1: -3, -3.9: -3, Math.floor(-3.9): -4, -0.9: -0, Infinity: Infinity, '3.9': 3, 3.9aaa: NaN, true: 1, false: 0, "": 0, null: 0, NaN: NaN, undefined: NaN, (): NaN, aaa: NaN }
	```

8. `Math.sign`: 判断一个数是正数、负数还是零

	```js
	console.log({
		100: Math.sign(100),
		0: Math.sign(0),
		'-20000': Math.sign(-20000)
	});  	// { 0: 0, 100: 1, -20000: -1 }
	```

---


# `0x0E` 对象方法扩展

1. `Object.is`: 判断两个值是否完全相等

	```js
	console.log([
		Object.is(NaN, NaN),
		NaN === NaN
	]);  	// [ true, false ]
	```

2. `Object.assign`: 对象的合并，将源对象的所有可枚举属性，复制到目标对象

	```js
	const target = {
		host: 'localhost',
		name: 'root'
	};

	const source1 = {
		name: 'user',
		pwd: 'password'
	};

	const source2 = {
		pwd: 'password2',
		port: 3306
	}

	console.log(
		Object.assign(target, source1, source2)
	);  	// { host: 'localhost', name: 'user', pwd: 'password2', port: 3306 }
	console.log(target);  	// { host: 'localhost', name: 'user', pwd: 'password2', port: 3306 }
	```

3. `Object.setPrototypeOf`: 设置原型对象

	`Object.getPrototypeOf`: 读取原型对象
	
	```js
	const a = { b: 'c' };
	const d = { e: 'f' };

	console.log(Object.getPrototypeOf(a));  	// { constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, … }
	Object.setPrototypeOf(a, d);
	console.log(Object.getPrototypeOf(a));  	// { e: 'f' }
	console.log(a);  	// { b: 'c' }
	```

---


# `0x0F` ES6 module

默认暴露：

* export.js:

	```js
	export default {  	//默认暴露
		school: 'LNU',
		teach: () => console.log('Teaching activities are in progress')
	}
	```

* import.js: 

	```js
	import * as m1 from './export.js'

	console.log(m1);  	// { default: { school: "LNU", teach: () => console.log('Teaching activities are in progress') } }
	//默认暴露出的内容挂在default属性下
	m1.default.teach();  	//Teaching activities are in progress
	```

**注意**：不能直接`import { default }`，但是起个别名之后可以

* import2.js:

	```js
	// import { default } from './export.js'      //SyntaxError: Unexpected reserved word，不能直接解构取default
	import { default as m1 } from './export.js'    //给default起个别名后可以import

	console.log(m1);    // { school: 'LNU', teach: [Function: teach] }
	```

打包：

```sh
# 安装工具
npm i babel-cli babel-preset-env browserify
npx babel src/js -d dist/js --presets-babel-preset-env  	# --presets-babel-preset-env一般在babelrc里配置
# 打包
npx browserify dist/js/demo.js -o dist/bundle.js
```

---


# `0x10` ES7特性

## 1. `Array.prototype.includes`

## 2. 指数操作符

ES7中引入指数运算符（`**`），用来实现幂运算，功能与`Math.pow`相同

---


# `0x11` ES8特性

## 1. `async`和`await`

注意：

1. `async`函数的**返回值为`promise`对象**
2. `promise`对象的结果由**`async`函数执行的返回值**决定
3. `await`右侧的表达式**一般返回`promise`对象**
4. `await`表达式返回的是**`promise`成功的值**
5. `await`的`promise`失败了，就会**抛出异常**，需要通过try...catch捕获处理

不同情况下的`async`函数返回值：

```js
// 返回字符串
const string = async () => 'test';
// 抛出错误，则返回的是失败的Promise
const fnThrowedErr = async () => {
	throw new Error('出错了');
	return '没出错';
}
// return的结果不是Promise类型的对象，则接收到的返回结果是成功的Promise对象
const nonPromise = async () => {};
// 返回的结果是Promise对象，则其状态取决于返回的Promise对象的状态
const resolvedPromise = async () => new Promise((resolve, reject) => resolve('resolved'));
const rejectedPromise = async () => new Promise((resolve, reject) => reject('rejected'));

// Promise.allSettled([
// 	string(), 
// 	fnThrowedErr(), 
// 	nonPromise(), 
// 	resolvedPromise(), 
// 	rejectedPromise()
// ])
// 	.then(result => console.log(result))  	// [{ status: 'fulfilled', value: 'test' }, { status: 'rejected', reason: Error: 出错了 at fnThrowedErr (<anonymous>:5:8) at <anonymous>:18:2 }, { status: 'fulfilled', value: undefined }, { status: 'fulfilled', value: 'resolved' }, { status: 'rejected', reason: 'rejected' }]
// 	.catch(e => console.warn(e));
string()
	.then(result => console.log(result))  	//test
	.catch(e => console.warn(e));
fnThrowedErr()
	.then(result => console.log(result))
	.catch(e => console.warn(e));  	//Error: 出错了
nonPromise()
	.then(result => console.log(result))
	.catch(e => console.warn(e));
resolvedPromise()
	.then(result => console.log(result))  	//resolved
	.catch(e => console.warn(e));
rejectedPromise()
	.then(result => console.log(result))
	.catch(e => console.warn(e));  	//rejected
```


---

***`//未完待xu`***

---


# 参考文献

* [B站《尚硅谷Web前端ES6教程，涵盖ES6-ES11》](https://www.bilibili.com/video/BV1uK411H7on)

![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)