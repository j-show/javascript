# jShow JavaScript Style Guide

*A style guide to Javascript，refer to [Airbnb](https://github.com/airbnb/javascript)*

> **注意**: this guide assumes you are use [jShow](https://github.com/j-show/jShow)。

Other Style Guides

- [CSS & Sass](https://github.com/j-show/css)

## Table of Contents

1. [Types](#types)
2. [References](#references)
3. [Objects](#objects)
4. [Arrays](#arrays)
5. [Destructuring](#destructuring)
6. [Strings](#strings)
7. [Functions](#functions)
8. [Arrow Functions](#arrow-functions)
9. [Classes](#classes)
10. [Modules](#modules)
11. [Iterators](#iterators)
12. [Properties](#properties)
13. [Variables](#variables)
14. [Operators](#operators)
15. [Blocks](#blocks)
16. [Comments](#comments)
17. [Async](#async)

## Types

- 1.1 **Primitives**: When you access a primitive type you work directly on its value.

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
	
	- `symbols` cannot be faithfully polyfilled, so they should not be used when targeting browsers/environments that don’t support them natively.

- 1.2 **Complex**: When you access a complex type you work on a reference to its value, manipulating the child content directly changes all the referenced objects.
	
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

**[⬆ back to top](#table-of-contents)**

## References

- 2.1 Use `const` for all of your references, avoid using `var`.
	
	> Why? This ensures that you can’t reassign your references, which can lead to bugs and difficult to comprehend code.
	
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
	
- 2.2 If you must reassign references, use `let` instead of `var`.

	> Why? `let` is block-scoped rather than function-scoped like `var`.
	
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
	
- 2.3 Note that both `let` and `const` are block-scoped.

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
	
- 2.4 Use one `const` or `let` declaration per variable or assignment.

	> This leads to redundant code, but it's easier to check and read
	> Concatenation is allowed if the variable is declared only without assignment

	```javascript
	// bad
	const a = 1,
	      b = 2;
	      
	// good
	const a = 1;
	const b = 2;
	```
	
- 2.5 Group all your `const`s and then group all your `let`s.

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
	
**[⬆ back to top](#table-of-contents)**
	
## Objects

- 3.1 Use the literal syntax for object creation.

	```javascript
	// bad
	const obj = new Object();
	
	//good
	const obj = {};
	```
	
- 3.2 Use computed property names when creating objects with dynamic property names.

	> Why? They allow you to define all the properties of an object in one place.

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
	
- 3.3 Use object method shorthand.

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
	
- 3.4 Use property value shorthand.

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
	
- 3.5 Group your shorthand properties at the beginning of your object declaration.

	> Why? It’s easier to tell which properties are using the shorthand.

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
	
- 3.6 Only quote properties that are invalid identifiers.

	> Why? In general we consider it subjectively easier to read. It improves syntax highlighting, and is also more easily optimized by many JS engines.

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
	
- 3.7 Do not call `Object.prototype` methods directly, such as `hasOwnProperty`, `propertyIsEnumerable`, and `isPrototypeOf`.

	> Why? These methods may be shadowed by properties on the object in question - consider `{ hasOwnProperty: false }` - or, the object may be a null object (`Object.create(null)`).

	```javascript
	// bad
	console.log(object.hasOwnProperty("method"));
	
	// good
	console.log(Object.prototype.hasOwnProperty.call(object, "method"));
	
	// best
	console.log(jShow.hasOwnProperty(object, "method"));
	```
	
- 3.8 Prefer the object spread operator over [`Object.assign`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) to shallow-copy objects. Use the object rest operator to get a new object with certain properties omitted.

	```javascript
	const a = {a:1, b:2};
	
	// bad
	const b = Object.assign(a, {c: 3});
	
	// good
	const b = {...a, c: 3};
	```

**[⬆ back to top](#table-of-contents)**

## Arrays

- 4.1 Use the literal syntax for array creation.

	```javascript
	// bad
	const arr = new Array();
	
	// good
	const arr = [];
	```
	
- 4.2 Use [Array Method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#Methods_2) change arrays.

	```javascript
	const arr = [];
	
	// bad
	arr[arr.length] = "new Item";
	
	// good
	arr.push("new Item");
	```

- 4.3 Use array spreads `...` to copy arrays.

	```javascript
	const arr = [1, 2, 3];
	// bad
	const copy = [];
	      
	for (i=0; i<arr.length; i++) copy.push(arr[i]);
	
	// good
	const copy = [...arr];
	```
	
- 4.4 To convert an iterable object to an array, use spreads `...` instead of [`Array.from`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from).

	```javascript
	const obj = document.querySelectorAll(".all");
	
	// bad
	const node = obj.from(obj);
	
	// good
	const node = [...obj];
	```
	
- 4.5 Use [`Array.from`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from) for converting an array-like object to an array.

	```javascript
	const obj = {0: "a1", 1: "a2"};
	
	// bad
	const arr = Array.prototye.slice.call(obj);
	
	// good
	const arr = Array.from(obj);
	```

- 4.6 Use [`Array.from`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from) instead of spread `...` for mapping over iterables, because it avoids creating an intermediate array.

	```javascript
	const fun = x=> x + 1;
	
	// bad
	const arr = [...obj].map(fun);
	
	// good
	const arr = Array.from(obj, fun);
	```
	
- 4.7 Use return statements in array method callbacks. It’s ok to omit the return if the function body consists of a single statement returning an expression without side effects.

	```javascript
	[1, 2, 3].map(v=> v + 1); // => [2, 3, 4]
	
	[2, 4, 3].filter(v=> v % 2); // => [3]
	```
	
- 4.8 Use line breaks after open and before close array brackets if an array has multiple lines.

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
	
**[⬆ back to top](#table-of-contents)**

## Destructuring

- 5.1 Use object destructuring when accessing and using multiple properties of an object.

	> Why? Destructuring saves you from creating temporary references for those properties.
	> Modifying a deconstructed variable does not change the true value of the object
	
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
	
- 5.2 Use array destructuring.

	```javascript
	const arr = [1, 2, 3];
	
	// bad
	const a = arr[0],
	      b = arr[1];
	      
	// good
	const [a, b] = arr;
	```
	
- 5.3 Use object destructuring for multiple return values, not array destructuring.

	> Why? You can add new properties over time or change the order of things without breaking call sites.
	
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
	
**[⬆ back to top](#table-of-contents)**

## Strings

- 6.1 Use single quotes `"` for strings. template literals should contain interpolation or newlines.

	```javascript
	// bad
	const name = 'this is name';
	
	const name = `this is name`;
	
	// good
	const name = "this is name";
	```
	
- 6.2 Strings that cause the line to go over 100 characters should not be written across multiple lines using string concatenation.

	> Why? Broken strings are painful to work with and make code less searchable.

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
	
- 6.3 When programmatically building up strings, use template strings instead of concatenation.

	> Why? Template strings give you a readable, concise syntax with proper newlines and string interpolation features.

	```javascript
	const name = "test";
	
	// bad
	const a = "How are you, " + name + " ?";
	
	const a = ["How are you,", name, "?"].join(" ");
	
	// good
	const a = `How are you, ${name} ?`;
	```
	
- 6.4 Never use `eval()` on a string, it opens too many vulnerabilities.

	> When there are special needs and you confirm execution effect can use completely, and very not recommended
	
- 6.5 Do not unnecessarily escape characters in strings.

	> Why? Backslashes harm readability, thus they should only be present when necessary.
	
	```javascript
	// bad
	const a = `this is \"name\"`;
	const a = "this is \'name\'";
	
	// good
	const a = "this is \"name\"";
	```
**[⬆ back to top](#table-of-contents)**

## Functions

- 7.1 Use named function expressions instead of function declarations.

	> Function declaration, which causes the function to be automatically mounted in the root node and to be globally valid.
	> Naming function, can effectively control the scope of the function, through the complete name of the function to distinguish the effective function function.
	
	```javascript
	// bad
	function a() {
	}
	
	// good
	const a = function() {};
	
	// best
	const a = function getKeyByObject() {};
	```
	
- 7.2 Wrap immediately invoked function expressions in parentheses.

	> This effectively protects the temporary variables used in the call from contaminating other scoped variables
	
	```javascript
	(function(){
		let a = 1;
		console.log(a);
	})()
	```
	
- 7.3 Never declare a function in a non-function block (`if`, `while`, etc). Assign the function to a variable instead. Browsers will allow you to do it, but they all interpret it differently, which is bad news bears.

- 7.4 **Note:** ECMA-262 defines a `block` as a list of statements. A function declaration is not a statement.

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
	
- 7.5 Never name a parameter `arguments`. This will take precedence over the `arguments` object that is given to every function scope.

	```javascript
	// bad
	function a(a, b, arguments){
	}
	
	// good
	function a(a, b, args){
	}
	```
	
- 7.6 Never use `arguments`, opt to use rest syntax `...` instead.

	> Why? `...` is explicit about which arguments you want pulled. Plus, rest arguments are a real Array, and not merely Array-like like `arguments`.
	
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
	
- 7.7 Use default parameter syntax rather than mutating function arguments.

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
	
- 7.8 Avoid side effects with default parameters.

	> Why? They are confusing to reason about.
	
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
	
- 7.9 Always put default parameters last.

	```javascript
	// bad
	function a(opts = {}, name){}
	
	// good
	function a(name, opts = {}){}
	```
	
- 7.10 Never use the Function constructor to create a new function.

	> Why? Creating a function in this way evaluates a string similarly to `eval()`, which opens vulnerabilities.
	
	```javascript
	// bad
	let a = new Function("a","b","return a-b");
	let a = Function("a","b","return a-b");
	```
	
- 7.11 Prefer the use of the spread operator `...` to call variadic functions.

	> Why? It’s cleaner, you don’t need to supply a context, and you can not easily compose `new` with `apply`.

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
	
**[⬆ back to top](#table-of-contents)**

## Arrow Functions

- 8.1 When you must use an anonymous function (as when passing an inline callback), use arrow function notation.

	> Arrow function suitable for does not need to be repeated use of the environment, this code is more concise
	> Arrow function cannot use ` this `, he inherited the scope of the function of outer scope
	
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
	
- 8.2 If the function body consists of a single statement returning an [expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions) without side effects, omit the braces and use the implicit return. Otherwise, keep the braces and use a `return` statement.

	> When using ellipsis `{}`, arrows return value as a function of the sentence will be in a single statement return values

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
	
- 8.3 In case the expression spans over multiple lines, wrap it in parentheses for better readability.

	> Why? It shows clearly where the function starts and ends.

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
	
- 8.4 If your function takes a single argument and doesn’t use braces, omit the parentheses. Otherwise, always include parentheses around arguments for clarity and consistency. 

	> **Note**: it is also acceptable to always use parentheses
	
	> Why? Less visual clutter.
	
	```javascript
	// bad
	[1, 2, 3].map((x) => x + 1);
	
	// good
	[1, 2, 3].map(x => x + 1);
	```
	
- 8.5 In arrow function, it is prohibited to use `=>`, `=<` such a comparison, and should use `>=`、`<=` and `()` package

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
	
- 8.6 By omitting `{}` and unused `()` package, function is the main body, are not allowed to write branch

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
	
**[⬆ back to top](#table-of-contents)**

## Classes

- 9.1 Always use `class`. Avoid manipulating `prototype` directly.

	> Why? `class` syntax is more concise and easier to reason about.
	
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
	
- 9.2 Use `extends` for inheritance.

	> Why? It is a built-in way to inherit prototype functionality without breaking `instanceof`.
	> inheritance classes can use ` super ` superclass object
	
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
	
- 9.3 Methods can return `this` to help with method chaining.

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
	
- 9.4 It’s okay to write a custom `toString()` method, just make sure it works successfully and causes no side effects.

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
	
- 9.5 Classes have a default constructor if one is not specified. An empty constructor function or one that just delegates to a parent class is unnecessary.

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
	
- 9.6 Avoid duplicate class members.

	```javascript
	// bad
	class test {
		a() { return 1; }
		a() { return 2; }
	}
	```
	
- 9.7 Class design, are not allowed to use the arrow function, and each function must have a `return`

	> `class` function is the nature of `function` shorthand, and need a `this` content class object references, arrow function cannot support `this` references
	
**[⬆ back to top](#table-of-contents)**

## Modules

	> To be continued
	
**[⬆ back to top](#table-of-contents)**

## Iterators

- 11.1 Don’t use iterators. Prefer JavaScript’s higher-order functions instead of loops like `for-in` or `for-of`.

	> Why? This enforces our immutable rule. Dealing with pure functions that return values is easier to reason about than side effects.
	
	> Use `map()` / `every()` / `filter()` / `find()` / `findIndex()` / `reduce()` / `some()` / ... to iterate over arrays, and `Object.keys()` / `Object.values()` / `Object.entries()` to produce arrays so you can iterate over objects.

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

**[⬆ back to top](#table-of-contents)**

## Properties

- 12.1 Use dot notation when accessing properties.

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
	
- 12.2 Use bracket notation `[]` when accessing properties with a variable.

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

**[⬆ back to top](#table-of-contents)**

## Variables

- 13.1 Always use `const` or `let` to declare variables. Not doing so will result in global variables. We want to avoid polluting the global namespace.

	```javascript
	// bad
	a = 123;
	
	// good
	const a = 123;
	```

- 13.2 Assign variables where you need them, but place them in a reasonable place.

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
	
- 13.3 Don’t chain variable assignments.

	> Why? Chaining variable assignments creates implicit global variables.

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
	
- 13.4 Avoid using unary increments and decrements (`++`, `--`).

	> Why? Per the eslint documentation, unary increment and decrement statements are subject to automatic semicolon insertion and can cause silent errors with incrementing or decrementing values within an application. It is also more expressive to mutate your values with statements like `num += 1` instead of `num++` or `num ++`. Disallowing unary increment and decrement statements also prevents you from pre-incrementing/pre-decrementing values unintentionally which can also cause unexpected behavior in your programs.
	
	```javascript
	let num = 1;
	
	// bad
	num++;
	--num;
	
	// good
	num += 1;
	num -= 1;
	```
	
- 13.5 Disallow unused variables.

	```javascript
	// bad
	function test() {
		let a = 123;
		
		return this;
	}
	```
	
**[⬆ back to top](#table-of-contents)**

## Operators

- 14.1 Use `===` and `!==` over `==` and `!=`.

- 14.2 Conditional statements such as the `if` statement evaluate their expression using coercion with the `ToBoolean` abstract method and always follow these simple rules:

	- **Objects** evaluate to **true**
	- **Undefined** evaluates to **false**
	- **Null** evaluates to **false**
	- **Booleans** evaluate to **the value of the boolean**
	- **Numbers** evaluate to **false** if **+0, -0, or NaN**, otherwise **true**
	- **Strings** evaluate to **false** if an empty string `""`, otherwise **true**

- 14.3 Use shortcuts for booleans, but explicit comparisons for strings and numbers.

	```javascript
	// bad
	if(name) {}
	
	if(list.length){}
	
	// good
	if(name !== ""){}
	
	if(list.length > 0){}
	```
	
- 14.4 Use braces to create blocks in `case` and `default` clauses that contain lexical declarations (e.g. `let`, `const`, `function`, and `class`).

	> Why? Lexical declarations are visible in the entire `switch` block but only get initialized when assigned, which only happens when its `case` is reached. This causes problems when multiple `case` clauses attempt to define the same thing.
	
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
	
- 14.5 Ternaries should not be nested and generally be single line expressions.

- 14.6 Avoid unneeded ternary statements.

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
	
- 14.7 When mixing operators, enclose them in parentheses. The only exception is the standard arithmetic operators (`+`, `-`, `*`, & `/`) since their precedence is broadly understood.

	> Why? This improves readability and clarifies the developer’s intention.

	```javascript
	// bad
	const t1 = a && b < 0 || c > 0 || d + 1 === 0;
	const t2 = a ** b - 5 % d;
	
	// good
	const t1 = (a && b < 0) || (c > 0) || (d + 1 === 0);
	const t2 = (a ** b) - (5 % d);
	```
	
- 14.8 Don't use selection operators in place of control statements.

	```javascript
	// bad
	!isRunning && doThing();
	
	// good
	if(!isRunning) {
		doThing();
	}
	```

**[⬆ back to top](#table-of-contents)**

## Blocks

- 15.1 Use braces with all multi-line blocks.

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
	
- 15.2 If you’re using multi-line blocks with `if` and `else`, put `else` on the same line as your `if` block’s closing brace.

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

**[⬆ back to top](#table-of-contents)**

## Comments

- 16.1 Use `/** ... */` for multi-line comments.

	> Start all comments with a space to make it easier to read.

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
	
- 16.2 Use `//` for single line comments. Place single line comments on a newline above the subject of the comment. Put an empty line before the comment unless it’s on the first line of a block.

- 16.3 Prefixing your comments with `FIX` or `TODO` helps other developers quickly understand if you’re pointing out a problem that needs to be revisited, or if you’re suggesting a solution to the problem that needs to be implemented. These are different than regular comments because they are actionable. The actions are `FIX: -- need to figure this out` or `TODO: -- need to implement`.

	- `// TODO: Need to do and instructions`
	- `// WARN: What to warn about`
	- `// ERROR: Error information and instructions`
	- `// FIX: Repair status and situation description`

**[⬆ back to top](#table-of-contents)**
	
## Async

- 17.1 Don't use the Generator as asynchronous solution, should use ` async `

	> `generator function` is very easy to cause misunderstanding, and uncontrolled during asynchronous processes
	
- 17.2 If you must use `generator function` (adapter) in order to do certain environment, follow the writing rules

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

- 17.3 Asynchronous operations should use `async/await` syntax, tie-in `Promise` extended object, make the code easier to read

	> `Promise` extended object, based on object Promise, expanded the part function, make it more easy to use, write asynchronous functions closer to the synchronization function

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
