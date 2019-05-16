# jShow JavaScript 代码规范

*这是一个javascript代码规范，参照了[Airbnb](https://github.com/airbnb/javascript)*

> **注意**: 本指南假设您正在使用[jShow](https://github.com/j-show/jShow)，以及相关的插件。

其他语言规范

- [CSS & Sass](https://github.com/j-show/css)

## 内容列表

1. [类型](#type)
2. [定义]()
3. [对象]()
4. [数组]()

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
	
- 2.4 简化相同类型定义;

	```javascript
	// bad
	const a = 1;
	const b = 2;
	
	// good
	const a = 1,
	      b = 2;
	```
	
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