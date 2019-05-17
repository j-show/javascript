# jShow JavaScript 代码规范

*这是一个javascript代码规范，参照了[Airbnb](https://github.com/airbnb/javascript)*

> **注意**: 本指南假设您正在使用[jShow](https://github.com/j-show/jShow)，以及相关的插件。

其他语言规范

- [CSS & Sass](https://github.com/j-show/css)

## 内容列表

1. [类型](#类型)
2. [定义](#定义)
3. [对象](#对象)
4. [数组](#数组)
5. [解构](#解构)
6. [字符串](#字符串)
7. [函数](#函数)
8. [箭头函数](#箭头函数)
9. [类](#类)
10. [模块](#模块)
11. [迭代器](#迭代器)
12. [属性](#属性)
13. [变量](#变量)
14. [运算](#运算)
15. [块](#块)
16. [注释](#注释)
17. [异步](#异步)

## 类型

- 1.1 基础类型：当处理简单类型时，可以直接处理值

	- `string`
	- `number`
	- `boolean`
	- `null`
	- `undefined`
	- `symbol`
	
	```javascript
	const a = 1;
	let b = a;
	
	b = 9;
	
	console.log(a, b); // => 1, 9;
	``` 
	
	- `symbol`类型支持范围有限，所以在所有场景中因尽量避免使用。

- 1.2 复杂类型：当处理复杂类型时，所有赋值只是对原始对象的引用，直接操作子内容会改变所有引用对象。
	
	- `object`
	- `array`
	- `function`
	
	```javasript
	let a = [1,2];
	let b = a;
	
	a = [3, 4];
	b[0] = 9;
	
	console.log(a); // => [3, 4]
	console.log(b); // => [9, 2]
	```

**[⬆ back to top](#内容列表)**

## 定义

- 2.1 使用`const`适合进行常量或功能模块定义，任何情况避免使用`var`
	
	> 这确保您不能重新分配引用，这可能会导致错误和难以理解的代码。
	
	```javascript
	// bad
	var a = 1;
	var b = require("test");
	var c = jShow.Deferred(true);
	
	// good
	const a = 1;
	const b = require("test");
	const c = jShow.Deferred(true);
	```
	
- 2.2 如果你需要使用临时变量定义，应该使用`let`

	> `let`存在作用域区分，这会降低内存消耗以及优化代码
	
	```javascript
	// bad
	var count = 1;
	if (true) {
		count += 1;
	}
	
	// good
	if (true) {
		let count = 1;
		count += 1;
	}
	```
	
- 2.3 `let`、`const`都存在作用域问题，在使用时因严格遵循先定义后使用原则，并区分作用域范围

	```javascript
	let a = 1;
	{
		let a = 2;
		let b = 10;
		console.log(a); // => 2
	}
	console.log(a); // => 1;
	console.log(b); // => 未定义，抛出异常
	```
	
- 2.4 每个变量或赋值，使用一个`const`、`let`声明，不要连写;

	> 会造成多余代码，但这样更便于检查与阅读
	> 如果变量只声明不赋值，允许连写

	```javascript
	// bad
	const a = 1,
	      b = 2;
	      
	// good
	const a = 1;
	const b = 2;
	```
	
- 2.5 将`const`、`let`分组

	```javascript
	// bad
	const a = 1;
	let b = 123;
	const c = 2;
	
	// good
	const a = 1;
	const c = 2;
	let b = 123;
	```
	
**[⬆ back to top](#内容列表)**
	
## 对象

- 3.1 对于非类化模块，使用文字语法创建对象

	```javascript
	// bad
	const obj = new Object();
	
	//good
	const obj = {};
	```
	
- 3.2 当需要使用动态属性名时，因计算属性名

	> 这样允许你在定义时完整创建所有出行内容

	```javascript
	function getKey(k){
		return `a key name ${k}`;
	}
	
	// bad
	const obj = {
		a: 1,
		b: "string"
	};
	obj[getKey(`c`)] = true;
	
	// good
	const obj = {
		a: 1,
		b: "string",
		[getKey("c")]: true
	};
	```
	
- 3.3 使用对象方法简写

	```javascript
	// bad
	const obj = {
		a: 1,
		b: function(value) {
			return `${this.a}_${value}`;
		}
	};
	
	// good
	const obj = {
		a: 1,
		b(value) {
			return `${this.a}_${value}`;
		}
	};
	```
	
- 3.4 使用属性值简写

	```javascript
	// const a = "this is value";
	
	// bad
	const obj = {
		a: a
	};
	
	// good
	const obj = {
		a
	};
	```
	
- 3.5 在声明时对简写属性进行分组

	```javascript
	const a = "123",
	      c = "345";
	      
	// bad
	const obj = {
		a,
		b: 1,
		c,
		d: 2
	};
	
	// good
	const obj = {
		a, c,
		b: 1,
		d: 2
	};
	```
	
- 3.6 属性引号只在属性标识无效时使用

	```javascript
	// bad
	const obj = {
		"a": 1,
		"b-c": 2
	};
	
	// good
	const obj = {
		a: 1,
		"b-c": 2
	};
	```
	
- 3.7 不要直接调用`Object.prototype`下的函数，它可能会被覆盖

	```javascript
	// bad
	console.log(object.hasOwnProperty("method"));
	
	// good
	console.log(Object.prototype.hasOwnProperty.call(object, "method"));
	
	// best
	console.log(jShow.hasOwnProperty(object, "method"));
	```
	
- 3.8 不要使用`Object.assign`，而使用浅复制声明方式

	```javascript
	const a = {a:1, b:2};
	
	// bad
	const b = Object.assign(a, {c: 3});
	
	// good
	const b = {...a, c: 3};
	```

**[⬆ back to top](#内容列表)**

## 数组

- 4.1 使用文字语法创建对象

	```javascript
	// bad
	const arr = new Array();
	
	// good
	const arr = [];
	```
	
- 4.2 使用`Array`对象函数，改变数组内容

	```javascript
	const arr = [];
	
	// bad
	arr[arr.length] = "new Item";
	
	// good
	arr.push("new Item");
	```

- 4.3 使用操作符`...`复制数组

	```javascript
	const arr = [1, 2, 3];
	// bad
	const copy = [];
	      
	for (i=0; i<arr.length; i++) copy.push(arr[i]);
	
	// good
	const copy = [...arr];
	```
	
- 4.4 类数组对象转换成数组时，不要使用`Array.from`函数

	```javascript
	const obj = document.querySelectorAll(".all");
	
	// bad
	const node = obj.from(obj);
	
	// good
	const node = [...obj];
	```
	
- 4.5 使用`Array.from`函数转换对象

	```javascript
	const obj = {0: "a1", 1: "a2"};
	
	// bad
	const arr = Array.prototye.slice.call(obj);
	
	// good
	const arr = Array.from(obj);
	```

- 4.6 类数组对象转成数组且需要进行map函数处理时，使用`Array.from`函数

	> 这样可以避免生成中间临时数组

	```javascript
	const fun = x=> x + 1;
	
	// bad
	const arr = [...obj].map(fun);
	
	// good
	const arr = Array.from(obj, fun);
	```
	
- 4.7 使用数组回调函数时，如果可以用一行语句完成时，可以省略`return`

	```javascript
	[1, 2, 3].map(v=> v + 1); // => [2, 3, 4]
	
	[2, 4, 3].filter(v=> v % 2); // => [3]
	```
	
- 4.8 如果数组有多行，则在打开数组括号后和关闭数组括号前使用换行符

	```javascript
	// bad
	const arr = [
	  [0, 1], [2, 3], [4, 5],
	];
	
	const objArr = [{
	  id: 1,
	}, {
	  id: 2,
	}];
	
	const numArr = [
	  1, 2,
	];
	
	// good
	const arr = [[0, 1], [2, 3], [4, 5]];
	
	const objArr = [
	  {
	    id: 1,
	  },
	  {
	    id: 2,
	  },
	];
	
	const numArr = [
	  1,
	  2,
	];
	```
	
**[⬆ back to top](#内容列表)**

## 解构

- 5.1 在使用多个对象属性时，使用解构。

	> 修改解构定义的变量无法修改对象真实值
	
	```javascript
	// bad
	function getName(obj) {
		const a = obj.a,
		      b = obj.b;
		      
		return `${a} ${b}`;
	}
	
	// good
	function getName(obj){
		const {a, b} = obj;
		
		return `${a} ${b}`;
	}
	
	// best
	function getName({a, b}) {
		return `${a} ${b}`;
	}
	```
	
- 5.2 数组解构

	```javascript
	const arr = [1, 2, 3];
	
	// bad
	const a = arr[0],
	      b = arr[1];
	      
	// good
	const [a, b] = arr;
	```
	
- 5.3 对需要多个返回值时，使用对象解构而不是数组解构

	> 这样可以便于以后扩容，而不至于出错，且可以指定使用哪些返回内容
	
	```javascript
	// bad
	function test(obj) {
		return [left, right, top, bottom];
	}
	
	const [left, _, top] = test(obj);
	
	// good
	function test(obj) {
		return {left, right, top, bottom};
	}
	
	const {left, top} = test(obj);
	```
	
**[⬆ back to top](#内容列表)**

## 字符串

- 6.1 使用双引号。如果没有动态内容，不使用字符串模板

	```javascript
	// bad
	const name = 'this is name';
	
	const name = `this is name`;
	
	// good
	const name = "this is name";
	```
	
- 6.2 确保字符串在一行内完成

	> 过长的字符串、换行符会造成识别异常。

	```javascript
	// bad
	const msg = "This is a super long error that was thrown because \
of Batman. When you stop to think about how Batman had anything to do \
with this, you would get nowhere \
fast.";

	const msg = "This is a super long error that was thrown because " +
	"of Batman. When you stop to think about how Batman had anything to do " +
	"with this, you would get nowhere fast."
	
	// good
	const msg = "This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.";
	```
	
- 6.3 有动态内容是，使用字符串模板，而不是字符串拼接

	```javascript
	const name = "test";
	
	// bad
	const a = "How are you, " + name + " ?";
	
	const a = ["How are you,", name, "?"].join(" ");
	
	// good
	const a = `How are you, ${name} ?`;
	```
	
- 6.4 永远不要对字符串使用`eval()`

	> 当有特殊需求且你完全确认执行效果时才能使用，且非常不建议
	
- 6.5 谨慎使用转义符，切只允许在双引号内使用

	> 转义符因只用于字符串内的双引号转义。并会降低可读性
	
	```javascript
	// bad
	const a = `this is \"name\"`;
	const a = "this is \'name\'";
	
	// good
	const a = "this is \"name\"";
	```
**[⬆ back to top](#内容列表)**

## 函数

- 7.1 使用命名函数方式，而不是函数声明

	> 函数声明，会造成函数被自动挂载于根节点中，并全局有效。
	> 命名函数方式，可以有效的控制函数的作用范围，通过完整的函数名称进行有效的函数功能区分
	
	```javascript
	// bad
	function a() {
	}
	
	// good
	const a = function() {};
	
	// best
	const a = function getKeyByObject() {};
	```
	
- 7.2 将需要立即调用的函数过程内容，写在匿名函数内

	> 这样可以有效的保护调用时所用到的临时变量不会污染其他作用域变量
	
	```javascript
	(function(){
		let a = 1;
		console.log(a);
	})()
	```
	
- 7.3 永远不要在非函数块中声明函数，如`if`、`while`、`for`、`switch`，一般环境允许这么做，但会导致作用范围被限定，且会重复声明过程

- 7.4 *注意*: `ECMA-262`将块定义为语句，但函数声明不是语句

	```javascript
	// bad
	if (true) {
	  function test() {
	    console.log('Nope.');
	  }
	}
	
	// good
	let test;
	if (true) {
	  test = () => {
	    console.log('Yup.');
	  };
	}
	```
	
- 7.5 永远不要将函数参数定义为`arguments`，这是默认名称，优先被系统使用

	```javascript
	// bad
	function a(a, b, arguments){
	}
	
	// good
	function a(a, b, args){
	}
	```
	
- 7.6 尽量不要使用`arguments`内置对象，而改用操作符`...`

	> `arguments`是类数组对象，不便于后续传值，且绝对不允许作为返回值输出
	
	```javascript
	// bad
	function a(){
		const args = Array.prototype.slice.call(arguments);
		
		return args.join(" ");
	}
	
	function b() {
		return arguments;
	}
	
	// good
	function a(...args){
		return args.join(" ");
	}
	
	function b(...args){
		return args;
	}
	```
	
- 7.7 使用默认参数语法，而不是修改函数参数

	```javascript
	// bad
	function a(opts) {
		opts = opts || {};
	}
	
	function a(opts) {
		if(opts === void 0) opts = {};
	}
	
	// good
	function a(opts= {}){}
	```
	
- 7.8 避免将默认参数与动态变量进行关联

	> 这会导致默认参数产生变化
	
	```javascript
	let b = 1;
	function a(v = b++){
		console.log(v);
	}
	
	a(); // 1
	a(); // 2
	a(); // 3
	a(3); // 3
	a(); // 4 
	```
	
- 7.9 总是将默认参数放在最后

	```javascript
	// bad
	function a(opts = {}, name){}
	
	// good
	function a(name, opts = {}){}
	```
	
- 7.10 永远不要用`new Function`方式创建函数

	> 这类似于用`eval()`生成代码，会造成安全漏洞
	
	```javascript
	// bad
	let a = new Function("a","b","return a-b");
	let a = Function("a","b","return a-b");
	```
	
- 7.11 尽量使用操作符`...`，作为数组类参数的输入方式

	```javascript
	const a = [1, 2, 3];
	// bad
	console.log.apply(console, a); // => 1 2 3
	
	// good
	console.log(...a);
	```
	
	```javascript
	// bad
	new (Function.prototype.bind.apply(Date, [null, 2019, 1, 1]));
	
	// good
	new Date(...[2019, 1, 1]);
	```
	
**[⬆ back to top](#内容列表)**

## 箭头函数

- 8.1 当需要使用匿名函数时，应当使用箭头函数

	> 箭头函数适用于不需要重复使用的环境，这将代码更简洁
	> 箭头函数无法使用`this`，他的作用域继承函数外层作用域
	
	```javascript
	// bad
	[1, 2, 3].map(function(x){
		const y = x + 1;
		return x * y;
	});
	
	// good
	[1, 2, 3].map(x => {
		const y = x + 1;
		return x * y;
	});
	```
	
- 8.2 如果函数主体可以用一条语句表达，那可以省略`{}`及`return`

	> 当用省略`{}`时，箭头语句会以单条语句的返回值作为函数返回值

	```javascript
	// bad
	[1, 2, 3].map(num => {
		const next = num + 1;
		`this is next ${next}`;
	});
	
	// good
	[1, 2, 3].map(num => `this is next ${num + 1}`);
	
	let out = 1;
	function change(callback) {
		const val = callback();
		console.log(val === true ? 1 : 2);
	}
	
	// bad
	change(() => out = true); // => 1
	
	// good
	change(() => {
		out = true;
	}); // => 2
	```
	
- 8.3 如果当行函数主体书写时需要用到多行时，用`()`包裹

	```javascript
	const arr = {1: "1", 2:"2", 3:"3"];
	// bad
	[1, 2].map(x => Object.prototype.hasOwnProperty.call(
		1,
		2
	));
	
	// good
	[1, 2].map(x => (
		Object.prototype.hasOwnProperty.call(
			1,
			2
		)
	));
	```
	
- 8.4 如果函数参数只用到一个参数，可以省略参数的`()`

	```javascript
	// bad
	[1, 2, 3].map((x) => x + 1);
	
	// good
	[1, 2, 3].map(x => x + 1);
	```
	
- 8.5 在箭头函数中，禁止使用`=>`、`=<`这样的比较语句，而因使用`>=`、`<=`，并用`()`包裹

	```javascript
	// bad
	const getWidth = item => item.width =< 10 ? 1 : 2;
	
	// good
	const getWidth = item => (item.width <= 10 ? 1 : 2);
	
	// good
	const getWidth = item => {
		const {width} = item;
		return width <= 10 ? 1 : 2;
	};
	```
	
- 8.6 在省略`{}`且未使用`()`包裹函数主体的情况下，不允许分行书写

	```javascript
	// bad
	a => 
	  b;
	  
	a => 
	  (b);
	  
	// good
	a => b;
	a => (b);
	a => (
	  b
	);
	```
	
**[⬆ back to top](#内容列表)**

## 类

- 9.1 总是使用`class`，避免通过`prototype`来进行类设计

	> `class`语法更简洁，更容易推断
	
	```javascript
	// bad
	function test(opts = []){
		this.list = [...opts];
	}
	test.prototype.pop = function(){
		const val = this.list[0];
		return val;
	}
	
	// good
	class test {
		constructor(opts = []) {
			this.list = [...opts];
		}
		
		pop() {
			const val = this.list[0];
			return val;
		}
	}
	```
	
- 9.2 使用`extends`进行类派生

	> 派生类中，可以使用`super`获取父类对象
	> 父类对象以本类对象为依据进行创建

	```javascript
	// good
	class test {
		constructor(opts = []) {
			this.list = [...opts];
		}
		
		pop() {
			const val = this.list[0];
			return val;
		}
	}
	class test2 extends test {
		pop() {
			let val = super.pop();
			return val + 1;
		}
	}
	```
	
- 9.3 不需要明确返回的函数，应当返回`this`，以确保函数可以连写

	```javascript
	// bad
	class test {
		constructor(){
			this._log = 1;
		}
		
		log1(v){
			this._log += v;
		}
		log2(v){
			this._log = v;
		}
	}
	
	let a = new test();
	a.log1(1);
	a.log2(2);
	
	// good
	class test {
		constructor(){
			this._log = 1;
		}
		
		log1(v) {
			this._log += v;
			return this;
		}
		log2(v) {
			this._log = v;
			return this;
		}
	}
	
	let a = new test();
	a.log1(1)
	 .log2(2);
	```
	
- 9.4 为了便于调试，应该对每个类重新`toString()`函数

	```javascript
	class test {
		constructor() {
			this._a = 1;
			this._b = 2;
		}
		
		toString() {
			return `a: ${this._a}, b: ${this._b}`;
		}
	}
	```
	
- 9.5 当类函数功能与父类一直时（包括构造函数），可以省略声明

	```javascript
	class a {
		constructor() {
			this._a = 1;
		}
		log(){
			this._a++;
			return this;
		}
	}
	
	// bad
	class b extends a {
		constructor(...args) {
			super(...args);
		}
		log() {
			return super.log();
		}
		out() {
			this._a = 1;
			return this;
		}
	}
	
	// good
	class b extends a {
		out() {
			this._a = 1;
			return this;
		}
	}
	```
	
- 9.6 避免重复的对象

	```javascript
	// bad
	class test {
		a() { return 1; }
		b() { return 2; }
	}
	```
	
- 9.7 类设计中，不允许使用箭头函数，并且每个函数都必须有`return`

	> `class`函数的本质是`function`的简写，并需要通过`this`引用类对象内容，箭头函数无法支持`this`引用
	
**[⬆ back to top](#内容列表)**

## 模块

	> 后期补充
	
**[⬆ back to top](#内容列表)**

## 迭代器

- 11.1 不要使用迭代器，尽量使用javascript提供的函数，而不是像`for-in`、`for-of`这样的循环

	```javascript
	const arr = [1, 2, 3];
	let sum = 0;
	
	// bad
	for(let v of arr) {
		sum += v;
	}
	
	// good
	arr.forEach(v => sum += v);
	
	// best
	sum = arr.reduce(((total, v) => total + v), 0);
	```

**[⬆ back to top](#内容列表)**

## 属性

- 12.1 访问属性是使用`.`符号

	```javascript
	const json = {
		a: true,
		b: 10
	};
	
	// bad
	const a = json["a"];
	
	// good
	const a = json.a;
	```
	
- 12.2 使用`[]`访问带有变量的属性

	```javascript
	const json = {
		a: true,
		b: 10
	};
	
	function getProp(name){
		return json[name];
	}
	
	console.log(getProp(“a"));
	```

**[⬆ back to top](#内容列表)**

## 变量

- 13.1 所有的变量都必须定义`const`、`let`

	```javascript
	// bad
	a = 123;
	
	// good
	const a = 123;
	```

- 13.2 变量声明放在代码合适的位置，如果某段代码不需要使用，应把声明置后

	```javascript
	// bad
	function test(name){
		const val = `${name}_123`;
		
		if(name) return false;
		
		if(val) return false;
		
		return val; 
	}
	
	// good
	function test(name){
		if(name) return false;
		
		const val = `${name}_123`;
		
		if(val) return false;
		
		return val;
	}
	```
	
- 13.3 不要使用链接变量方式声明并赋值变量

	> 链接变量赋值会隐式创建全局变量

	```javascript
	// bad
	(function(){
		let a = b = 1;
	})();
	console.log(a); // => 1
	console.log(b); // => 变量未定义
	
	// good
	(function(){
		let a = 1;
		let b = a;
	})();
	console.log(a); // => 变量未定义
	console.log(b); // => 变量未定义
	```
	
- 13.4 避免使用`++`、`--`语法

	> 在特定编译环境是，一元递增或递减语句会受到异常判定，导致值不正确
	> 一元操作符在变量之前与之后存在完全不同的结果，应避免误解
	
	```javascript
	let num = 1;
	
	// bad
	num++;
	--num;
	
	// good
	num += 1;
	num -= 1;
	```
	
- 13.5 不允许声明未使用的变量

	```javascript
	// bad
	function test() {
		let a = 123;
		
		return this;
	}
	```
	
**[⬆ back to top](#内容列表)**

## 运算

- 14.1 使用`===`、`!==`代替`==`、`!=`

- 14.2 条件语句(比如`if`)，使用`ToBoolean`抽象方法强制求值是，遵循以下规则

	- *Object*对象 = *true*
	- *undefined* = *false*
	- *null* = *false*
	- *number*对象中 *+0*、*-0*、*NaN* = *false*，其他为*true*
	- *string*对象中空字符串 = false

- 14.3 建议使用`14.2`的规则，进行简单条件判定

	> 但有部分例外如下

	```javascript
	// bad
	if(name) {}
	
	if(list.length){}
	
	// good
	if(name !== ""){}
	
	if(list.length > 0){}
	```
	
- 14.4 使用`{}`来包裹，含有定义声明的`case`语句

	> `switch`下的所有`case`共用一个作用域，所以相同名称会造成定义冲突
	
	```javascript
	// bad
	switch (true) {
		case 1:
			let x = 1;
			break;
		case 2:
			let x = 2;
			break;
	}
	
	// good
	switch (true){
		case 1: {
			let x = 1;
			break;
		}
		case 2: {
			let x = 2;
			break;
		}
	}
	```
	
- 14.5 使用三元表达式的时，确保语句写在同一行

- 14.6 避免使用简单的二元表达式

	```javascript
	// bad
	const t1 =  a ? a : b;
	const t2 = c ? true : false;
	const t3 = c ? false : true;
	
	// good
	const t1 = a || b;
	const t2 = !!c;
	const t3 = !c;
	```
	
- 14.7 当混合运算是，用`()`包裹单项

	```javascript
	// bad
	const t1 = a && b < 0 || c > 0 || d + 1 === 0;
	const t2 = a ** b - 5 % d;
	
	// good
	const t1 = (a && b < 0) || (c > 0) || (d + 1 === 0);
	const t2 = (a ** b) - (5 % d);
	```
	
- 14.8 不要使用选择符来代替控制语句

	```javascript
	// bad
	!isRunning && doThing();
	
	// good
	if(!isRunning) {
		doThing();
	}
	```

**[⬆ back to top](#内容列表)**

## 块

- 15.1 优先使用单行语句，并在单行时省略块

	```javascript
	// bad
	if (true)
		return false;
		
	function test() { return false; }
		
	// good
	if (true) return false;
	
	function test() {
		return false;
	}
	```
	
- 15.2 如果`if`块最终会执行`return`语句，这不需要后续的else块，并在包含返回值的`if`块后面的`else if`块中的返回可以分割为多个`if`块

	```javascript
	// bad
	function t1(){
		if(x) {
			return x;
		} else {
			return y;
		}
	}
	
	function t2(){
		if(x) {
			return x;
		} else if(y) {
			return y;
		}
	}
	
	// good
	function t1() {
		if(x){
			return x;
		}
		
		return y;
	}
	
	function t2() {
		if(x){
			return x;
		}
		
		if(y){
			return y;
		}
	}
	```

**[⬆ back to top](#内容列表)**

## 注释

- 16.1 使用`/** ... */`作为多行注释

	> 注释内容与`*`有一个空格间距

	```javascript
	// bad
	// mark function is test
	//
	// @param {String} tag
	// @return {Number} result
	function make(tag) {
	
	  // ...
	
	  return result;
	}
	
	// good
	/**
	 * mark function is test
	 *
	 * @param {String} tag
	 * @return {Number} result
	 * /
	function make(tag) {
	
	  // ...
	
	  return result;
	}
	```
	
- 16.2 使用`//`作为单行注释

- 16.3 使用保留词作为问题说明

	- `// TODO: 需要去做的事情及说明`
	- `// WARN: 需要警告的内容`
	- `// ERROR: 错误情况说明`
	- `// FIX: 修复状态及情况说明`

**[⬆ back to top](#内容列表)**
	
## 异步

- 17.1 不要使用Generator作为异步方案，应该使用`async`

	> `generator`函数非常容易引起误解，并且在异步流程时不可控
	
- 17.2 如果一定要使用`generator`函数（为了做某些环境下的适配），遵循书写规则

	```javascript
	// bad
	function * a() {}
	
	function*a(){}
	
	function *a(){}
	
	const a = function * () {}
	
	const a = function*() {}
	
	//good
	function* a() {}
	
	const a = function* (){}
	```

- 17.3 异步操作应使用`async/await`语法，搭配`Promise`扩展对象，让代码更便于阅读

	> `Promise`扩展对象，基于Promise对象，扩展了部分函数，让它更便于使用，写异步函数更贴近同步函数

	```javascript
	// bad
	async function t1() {
		return new Promise(function(resovle, reject){
			// do some thing
		});
	}
	
	async function test(){
		const a = t1();
		
		a
			.then(function(result){
				// done result do thing
			})
			.catch(function(err){
				// fail result do thing
			});
	}
	
	// good
	async function t1() {
		const dtd = jShow.Deferred(true);
		
		try{
			dtd.resolve(1);
		}
		catch(e){
			dtd.reject(e);
		}
		
		return dtd.promise();
	}
	
	async function test() {
		const dtd = jShow.Deferred(true);
		
		try{
			let result = await t1();
			
			if(result !== 1) throw 0;
			
			dtd.resolve(result);
		}
		catch(e){
			dtd.reject(e);
		}
		
		return dtd.promise();
	}
	```
