# let &&  const
### let
1) 基本用法
	```javascript
	var a = [];
	for (let i = 0; i < 10; i++) {
	  a[i] = function() {
	    console.log(i);
	  };
	}
	a[6](); // 6  let 声明, 块级作用域, i在循环作用域内
	```
2) 暂时性死区
	+ 只要块级作用域内存在let命令，它所声明的变量就“绑定”(binding)这个区域，不再受外部影响。
	+ 总之，在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”(temporal dead zone，简称 TDZ)
	+ 暂时性死区也意味着typeof不再是一个百分百安全的操作
	```javascript
	typeof x ; // ReferenceError
	let x;
	 ```
	```javascript
	var x = x   //不报错
	let x = x   // 报错， ReferenceError: x is not defined
	```
3) 不允许重复声明
4) 不存在变量提升
### 块级作用域
1. 为什么需要块级作用域
	+ ES5只有全局作用域和函数作用域，没有块级作用域，这带来很多不合理的场景，如：
		+ 内层变量可能会覆盖外层变量
		+ 用来计数的循环变量泄露为全局变量
2. 块级作用域与函数声明
	+ ES6允许在块级作用域内声明的函数处理规则
		+ 允许在块级作用域内声明函数
		+ 函数声明类似于var, 即会提升到全局作用域或函数作用域的头部
		+ 同时，函数声明还会提升到所在的块级作用域的头部
	+ 考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成`函数表达式`，而不是函数声明语句
	+ ES6的块级作用域允许声明函数的规则，只在使用大括号的情况下成立
### const
1. const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。原始类型(数值、字符串、布尔值)，值就是变量所指向的内存地址，不能改动。但对于复合类型的数据(主要是对象和数组)，变量指向的内存地址，保存的只是一个指向实际数据的指针。可为其添加属性，但是不能指向另一个对象或数组。
2. let 和 const
	1) 在let和const之间，建议优先使用const，尤其在全局环境，不应该设置变量，只应设计常量
	2) const比较符合函数式的编程思想，运算不改变值，只是新建值
	3) JavaScript编译器会对const进行优化，所以多用const，有利于提高程序的运行效率，也就是说let和const的本质区别，其实是编译器内部的处理不同
3. ES6声明变量的6种方法 ： var、 function、 let、 const、 import、 class
### 顶层对象的属性
1. 顶层对象，在浏览器环境指的是window对象，在Node指的是global对象。ES5之中，顶层对象的属性与全局变量是等价的。
2. 这样设计所带来的问题
	1) 没法在编译时就报出变量未声明的错误，只有运行时才能知道(因为全局变量可能是顶层对象的属性创造的，而属性的创造是动态的)
	2) 程序员很容易不知不常见地就创建了全局变量
	3) 顶层对象的属性是到处可以读写的，这非常不利于模块化编程
	4) winodw对象有实体含义，指的是浏览器窗口对象，顶层对象是具有实体含义的，也是不合适的
3. ES6为了改变这一点，一方面规定，为了保持兼容性，var和function命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，let、const、class命令声明的全局变量，不属于顶层对象的属性。
	```javascript
	var a = 1   window.a   //1
	let a = 1   window.a   // undefined
	```
### global对象
1. ES5的顶层对象，本身也是一个问题，因为它在各种实现里面是不统一的。
	1) 浏览器里面，顶层对象是window，但Node和Web Worker没有window
	2) 浏览器和Web Worker里面，self也指向顶层对象，但Node没有self
	3) Node里面，顶层对象是global，但其他环境都不支持
2. 同一份代码为了能够在各个环境，都能取到顶层对象，现在一般是使用this变量，但有局限性
	1) 全局环境中，this会返回顶层对象，但是Node模块和ES6模块中，this返回的是当前模块
	2) 函数里面的this，如果函数不是作为对象的方法运行，而是单纯作为函数运行，this会指向顶层对象。但是，严格模式下，这时this会返回undefined
	3) 不管是严格模式，还是普通模式，new Function('return this')()，总会返回全局对象，但是，如果浏览器用了CSP(Content Security Policy,内容安全策略)，那么eval、new Function这些方法都可能无法使用
	```javascript
	// 获得顶层对象的方法
	var getGlobal = function(){
	  if(typeof self !== 'undefined'){ return self;}
	  if(typeof window !=='undefined'){ return window;}
	  if(typeof global !=='undefined'){ return global;}
	  throw new Error('unable to locate global object')
	}
	```
________
# 解构赋值
### 基本用法
1. ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构<br>
	`let [a,b,c] = [1,2,3]` <br>
	+ 本质上，这种写法属性“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值
	+ 如果解构不成功，变量的值就等于undefined
	+ 只要某种数据结构具有Iterator接口，都可以采用数组形式的解构赋值
2. 默认值
	<br>&ensp;&ensp;&ensp;&ensp;解构赋值允许指定默认值，ES6内部使用严格相等运算符(===)，判断一个位置是否有值。所以，只有当一个数组成员严格等于undefined,默认值才会生效
	```javascript
	let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
	let [x, y = 'b'] = ['a', null]; // x='a', y=null
	```
### 对象的解构赋值
+ 对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到取到正确的值。
	```javascript
	let { foo: baz } = { foo: "aaa", bar: "bbb" };
	baz // "aaa"
	foo // error: foo is not defined
	/*
	对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者。foo是匹配的模式，baz才是变量
	*/
	```
+ 解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。
### 函数参数的解构赋值
```javascript
function move({x=0,y=0}={}){
  return [x,y]
}
move({x:3,y:8})   //[3,8]
move({})          //[0,0]
```
```javascript
function move({x:y}={x:0,y:0}){
  return [x,y]
}
move({x:3,y:8})  //[3,8]
move({})         //[undefined,undefined]
```
&ensp;&ensp;&ensp;&ensp;两种写法都对函数的参数设定了默认值，区别是写法一函数参数的默认值是空对象，但是设置了对象解构赋值的默认值；写法二函数参数的默认值是一个有具体属性的对象，但是没有设置对象解构赋值的默认值。
### 圆括号问题
1. ES6的规则是，只要有可能导致解构的歧义，就不得使用圆括号。
2. 不能使用圆括号的情况
	+ 变量声明语句
	+ 函数参数
	+ 赋值语句的模式
3. 可以使用圆括号的情况只有一种：赋值语句的非模式部分
__________

# 字符串扩展
1. 字符的Unicode表示法
	+ JavaScirpt允许采用\uxxxx形式表示一个字符，其中xxxx表示字符的Unicode码点。但是，这种表示法只限于码点在\uxxxx ~ \uFFFF之间的字符。超出这个范围的字符，必须用两个双字节表示。
	+ ES6对这一点做出了改进，只要将码点放入大括号，就能正确解读该字符
2. codePointAt()
	+ JavaScript内部，字符以UTF-16的格式储存，每个字符固定为2个字节，对于那些需要4个字节储存的字符，JavaScript会认为它们是两个字符
	+ codePointAt()能够正确处理4个字节储存的字符，返回一个字符的码点。对于那些两个字节储存的常规字符，它的返回结果与charCodeAt()相同
3. String.fromCodePoint()
	+ ES5提供String.fromCharCode方法，用于从码点返回对应字符，但是这个方法不能识别32位的UTF-16字符(Unicode编号大于0xFFFF)
	+ ES6提供了String.fromCodePoint(),可以识别大于0xFFFF的字符，弥补了String.fromCharCode()的不足。
	+ fromCodePoint方法定义在String对象上，而codePointAt方法定义在字符串的实例对象上。
4. for...of循环
	+ 除了遍历字符串，这个遍历器的最大优点是可以识别大于0xFFFF的码点，传统的for循环无法识别这样的码点。
5. 检测字符串是否包含某字符串
	+ includes() ：返回布尔值，表示是否找到了参数字符串
	+ startsWith() ：返回布尔值，表示参数字符串是否在原字符串的头部
	+ endsWith() ：返回布尔值，表示参数字符串是否在原字符串的尾部
	+ 这三个方法都支持第二个参数，表示开始搜索的位置，使用第二个参数n时，endsWith的行为与其他两个方法有所不同。它针对前n个字符，而其他两个方法针对从第n个位置直到字符串结束
6. 字符串重复
	+ repeat()
7. 字符串补全
	+ ES2017引入了字符串实例长度的功能。
	+ padStart() 头部补全
	+ padEnd()  尾部补全
8. 模板字符串 ``
_____________
# 正则的扩展
1. u修饰符
	+ ES6对正则表达式添加了u修饰符，含义为"Unicode模式"，用来正确处理大于\uFFFF的Unicode字符。
	+ ES6新增了使用大括号表示Unicode字符，这种表示法在正则表达式中必须加上u修饰符，才能识别当中的大括号，否则会被解读为量词。

# 函数的扩展
### 函数参数的默认值
1. 基本用法
	+ ES6之前，不能直接为函数的参数指定默认值，ES6允许为函数的参数设置默认值，即直接写在参数定义的后面
	+ 参数默认值不是传值的，而是每次都重新计算默认值表达式的值，也就是说，参数默认值是惰性求值的。
2. 函数的length属性
	+ length属性的含义是，该函数预期传入的参数个数。某个参数指定默认值后，预期传入的参数个数就不包括这个参数了。同理，rest参数也不会计入length属性
	+ 如果设置了默认值的参数不是尾参数，那么length属性也不再计入后面的参数了。
3. 作用域
	+ 一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域。等到初始化结束，这个作用域就会消失。
		```javascript
		var x = 1;
		function foo(x, y = function(){ x = 2; }){
		  var x = 3;
	    y();
		  console.log(x)
		}
		foo() //3
		/*
		函数foo的参数形成一个单独作用域。这个作用域里面，匿名函数内部的变量x，指向同一个作用域的第一个参数x。
		函数foo内部又声明了一个内部变量x，该变量与第一个参数x由于不是同一个作用域，所以不是同一个变量。
		*/
		```
		```javascript
		var x = 1;
		function foo(x, y = function(){ x = 2; }){
		  x = 3;
		  y();
	    console.log(x)
		}
		foo() //2
		/*
		去掉var后，函数foo的内部变量x指向第一个参数x，与匿名函数内部的x一致
		*/
		```
### rest参数
+ ES6引入rest参数(...变量名)，用于获取函数的多余参数，这样就不需要使用arguments对象了。rest参数搭配的变量是一个数组，该变量将多余的参数放入数组中。
+ rest参数之后不能再有其他参数，即只能是最后一个参数
+ 函数的length属性，不包括rest参数
### 严格模式
+ 从ES5开始，函数内部可以设定为严格模式。ES2016做了一点修改，规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格格式，否则会报错。
+ 可设定全局性的严格模式，或者把函数包在一个无参数的立即执行函数里面
### 箭头函数
1. 基本用法
	+ ES6允许使用"箭头(=>)"定义函数，如果箭头函数不需要参数或者需要多个参数，就使用一个圆括号代表参数部分
	+ 如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用return语句返回
	+ 由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号
	+ 如果箭头函数只有一行语句，且不需要返回值，可以采用下面的写法，就不用写大括号了。<br>
		```javascript
		let fn = () => void doesNotReturn();
		```
	+ 箭头函数可以与变量解构结合使用。<br>
		```javascript
		const full = ({ first, last }) => first + ' ' + last;
		```
2. 使用注意点
	1) 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象
	2) 不可以当作构造函数，也就是说，不可以使用`new`命令，否则会抛出一个错误
	3) 不可以使用`arguments`对象，该对象在函数体内不存在。可以用rest参数代替
	4) 不可以使用`yield`命令，因此箭头函数不能用作Generator函数
3. 不能使用箭头函数的情况
	1) 定义字面量方法
		```javascript
		const calculator = {
		  sum: () => {
		    console.log(this === window); // => true
		  }
		};
		// Throws "TypeError: Cannot read property 'sum' of undefined"
		calculator.sum();
		```
	2) 定义原型方法
	3) 定义事件回调函数
		<br>&ensp;&ensp;&ensp;箭头函数在声明的时候就绑定了执行上下文，要动态改变上下文是不可能的，在需要动态上下文的时候它的弊端就凸显出来。比如在客户端编程中常见的 DOM 事件回调函数（event listenner）绑定，触发回调函数时 this 指向当前发生事件的DOM节点，而动态上下文这个时候就非常有用
	4) 定义构造函数
### 双冒号运算符
+ 箭头函数可以绑定this对象，大大减少了显式绑定this对象的写法(call、apply、bind)
+ 函数绑定运算符是并排的两个冒号`(::)`，双冒号左边是一个对象，右边是一个函数。该运算符会自动将左边的对象，作为上下文环境(即this对象)，绑定到右边的函数上面
___________________
# 数组的扩展
### 扩展运算符
+ 扩展运算符(spread)是三个点(...)。它好比rest参数的逆运算，将一个`数组`转为用逗号分隔的`参数序列`。
1. 替代函数的apply方法
	<br>由于扩展运算符可以展开数组，所以不再需要apply方法，将数组转为函数的参数了。
	```javascript
	// ES5 的写法
	function f(x, y, z) {}
	var args = [0, 1, 2];
	f.apply(null, args);
	// ES6的写法
	function f(x, y, z) {}
	let args = [0, 1, 2];
	f(...args);
	```
2. 复制数组
	+ 数组是复合的数据类型，直接复制的话，只是复制了指向底层数据结构的指针，而不是克隆一个全新的数组。
	+ ES5只能用变通方法来复制数组。
		```javascript
		const a1 = [1, 2];
		const a2 = a1.concat();
		a2[0] = 2;
		a1 // [1, 2]
		```
	+ 扩展运算符提供了复制数组的简便写法。
		```javascript
		const a1 = [1, 2];
		// 写法一
		const a2 = [...a1];
		// 写法二
		const [...a2] = a1;
		```
3. 合并数组
	```javascript
	const a1 = [{ foo: 1 }];
	const a2 = [{ bar: 2 }];
	const a3 = a1.concat(a2);
	const a4 = [...a1, ...a2];
	a3[0] === a1[0] // true
	a4[0] === a1[0] // true
	/*
	a3和a4是用两种不同方法合并而成的新数组，但是它们的成员都是对原数组成员的引用，这就是浅拷贝。如果修改了原数组的成员，会同步反映到新数组。
	*/
	```
4. 与解构赋值结合
	<br>扩展运算符可以与解构赋值结合起来，用于生成数组。
	```javascript
	// ES5
	a = list[0], rest = list.slice(1)
	// ES6
	[a, ...rest] = list
	```
5. 字符串
	+ 扩展运算符还可以将字符串转为真正的数组。<br>
		`[...'hello']`
	+ 有一个重要的好处，那就是能够正确识别四个字节的Unicode字符。
6. 实现了Iterator接口的对象
	+ 任何Iterator接口的对象，都可以用扩展运算符转为真正的数组。
	+ 对于那些没有部署Iterator接口的类似数组的对象，扩展运算符就无法将其转为真正的数组。可以改为使用Array.from方法将其转为真正的数组
7. Map和Set结构，Generator函数
	<br>扩展运算符内部调用的是数据结构的Iterator接口，因此只要具有Iterator接口的对象，都可以使用扩展运算符，比如Map结构
	```javascript
	let map = new Map([
	  [1, 'one'],
	  [2, 'two'],
	  [3, 'three'],
	]);
	let arr = [...map.keys()];  // [1, 2, 3]
	```
	<br>Generator函数运行后，返回一个遍历器对象，因此也可以使用扩展运算符。
	```javascript
	const go = function*(){
	  yield 1;
	  yield 2;
	  yield 3;
	};
	[...go()] // [1, 2, 3]
	```
### Array.from()
+ Array.from方法用于将两类对象转为真正的数组：类似数组的对象(array-like object)和可遍历(iterable)的对象(包括ES6新增的数据结构Set和Map)。
+ 所谓类似数组的对象，本质特征只有一点，即必须有length属性。因此，任何有length属性的对象，都可以通过Array.from方法转为数组，而此时扩展运算符就无法转换
+ 对于还没有部署该方法的浏览器，可以用Array.prototype.slice方法替代。
	```javascript
	const toArray = (() =>
	  Array.from ? Array.from : obj => [].slice.call(obj)
	)();
	```
+ Array.from还可以接受第二个参数，作用类似于数组的map方法，用来对每个元素进行处理，将处理后的值放入返回的数组。<br>
	```javascript
	Array.from([1, 2, 3], (x) => x + x)  // [1, 4, 9]
	```
+ 如果map函数里面用到了this关键字，还可以传入Array.from的第三个参数，用来绑定this。
### Array.of()
+ Array.of方法用于将一组值，转换为数组。
	```javascript
	Array.of(3, 11, 8) // [3,11,8]
	Array.of(3) // [3]
	Array.of(3).length // 1
	```
+ 这个方法的主要目的，是弥补数组构造函数Array()的不足。因为参数个数的不同，会导致Array()的行为有差异。只有当参数个数不少于2个时，Array()才会返回由参数组成的新数组。参数个数只有一个时，实际上是指定数组的长度。
	```javascript
	Array() // []
	Array(3) // [, , ,]
	Array(3, 11, 8) // [3, 11, 8]
	```
+ Array.of基本上可以用来替代Array()或new Array()，并且不存在由于参数不同而导致的重载。它的行为非常统一。
	```javascript
	Array.of() // []
	Array.of(undefined) // [undefined]
	Array.of(1) // [1]
	Array.of(1, 2) // [1, 2]
	```
+ Array.of方法可以用下面的代码模拟实现。
	```javascript
	function ArrayOf(){
	  return [].slice.call(arguments);
	}
	```
### 数组实例的copyWithin()
<br>&ensp;&ensp;&ensp;&ensp;数组实例的copyWithin方法，在当前数组内部，将指定位置的成员复制到其他位置(会覆盖原有成员)，然后返回当前数组。也就是说，使用这个方法，会修改当前数组。
```javascript
Array.prototype.copyWithin(target, start = 0, end = this.length)
```
### 数组实例的find()和findIndex()
+ 数组实例的find方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为true的成员，然后返回该成员。如果没有符合条件的成员，则返回undefined。
+ 数组实例的findIndex方法的用法与find方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1。
+ 这两个方法都可以接受第二个参数，用来绑定回调函数的this对象。
	```javascript
	function f(v){
	  return v > this.age;
	}
	let person = {name: 'John', age: 20};
	[10, 12, 26, 15].find(f, person);    // 26
	```
+ 这两个方法都可以发现NaN，弥补了数组的indexOf方法的不足，indexOf方法无法识别数组的NaN成员，但是findIndex方法可以借助Object.is方法做到。
### 数组实例的fil()
+ fill方法使用给定值，填充一个数组。
	```javascript
	new Array(3).fill(7)  // [7, 7, 7]
	```
+ fill方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。
	```javascript
	['a', 'b', 'c'].fill(7, 1, 2)  // ['a', 7, 'c']
	```
+ 如果填充的类型为对象，那么被赋值的是同一个内存地址的对象，而不是深拷贝对象。
	```javascript
	let arr = new Array(3).fill({name: "Mike"});
	arr[0].name = "Ben";
	arr   // [{name: "Ben"}, {name: "Ben"}, {name: "Ben"}]
	let arr = new Array(3).fill([]);
	arr[0].push(5);
	arr  // [[5], [5], [5]]
	```
### 数组实例的entries(), keys()和values()
+ 它们都返回一个遍历器对象，可以用for...of循环进行遍历。
+ 如果不使用for...of循环，可以手动调用遍历器对象的next方法，进行遍历。
### 数组实例的includes()
1. Array.prototype.includes方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的includes方法类似。ES2016引入了该方法。
	```javascript
	[1, 2, 3].includes(2)     // true
	[1, 2, 3].includes(4)     // false
	[1, 2, NaN].includes(NaN) // true
	/*
	该方法的第二个参数表示搜索的起始位置，默认为0。如果第二个参数为负数，则表示倒数的位置，
	如果这时它大于数组长度(比如第二个参数为-4，但数组长度为3)，则会重置为从0开始。
	*/
	```
2. indexOf方法有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于-1，表达起来不够直观。二是，它内部使用严格相等运算符(===)进行判断，这会导致对NaN的误判。<br>
	```javascript
	[NaN].indexOf(NaN) // -1
	```
3. 下面代码用来检查当前环境是否支持该方法，如果不支持，部署一个简易的替代版本。
	```javascript
	const contains = (() =>
	  Array.prototype.includes ? (arr, value) => arr.includes(value) : (arr, value) => arr.some(el => el === value)
	)();
	contains(['foo', 'bar'], 'baz'); // => false
	```
4. 另外，Map和Set数据结构有一个has方法，需要注意与includes区分。
	1) `Map`结构的has方法，是用来查找`键名`的，比如Map.prototype.has(key)、WeakMap.prototype.has(key)、Reflect.has(target, propertyKey)。
	2) `Set`结构的has方法，是用来查找`值`的，比如Set.prototype.has(value)、WeakSet.prototype.has(value)。
### 数组实例的flat()，flatMap()
+ 数组的成员有时还是数组，Array.prototype.flat()用于将嵌套的数组“拉平”，变成一维的数组。该方法返回一个新数组，对原数据没有影响。
+ flat()默认只会“拉平”一层，如果想要“拉平”多层的嵌套数组，可以将flat()方法的参数写成一个整数，表示想要拉平的层数，默认为1。
	```javascript
	[1, 2, [3, [4, 5]]].flat()  // [1, 2, 3, [4, 5]]
	[1, 2, [3, [4, 5]]].flat(2) // [1, 2, 3, 4, 5]
	```
+ 如果不管有多少层嵌套，都要转成一维数组，可以用Infinity关键字作为参数。
	```javascript
	[1, [2, [3]]].flat(Infinity) // [1, 2, 3]
	```
+ 如果原数组有空位，flat()方法会跳过空位。
	```javascript
	[1, 2, , 4, 5].flat()  // [1, 2, 4, 5]
	```
+ flatMap()方法对原数组的每个成员执行一个函数(相当于执行Array.prototype.map())，然后对返回值组成的数组执行flat()方法。该方法返回一个新数组，不改变原数组。
	```javascript
	[2, 3, 4].flatMap((x) => [x, x + 2]) // [2, 4, 3, 6, 4, 8]
	```
### 数组的空位
+ 数组的空位指，数组的某一个位置没有任何值。比如，Array构造函数返回的数组都是空位。
	```javascript
	Array(3) // [, , ,]
	```
+ 注意，空位不是undefined，一个位置的值等于undefined，依然是有值的。空位是没有任何值，in运算符可以说明这一点。
	```javascript
	0 in [undefined, undefined, undefined] // true
	0 in [, , ,] // false
	```
1. ES5对空位的处理，已经很不一致了，大多数情况下会忽略空位。
	+ forEach(), filter(), reduce(), every() 和some()都会跳过空位。
	+ map()会跳过空位，但会保留这个值
	+ join()和toString()会将空位视为undefined，而undefined和null会被处理成空字符串。
2. ES6则是明确将空位转为undefined。
	+ Array.from方法会将数组的空位，转为undefined，也就是说，这个方法不会忽略空位。
	+ 扩展运算符(...)也会将空位转为undefined。
	+ copyWithin()会连空位一起拷贝。
	+ fill()会将空位视为正常的数组位置。
	+ for...of循环也会遍历空位。

# 对象扩展
### 属性名表达式
+ JavaScript 定义对象的属性，有两种方法。
	```javascript
	// 方法一
	obj.foo = true;
	// 方法二
	obj['a' + 'bc'] = 123;
	```
+ 如果使用字面量方式定义对象(使用大括号)，在ES5中只能使用方法一(标识符)定义属性。
+ ES6允许字面量定义对象时，用方法二(表达式)作为对象的属性名，即把表达式放在方括号内。
+ 注意，属性名表达式与简洁表示法，不能同时使用，会报错。
	```javascript
	// 报错
	const foo = 'bar';
	const bar = 'abc';
	const baz = { [foo] };
	// 正确
	const foo = 'bar';
	const baz = { [foo]: 'abc'};
	```
### 方法的name属性
+ 函数的name属性，返回函数名。对象方法也是函数，因此也有name属性。
+ 如果对象的方法使用了取值函数(getter)和存值函数(setter)，则name属性不是在该方法上面，而是该方法的属性的描述对象的get和set属性上面，返回值是方法名前加上get和set。
+ 有两种特殊情况：bind方法创造的函数，name属性返回bound加上原函数的名字；Function构造函数创造的函数，name属性返回anonymous。
+ 如果对象的方法是一个Symbol值，那么name属性返回的是这个Symbol值的描述。
### Object.is()
+ ES5比较两个值是否相等，只有两个运算符：相等运算符(==)和严格相等运算符(===)。它们都有缺点，前者会自动转换数据类型，后者的NaN不等于自身，以及+0等于-0
	```javascript
	+0 === -0 //true
	NaN === NaN // false
	Object.is(+0, -0) // false
	Object.is(NaN, NaN) // true
	```
+ ES5可以通过下面的代码，部署Object.is
	```javascript
	Object.defineProperty(Object, 'is', {
	  value: function(x, y) {
	    if (x === y) {
	      // 针对+0 不等于 -0的情况
	      return x !== 0 || 1 / x === 1 / y;
	    }
	    // 针对NaN的情况
	    return x !== x && y !== y;
	  },
	  configurable: true,
	  enumerable: false,
	  writable: true
	});
	```
### Object.assign()
1. 基本用法
	+ Object.assign方法用于对象的合并，将源对象(source)的所有可枚举属性，复制到目标对象(target)。
		```javascript
		Object.assign(target, source1, source2);
		/*
		Object.assign方法的第一个参数是目标对象，后面的参数都是源对象
		*/
		```
	+ 由于undefined和null无法转成对象，所以如果它们作为首参数，就会报错。不在首参数，就会跳过，不报错。
		```javascript
		Object.assign(undefined) // 报错
		Object.assign(null) // 报错
		```
	+ Object.assign拷贝的属性是有限制的，只拷贝源对象的自身属性(不拷贝继承属性)，也不拷贝不可枚举的属性(enumerable: false)。
2. 浅拷贝
	+ Object.assign方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。
		```javascript
		const obj1 = {a: {b: 1}};
		const obj2 = Object.assign({}, obj1);
		obj1.a.b = 2;
		obj2.a.b // 2
		/*
		源对象obj1的a属性的值是一个对象，Object.assign拷贝得到的是这个对象的引用。这个对象的任何变化，都会反映到目标对象上面。
		*/
		```
3. 同名属性的替换
	+ 对于这种嵌套的对象，一旦遇到同名属性，Object.assign的处理方法是替换，而不是添加
	+ 一些函数库提供Object.assign的定制版本(比如Lodash的_.defaultsDeep方法)，可以得到深拷贝的合并。
4. 数组的处理
	+ Object.assign可以用来处理数组，但是会把数组视为对象。
5. 取值函数的处理
	+ Object.assign只能进行值的复制，如果要复制的值是一个取值函数，那么将求值后再复制。
		```javascript
		const source = {
		  get foo() { return 1 }
		};
		const target = {};
		Object.assign(target, source)
		// { foo: 1 }
		```
### 属性的可枚举性和遍历
+ 对象的每个属性都有一个描述对象(Descriptor)，用来控制该属性的行为。Object.getOwnPropertyDescriptor方法可以获取该属性的描述对象。
	```javascript
	let obj = { foo: 123 };
	Object.getOwnPropertyDescriptor(obj, 'foo')
	```
1. 目前，有四个操作会忽略enumerable为false的属性。
	1) for...in循环：只遍历对象自身的和继承的可枚举的属性。
	2) Object.keys(obj)：返回对象自身的所有可枚举的属性的键名。
	3) JSON.stringify()：只串行化对象自身的可枚举的属性。(可配合JSON.parse实现深拷贝)
	4) Object.assign()：忽略enumerable为false的属性，只拷贝对象自身的可枚举的属性。(浅拷贝)
2. 属性的遍历
	1) for...in循环遍历对象自身的和继承的可枚举属性(不含Symbol属性)
	2) Object.keys(obj)返回一个数组，包括对象自身的(不含继承的)所有可枚举属性(不含Symbol属性)的键名
	3) Object.getOwnPropertyNames(obj)返回一个数组，包含对象自身的所有属性(不含Symbol属性，但是包括不可枚举属性)的键名
	4) Object.getOwnPropertySymbols(obj)返回一个数组，包含对象自身的所有Symbol属性的键名
	5) Reflect.ownKeys(obj)返回一个数组，包含对象自身的所有键名，不管键名是Symbol或字符串，也不管是否可枚举。
+ 以上的5种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。
	1) 首先遍历所有数值键，按照数值升序排列。
	2) 其次遍历所有字符串键，按照加入时间升序排列。
	3) 最后遍历所有Symbol键，按照加入时间升序排列。
	```javascript
	Reflect.ownKeys({ [Symbol()]:0, b:0, 10:0, 2:0, a:0 })
	// ['2', '10', 'b', 'a', Symbol()]
	```
### Object.getOwnPropertyDescriptors()
+ ES2017引入了Object.getOwnPropertyDescriptors方法，返回指定对象所有自身属性(非继承属性)的描述对象。
+ 该方法的实现非常容易。
	```javascript
	function getOwnPropertyDescriptors(obj) {
	  const result = {};
	  for (let key of Reflect.ownKeys(obj)) {
	    result[key] = Object.getOwnPropertyDescriptor(obj, key);
	  }
	  return result;
	}
	```
+ 该方法的引入目的，主要是为了解决Object.assign()无法正确拷贝get属性和set属性的问题。这是因为Object.assign方法总是拷贝一个属性的值，而不会拷贝它背后的赋值方法或取值方法。这时，Object.getOwnPropertyDescriptors方法配合Object.defineProperties方法，就可以实现正确拷贝。
	```javascript
	const source = {
	  set foo(value) {
	    console.log(value);
	  }
	};
	const target2 = {};
	const shallowMerge = (target, source) => Object.defineProperties(
	  target,
	  Object.getOwnPropertyDescriptors(source)
	);
	```
+ Object.getOwnPropertyDescriptors方法的另一个用处，是配合Object.create方法，将对象属性克隆到一个新对象。这属于浅拷贝。
	```javascript
	const shallowClone = (obj) => Object.assign(
	  Object.create(Object.getPrototypeOf(obj)),
	  obj
	);
	// 或者
	const shallowClone = (obj) => Object.create(
	  Object.getPrototypeOf(obj),
	  Object.getOwnPropertyDescriptors(obj)
	);
	```
+ 另外，Object.getOwnPropertyDescriptors方法可以实现一个对象继承另一个对象。以前，继承另一个对象，常常写成下面这样。
	```javascript
	const obj = {
	  __proto__: prot,
	  foo: 123,
	};
	```
+ ES6规定__proto__只有浏览器要部署，其他环境不用部署。如果去除__proto__，上面代码就要改成下面这样。
	```javascript
	const obj = Object.assign(
	  Object.create(prot),
	  {
	    foo: 123,
	  }
	);
	```
+ 有了Object.getOwnPropertyDescriptors，我们就有了另一种写法。
	```javascript
	const obj = Object.create(
	  prot,
	  Object.getOwnPropertyDescriptors({
	    foo: 123,
	  })
	);
	```
### __proto__属性，Object.setPrototypeOf()，Object.getPrototypeOf()
1. \_\_proto\_\_
	<br>&ensp;&ensp;&ensp;&ensp;__proto__属性(前后各两个下划线)，用来读取或设置当前对象的prototype对象。目前，所有浏览器(包括 IE11)都部署了这个属性。
2. Object.setPrototypeOf()
	+ Object.setPrototypeOf方法的作用与__proto__相同，用来设置一个对象的prototype对象，返回参数对象本身。它是 ES6 正式推荐的设置原型对象的方法。
		```javascript
		Object.setPrototypeOf(object, prototype)
		// 等同于
		function (obj, proto) {
		  obj.__proto__ = proto;
		  return obj;
		}
		```
	+ 如果第一个参数不是对象，会自动转为对象。但是由于返回的还是第一个参数，所以这个操作不会产生任何效果。
		```javascript
		Object.setPrototypeOf(1, {}) === 1 // true
		Object.setPrototypeOf('foo', {}) === 'foo' // true
		Object.setPrototypeOf(true, {}) === true // true
		```
	+ 由于undefined和null无法转为对象，所以如果第一个参数是undefined或null，就会报错
3) Object.getPrototypeOf()
	+ 该方法与Object.setPrototypeOf方法配套，用于读取一个对象的原型对象。
### super关键字
+ ES6引入关键字super，指向当前对象的原型对象。
+ super关键字表示原型对象时，只能用在对象的方法之中，用在其他地方都会报错
+ JavaScript引擎内部，super.foo等同于Object.getPrototypeOf(this).foo(属性)或Object.getPrototypeOf(this).foo.call(this)(方法)。
	```javascript
	const proto = {
	  x: 'hello',
	  foo() {
	    console.log(this.x);
	  },
	};
	const obj = {
	  x: 'world',
	  foo() {
	    super.foo();
	  }
	}
	Object.setPrototypeOf(obj, proto);
	obj.foo() // "world"
	/*
	super.foo指向原型对象proto的foo方法，但是绑定的this却还是当前对象obj，因此输出的就是world
	*/
	```
### Object.keys()，Object.values()，Object.entries()
1. Object.keys()
	+ ES5引入了Object.keys方法，返回一个数组，成员是参数对象自身的(不含继承的)所有可遍历(enumerable)属性的键名。
	+ ES2017引入了跟Object.keys配套的Object.values和Object.entries，作为遍历一个对象的补充手段，供for...of循环使用。
2. Object.values()
	+ 返回一个数组，成员是参数对象自身的(不含继承的)所有可遍历(不含Symbol属性)属性的键值
	+ 如果参数不是对象，Object.values会先将其转为对象。由于数值和布尔值的包装对象，都不会为实例添加非继承的属性。所以，Object.values会返回空数组
3. Object.entries()
	+ 返回一个数组，成员是参数对象自身的(不含继承的)所有可遍历(不含Symbol属性)属性的键值对数组
	+ Object.entries方法的另一个用处是，将对象转为真正的Map结构。
	+ 实现Object.entries方法
		```javascript
		function* entries(obj) {
		  for (let key of Object.keys(obj)) {
		    yield [key, obj[key]];
		  }
		}
		// 非Generator
		function entries(obj) {
		  let arr = [];
		  for (let key of Object.keys(obj)) {
		    arr.push([key, obj[key]]);
		  }
		  return arr;
		}
		```
### 对象的扩展运算符
+ ES2018将扩展运算符引入了对象
+ 解构赋值的拷贝是浅拷贝，即如果一个键的值是复合类型的值(数组、对象、函数)、那么解构赋值拷贝的是这个值的引用，而不是这个值的副本。
+ 扩展运算符的解构赋值，不能复制继承自原型对象的属性。
+ 如果想完整克隆一个对象，还拷贝对象原型的属性，可以采用下面的写法。
	```javascript
	const clone = {
	  __proto__: Object.getPrototypeOf(obj),
	  ...obj
	};
	```
+ 扩展运算符可以用于合并两个对象。
	```javascript
	let ab = { ...a, ...b };
	// 等同于
	let ab = Object.assign({}, a, b);
	```
	_____________
# Symbol
### 概述
+ ES6引入了一种新的原始数据类型Symbol，表示独一无二的值。它是JavaScript语言的第七种数据类型。引入的原因：防止属性名的冲突
+ Symbol函数前不能使用new命令，否则会报错。这是因为生成的Symbol是一个原始类型的值，不是对象。
+ Symbol函数的参数只是表示对当前Symbol值的描述，因此相同参数的Symbol函数的返回值是不相等的。
	```javascript
	let s1 = Symbol('foo');
	let s2 = Symbol('foo');
	s1 === s2 // false
	```
+ Symbol值不能与其他类型的值进行运算，会报错。但是，Symbol值可以显式转为字符串、布尔值。(ps不能转为数值)
	```javascript
	let sym = Symbol('My symbol');
	String(sym) // 'Symbol(My symbol)'
	Boolean(sym) // true
	```
### 作为属性名的Symbol
1. 由于每一个Symbol值都是不相等的，这意味着Symbol值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。这对于一个对象由多个模块构成的情况非常有用，能防止某一个键被不小心改写或覆盖。
	```javascript
	let mySymbol = Symbol();
	let a = {};
	a[mySymbol] = 'Hello!';
	```
2. Symbol值作为对象属性名时，不能用点运算符，要用[],同理，在对象的内部，使用Symbol值定义属性时，Symbol值必须放在方括号之中
	```javascript
	let s = Symbol();
	let obj = {
	  [s]: function (arg) { ... }
	};
	obj[s](123);
	```
3. 常量使用Symbol值最大的好处，就是其他任何值都不可能有相同的值了，因此可以保证上面的switch语句会按设计的方式工作。
4. Symbol值作为属性名时，该属性还是公开属性，不是私有属性。
5. `实例：消除魔术字符串`
	<br>魔术字符串指的是，在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值。风格良好的代码，应该尽量消除魔术字符串，改由含义清晰的变量代替。常用的消除魔术字符串的方法，就是把它写成一个变量。
	```javascript
	const shapeType = {
	  triangle: 'Triangle'
	};
	function getArea(shape, options) {
	  let area = 0;
	  switch (shape) {
	    case shapeType.triangle:
	    area = .5 + options.width + options.height;
	    break;
	  }
	  return area;
	}
	getArea(shapeType.triangle, { width: 100, height: 100 });
 	/*
	如果仔细分析，可以发现shapeType.triangle等于哪个值并不重要，只要确保不会跟其他shapeType属性的值冲突即可。因此，这里就很适合改用Symbol值。
 	*/
 	const shapeType = {
	  triangle: Symbol()
	};
	```
### 属性名的遍历
+ Symbol作为属性名，该属性不会出现在for...in、for...of循环中，也不会被Object.keys()、Object.getOwnPropertyNames()、JSON.stringify()返回。但是，它也不是私有属性。
+ Object.getOwnPropertySymbols():返回一个数组，成员是当前对象的所有用作属性名的Symbol值
+ Reflect.ownKeys():可以返回所有类型的键名，包括常规键名和Symbol键名
+ 由于以Symbol值作为名称的属性，不会被常规方法遍历得到。我们可以利用这个特性，为对象定义一些非私有的、但又希望只用于内部的方法。
### Symbol.for()，Symbol.keyFor()
+ Symbol.for():它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的Symbol值。如果有，就返回这个Symbol值，否则就新建并返回一个以该字符串为名称的Symbol值。
	```javascript
	let s1 = Symbol.for('foo');
	let s2 = Symbol.for('foo');
	s1 === s2 // true
	/*
	s1和s2都是Symbol值，但是它们都是同样参数的Symbol.for方法生成的，所以实际上是同一个值
	*/
	```
+ Symbol.for()与Symbol()这两种写法，都会生成新的Symbol。它们的区别是，前者会被登记在全局环境中供搜索，后者不会。Symbol.for()不会每次调用就返回一个新的Symbol类型的值，而是会先检查给定的key是否已经存在，如果不存在才会新建一个值。
+ Symbol.keyFor方法返回一个已登记的Symbol类型值的key。
	```javascript
	let s1 = Symbol.for("foo");
	Symbol.keyFor(s1) // "foo"
	let s2 = Symbol("foo");
	Symbol.keyFor(s2) // undefined
	```
+ Symbol.for为Symbol值登记的名字，是全局环境的，可以在不同的iframe或 service worker中取到同一个值。
### 内置的Symbol值
1) Symbol.hasInstance
	+ 对象的Symbol.hasInstance属性，指向一个内部方法。当其他对象使用instanceof运算符，判断是否为该对象的实例时，会调用这个方法。比如，foo instanceof Foo在语言内部，实际调用的是Foo[Symbol.hasInstance](foo)。
2) Symbol.isConcatSpreadable
	+ 对象的Symbol.isConcatSpreadable属性等于一个布尔值，表示该对象用于Array.prototype.concat()时，是否可以展开。
	+ 数组的默认行为是可以展开，Symbol.isConcatSpreadable默认等于undefined。该属性等于true时，也有展开的效果
		```javascript
		let arr1 = ['c', 'd'];
		['a', 'b'].concat(arr1, 'e') // ['a', 'b', 'c', 'd', 'e']
		arr1[Symbol.isConcatSpreadable] // undefined
		let arr2 = ['c', 'd'];
		arr2[Symbol.isConcatSpreadable] = false;
		['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']
		```
	+ 类似数组的对象正好相反，默认不展开。它的Symbol.isConcatSpreadable属性设为true，才可以展开。
		```javascript
		let obj = {length: 2, 0: 'c', 1: 'd'};
		['a', 'b'].concat(obj, 'e') // ['a', 'b', obj, 'e']
		obj[Symbol.isConcatSpreadable] = true;
		['a', 'b'].concat(obj, 'e') // ['a', 'b', 'c', 'd', 'e']
		```
3) Symbol.species
	+ 对象的Symbol.species属性，指向一个构造函数。创建衍生对象时，会使用该属性。
		```javascript
		class MyArray extends Array {
		  static get [Symbol.species]() { return Array; }
		}
		const a = new MyArray();
		const b = a.map(x => x);
		b instanceof MyArray // false
		b instanceof Array // true
		```
	+ 总之，Symbol.species的作用在于，实例对象在运行过程中，需要再次调用自身的构造函数时，会调用该属性指定的构造函数。它主要的用途是，有些类库是在基类的基础上修改的，那么子类使用继承的方法时，作者可能希望返回基类的实例，而不是子类的实例。
4) Symbol.match
	+ 对象的Symbol.match属性，指向一个函数。当执行str.match(myObject)时，如果该属性存在，会调用它，返回该方法的返回值。
		```javascript
		String.prototype.match(regexp)
		// 等同于
		regexp[Symbol.match](this)
		class MyMatcher {
		  [Symbol.match](string) {
		    return 'hello world'.indexOf(string);
		  }
		}
		'e'.match(new MyMatcher()) // 1
		```
5) Symbol.replace
	+ 对象的Symbol.replace属性，指向一个方法，当该对象被String.prototype.replace方法调用时，会返回该方法的返回值。
		```javascript
		String.prototype.replace(searchValue, replaceValue)
		// 等同于
		searchValue[Symbol.replace](this, replaceValue)
		```
6) Symbol.search
	+ 对象的Symbol.search属性，指向一个方法，当该对象被String.prototype.search方法调用时，会返回该方法的返回值。
		```javascript
		String.prototype.search(regexp)
		// 等同于
		regexp[Symbol.search](this)
		class MySearch {
		  constructor(value) {
		    this.value = value;
		  }
		  [Symbol.search](string) {
		    return string.indexOf(this.value);
		  }
		}
		'foobar'.search(new MySearch('foo')) // 0
		```
7) Symbol.split
	+ 对象的Symbol.split属性，指向一个方法，当该对象被String.prototype.split方法调用时，会返回该方法的返回值。
		```javascript
		String.prototype.split(separator, limit)
		// 等同于
		separator[Symbol.split](this, limit)
		```
8) Symbol.iterator
	+ 对象的Symbol.iterator属性，指向该对象的默认遍历器方法。
9) Symbol.toPrimitive
	+ 对象的Symbol.toPrimitive属性，指向一个方法。该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。
	+ Symbol.toPrimitive被调用时，会接受一个字符串参数，表示当前运算的模式，一共有三种模式
		1) Number：该场合需要转成数值
		2) String：该场合需要转成字符串
		3) Default：该场合可以转成数值，也可以转成字符串
10) Symbol.toStringTag
	+ 对象的Symbol.toStringTag属性，指向一个方法。在该对象上面调用Object.prototype.toString方法时，如果这个属性存在，它的返回值会出现在toString方法返回的字符串之中，表示对象的类型。也就是说，这个属性可以用来定制[object Object]或[object Array]中object后面的那个字符串。
11) Symbol.unscopables
	+ 对象的Symbol.unscopables属性，指向一个对象。该对象指定了使用with关键字时，哪些属性会被with环境排除。
____________________
# Proxy
### 概述
+ Proxy用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。
+ Proxy可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。
+ ES6原生提供Proxy构造函数，用来生成Proxy实例。
	```javascript
	var proxy = new Proxy(target, handler);
	/*
  new Proxy()表示生成一个Proxy实例，target参数表示所要拦截的目标对象，handler参数也是一个对象，用来定制拦截行为。如果handler没有设置任何拦截，那就等同于直接通向原对象。
	*/
	```
+ Proxy 实例也可以作为其他对象的原型对象。
	```javascript
	var proxy = new Proxy({}, {
	  get: function(target, property) {
	    return 35;
	  }
	});
	let obj = Object.create(proxy);
	obj.time // 35
	/*
	proxy对象是obj对象的原型，obj对象本身并没有time属性，所以根据原型链，会在proxy对象上读取该属性，导致被拦截。
	*/
	```
### 实例方法
1. get()
	+ get(target, propKey, receiver)：拦截对象属性的读取，比如proxy.foo和proxy['foo']
	+ 如果一个属性不可配置（configurable）且不可写（writable），则 Proxy 不能修改该属性，否则通过 Proxy 对象访问该属性会报错。
2. set()
	+ set(target, propKey, value, receiver)：拦截对象属性的设置，比如` proxy.foo = v `或` proxy['foo'] = v `，返回一个布尔值。
	+ 我们会在对象上面设置内部属性，属性名的第一个字符使用下划线开头，表示这些属性不应该被外部使用。结合get和set方法，就可以做到防止这些内部属性被外部读写
		```javascript
		const handler = {
		  get (target, key) {
		    invariant(key, 'get');
		    return target[key];
		  },
		  set (target, key, value) {
		    invariant(key, 'set');
		    target[key] = value;
		    return true;
		  }
		};
		function invariant (key, action) {
		  if (key[0] === '_') {
		    throw new Error(`Invalid attempt to ${action} private "${key}" property`);
		  }
		}
		const target = {};
		const proxy = new Proxy(target, handler);
		proxy._prop
		// Error: Invalid attempt to get private "_prop" property
		proxy._prop = 'c'
		// Error: Invalid attempt to set private "_prop" property
		```
	+ 如果目标对象自身的某个属性，不可写且不可配置，那么set方法将不起作用。
3. has()
	+ has(target, propKey)：拦截propKey in proxy的操作，返回一个布尔值。
	+ has方法用来拦截HasProperty操作，即判断对象是否具有某个属性时，这个方法会生效。典型的操作就是in运算符。
	+ 某个属性不可配置（或者目标对象不可扩展），则has方法就不得“隐藏”（即返回false）目标对象的该属性。
	+ has方法拦截的是HasProperty操作，而不是HasOwnProperty操作，即has方法不判断一个属性是对象自身的属性，还是继承的属性。
	+ 虽然for...in循环也用到了in运算符，但是has拦截对for...in循环不生效
4. deleteProperty()
	+ deleteProperty(target, propKey)：拦截delete proxy[propKey]的操作，返回一个布尔值。
	+ 目标对象自身的不可配置（configurable）的属性，不能被deleteProperty方法删除，否则报错。
5. ownKeys()
	+ ownKeys(target)：拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。
	+ ownKeys方法返回的数组成员，只能是字符串或Symbol值。如果有其他类型的值，或者返回的根本不是数组，就会报错。
6) getOwnPropertyDescriptor()
	+ getOwnPropertyDescriptor(target, propKey)：拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
7. defineProperty()
	+ defineProperty(target, propKey, propDesc)：拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。
	+ 如果目标对象不可扩展（extensible），则defineProperty不能增加目标对象上不存在的属性，否则会报错。另外，如果目标对象的某个属性不可写（writable）或不可配置（configurable），则defineProperty方法不得改变这两个设置
8. preventExtensions()
	+ preventExtensions(target)：拦截Object.preventExtensions(proxy)，返回一个布尔值。
9. getPrototypeOf()
	+ getPrototypeOf(target)：拦截Object.getPrototypeOf(proxy)，返回一个对象。
	+ getPrototypeOf方法主要用来拦截获取对象原型
		1) Object.prototype.\_\_proto\_\_
		2) Object.prototype.isPrototypeOf()
		3) Object.getPrototypeOf()
		4) Reflect.getPrototypeOf()
		5) instanceof
	+ getPrototypeOf方法的返回值必须是对象或者null，否则报错。另外，如果目标对象不可扩展（extensible），getPrototypeOf方法必须返回目标对象的原型对象
10. isExtensible()
	+ isExtensible(target)：拦截Object.isExtensible(proxy)，返回一个布尔值。
11. setPrototypeOf()
	+ setPrototypeOf(target, proto)：拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
12. apply()
	+ apply(target, object, args)：拦截Proxy实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
13. construct()
	+ construct(target, args)：拦截Proxy实例作为构造函数调用的操作，比如new proxy(...args)。
		```javascript
		var handler = {
		  construct (target, args, newTarget) {
		    return new target(...args);
		  }
		};
		```
### Proxy.revocable()
+ Proxy.revocable的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。
### this问题
+ 虽然Proxy可以代理针对目标对象的访问，但它不是目标对象的透明代理，即不做任何拦截的情况下，也无法保证与目标对象的行为一致。主要原因就是在Proxy代理的情况下，目标对象内部的this关键字会指向Proxy代理。
	```javascript
	const target = {
	  m: function () {
	    console.log(this === proxy);
	  }
	};
	const handler = {};
	const proxy = new Proxy(target, handler);
	target.m() // false
	proxy.m()  // true
	```
____________
# Reflect
### 概述
+ Reflect对象与Proxy对象一样，也是ES6为了操作对象而提供的新API。Reflect对象的设计目的有这样几个。
	1) 将Object对象的一些明显属于语言内部的方法（比如Object.defineProperty），放到Reflect对象上
	2) 修改某些Object方法的返回结果，让其变得更合理。
	3) 让Object操作都变成函数行为。某些Object操作是命令式，比如name in obj和delete obj[name]，而Reflect.has(obj, name)和Reflect.deleteProperty(obj, name)让它们变成了函数行为。
	4) Reflect对象的方法与Proxy对象的方法一一对应，只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法。
### 静态方法
1) Reflect.apply(target, thisArg, args)
2) Reflect.construct(target, args)
3) Reflect.get(target, name, receiver)
4) Reflect.set(target, name, value, receiver)
5) Reflect.defineProperty(target, name, desc)
6) Reflect.deleteProperty(target, name)
7) Reflect.has(target, name)
8) Reflect.ownKeys(target)
9) Reflect.isExtensible(target)
10) Reflect.preventExtensions(target)
11) Reflect.getOwnPropertyDescriptor(target, name)
12) Reflect.getPrototypeOf(target)
13) Reflect.setPrototypeOf(target, prototype)

_______________________________
# Iterator和for...of循环
### Iterator
1. Iterator的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是ES6创造了一种新的遍历命令for...of循环，Iterator接口主要供for...of消费
2. Iterator的遍历过程是这样的。
	1) 创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。
	2) 第一次调用指针对象的next方法，可以将指针指向数据结构的第一个成员。
	3) 第二次调用指针对象的next方法，指针就指向数据结构的第二个成员。
	4) 不断调用指针对象的next方法，直到它指向数据结构的结束位置。
3. 每一次调用next方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含value和done两个属性的对象。其中，value属性是当前成员的值，done属性是一个布尔值，表示遍历是否结束。
4. ES6规定，默认的Iterator接口部署在数据结构的Symbol.iterator属性，或者说，一个数据结构只要具有Symbol.iterator属性，就可以认为是“可遍历的”(iterable)
5. 原生具备Iterator接口的数据结构如下:<br>
	Array、Map、Set、String、TypedArray、函数的arguments对象、NodeList对象
6. 类数组部署iterator接口
	```javascript
	let iterable = {
	  0: 'a',
	  1: 'b',
	  2: 'c',
	  length: 3,
	  [Symbol.iterator]: Array.prototype[Symbol.iterator]
	};
	for (let item of iterable) {
	  console.log(item); // 'a', 'b', 'c'
	}
	```
### for...of循环
1. 数组
	+ 数组原生具备iterator接口(即默认部署了Symbol.iterator属性)，for...of循环本质上就是调用这个接口产生的遍历器
	+ JavaScript原有的for...in循环，只能获得对象的键名，不能直接获取键值。ES6 提供for...of循环，允许遍历获得键值。for...of循环调用遍历器接口，数组的遍历器接口只返回具有数字索引的属性。
		```javascript
		let arr = [3, 5, 7];
		arr.foo = 'hello';
		for (let i in arr) {
		  console.log(i); // "0", "1", "2", "foo"
		}
		for (let i of arr) {
		  console.log(i); //  "3", "5", "7"
		}
		```
2. Set和Map结构
	+ 值得注意的地方有两个，首先，遍历的顺序是按照各个成员被添加进数据结构的顺序。其次，Set结构遍历时，返回的是一个值，而Map结构遍历时，返回的是一个数组，该数组的两个成员分别为当前Map成员的键名和键值。
3. 对象
	+ 对于普通的对象，for...of结构不能直接使用会报错，必须部署了Iterator接口后才能使用。但是，这样情况下，for...in循环依然可以用来遍历键名。
	+ 对象(Object)之所以没有默认部署Iterator接口，是因为对象的哪个属性先遍历，哪个属性后遍历是不确定的，需要开发者手动指定。本质上，遍历器是一种线性处理，对于任何非线性的数据结构，部署遍历器接口，就等于部署一种线性转换。
4. 与其他遍历语法的比较
	1) for
		```javascript
		for (var index = 0; index < myArray.length; index++) {
		  console.log(myArray[index]);
		}
		/*这种写法比较麻烦，因此数组提供内置的forEach方法。*/
		```
	2) forEach
		```javascript
		myArray.forEach(function (value) {
		  console.log(value);
		});
		/*这种写法的问题在于，无法中途跳出forEach循环，break命令或return命令都不能奏效。*/
		```
	3) for...in
		```javascript
		for (var index in myArray) {
		  console.log(myArray[index]);
		}
		```
		+ for...in循环有几个缺点
			+ 数组的键名是数字，但是for...in循环是以字符串作为键名“0”、“1”、“2”等等。
			+ for...in循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。
			+ 某些情况下，for...in循环会以任意顺序遍历键名。
		+ 总之，for...in循环主要是为遍历对象而设计的，不适用于遍历数组
	4) for...of
		<br>for...of循环相比上面几种做法，有一些显著的优点。
		+ 有着同for...in一样的简洁语法，但是没有for...in那些缺点。
		+ 不同于forEach方法，它可以与break、continue和return配合使用。
		+ 提供了遍历所有数据结构的统一操作接口。

______________
# Set && Map数据结构
### Set
+ ES6提供了新的数据结构Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。
1. 基本用法
	+ Set函数可以接受一个数组(或者具有iterable接口的其他数据结构)作为参数，用来初始化。
		```javascript
		const set = new Set([1, 2, 3, 4, 4]);
		[...set]
		// [1, 2, 3, 4]
		```
	+ 一种去除数组重复成员的方法
		```javascript
		[...new Set(array)]
		```
	+ 向Set加入值的时候，不会发生类型转换，所以5和"5"是两个不同的值。Set内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符(===)，主要的区别是NaN等于自身，而精确相等运算符认为NaN不等于自身。
2. Set实例属性与方法
	1) 属性
		+ constructor：构造函数，默认就是Set函数。
		+ size：返回Set实例的成员总数。
	2) 方法
		+ add(value)：添加某个值，返回Set结构本身
		+ delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
		+ has(value)：返回一个布尔值，表示该值是否为Set的成员。
		+ clear()：清除所有成员，没有返回值。
	3) Array.from方法可以将Set结构转为数组。
		```javascript
		let set = new Set([1,2,3])
		let arry = Array.from(set)    // 转化为数组
		```
	4) 去除数组重复成员的另一种方法。
		```javascript
		function dedupe(array) {
		  return Array.from(new Set(array));
		}
		dedupe([1, 1, 2, 3]) // [1, 2, 3]
		```
3. 遍历操作
 	<br>Set 结构的实例有四个遍历方法，可以用于遍历成员
	+ keys()：返回键名的遍历器
	+ values()：返回键值的遍历器
	+ entries()：返回键值对的遍历器
	+ forEach()：使用回调函数遍历每个成员

	<br>Set的遍历顺序就是插入顺序。这个特性有时非常有用，比如使用Set保存一个回调函数列表，调用时就能保证按照添加顺序调用
4. 遍历的应用
	```javascript
	let a = new Set([1, 2, 3]);
	let b = new Set([4, 3, 2]);
	// 并集
	let union = new Set([...a, ...b]);    // Set {1, 2, 3, 4}
	// 交集
	let intersect = new Set([...a].filter(x => b.has(x)));   // set {2, 3}
	// 差集
	let difference = new Set([...a].filter(x => !b.has(x)));  // Set {1}
	```
### WeakSet
1. 它与Set有两个区别:
	1) 首先，`WeakSet`的成员只能是`对象`，而不能是其他类型的值。
	2) WeakSet中的对象都是`弱引用`，即垃圾回收机制不考虑WeakSet对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于WeakSet之中。
2. 作为构造函数，WeakSet可以接受一个数组或类似数组的对象作为参数。(实际上，任何具有Iterable接口的对象，都可以作为WeakSet的参数。)该数组的所有成员，都会自动成为WeakSet实例对象的成员。
	```javascript
	const a = [[1, 2], [3, 4]];
	const ws = new WeakSet(a); // WeakSet {[1, 2], [3, 4]}
	/*
	是a数组的成员成为WeakSet的成员，而不是a数组本身。这意味着，数组的成员只能是对象
	*/
	```
3. WeakSet结构有以下三个方法
	+ add(value)：向WeakSet实例添加一个新成员。
	+ delete(value)：清除WeakSet实例的指定成员。
	+ has(value)：返回一个布尔值，表示某个值是否在
4. WeakSet不能遍历，是因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。WeakSet的一个用处，是储存DOM节点，而不用担心这些节点从文档移除时，会引发内存泄漏。
### Map
1. 含义和基本用法
	+ JavaScript的对象(Object)，本质上是键值对的集合(Hash结构)，但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。
	+ 为了解决这个问题，ES6提供了Map数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值(包括对象)都可以当作键。也就是说，Object结构提供了“字符串—值”的对应，Map结构提供了“值—值”的对应，是一种更完善的Hash结构实现。如果你需要“键值对”的数据结构，Map比Object更合适。
	+ 作为构造函数，Map也可以接受一个数组作为参数。该数组的成员是一个个表示键值对的数组。
	+ 事实上，不仅仅是数组，任何具有Iterator接口、且每个成员都是一个双元素的数组的数据结构都可以当作Map构造函数的参数。这就是说，Set和Map都可以用来生成新的Map。
	+ 注意，只有对同一个对象的引用，Map结构才将其视为同一个键。
		```javascript
		const map = new Map();
		map.set(['a'], 555);
		map.get(['a']) // undefined
		```
	+ Map的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。这就解决了同名属性碰撞(clash)的问题，我们扩展别人的库的时候，如果使用对象作为键名，就不用担心自己的属性与原作者的属性同名。
	+ 如果Map的键是一个简单类型的值(数字、字符串、布尔值)，则只要两个值严格相等，Map将其视为一个键，比如0和`-0`就是一个键，布尔值true和字符串true则是两个不同的键。另外，undefined和null也是两个不同的键。虽然NaN不严格相等于自身，但Map将其视为同一个键。
2. 实例的属性和操作方法
	 + size属性：返回Map结构的成员总数。
	 + set(key, value)：设置键名key对应的键值为value，然后返回整个Map结构。
	 + get(key)：读取key对应的键值，如果找不到key，返回undefined。
	 + delete(key)：删除某个键，返回true。如果删除失败，返回false。
	 + has(key)：返回一个布尔值，表示某个键是否在当前Map对象之中。
	 + clear()：清除所有成员，没有返回值。
3. 遍历方法
	<br>Map结构原生提供三个遍历器生成函数和一个遍历方法。需要特别注意的是，Map的遍历顺序就是插入顺序。
	+ keys()：返回键名的遍历器。
	+ values()：返回键值的遍历器。
	+ entries()：返回所有成员的遍历器。
	+ forEach()：遍历 Map的所有成员。
4. Map结构转为数组结构，比较快速的方法是使用`扩展运算符(...)`
### WeakMap
1. 含义
	+ `WeakMap`结构与`Map`结构类似，也是用于生成键值对的集合
	+ WeakMap与Map的区别有两点。
		1) 首先，WeakMap只接受对象作为键名(null除外)，不接受其他类型的值作为键名。
		2) 其次，WeakMap的键名所指向的对象，不计入垃圾回收机制。
	+ WeakMap的设计目的在于，有时我们想在某个对象上面存放一些数据，但是这会形成对于这个对象的引用。
	+ 一个典型应用场景是，在网页的DOM元素上添加数据，就可以使用WeakMap结构。当该DOM元素被清除，其所对应的WeakMap记录就会自动被移除。
	+ 总之，WeakMap的专用场合就是，它的键所对应的对象，可能会在将来消失。WeakMap结构有助于防止内存泄漏。
	+ 注意，WeakMap弱引用的只是键名，而不是键值。键值依然是正常引用。
		```javascript
		const wm = new WeakMap();
		let key = {};
		let obj = {foo: 1};
		wm.set(key, obj);
		obj = null;
		wm.get(key) // Object {foo: 1}
		```
2. WeakMap的语法
	<br>WeakMap与Map在API上的区别主要是两个，一是没有遍历操作(即没有keys()、values()和entries()方法)，也没有size属性。因为没有办法列出所有键名，某个键名是否存在完全不可预测，跟垃圾回收机制是否运行相关。这一刻可以取到键名，下一刻垃圾回收机制突然运行了，这个键名就没了，为了防止出现不确定性，就统一规定不能取到键名。二是无法清空，即不支持clear方法。因此，WeakMap只有四个方法可用：get()、set()、has()、delete()。
3. WeakMap的用处
	1) WeakMap应用的典型场合就是DOM节点作为键名
	2) 部署私有属性

____________________
# Promise对象
### Promise的含义
+ Promise是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件(通常是一个异步操作)的结果。从语法上说，Promise是一个对象，从它可以获取异步操作的消息。Promise对象有以下两个特点。
	1) 对象的状态不受外界影响。
		<br>Promise对象代表一个异步操作，有三种状态：pending(进行中)、fulfilled(已成功)和rejected(已失败)。
	2) 一旦状态改变，就不会再变，任何时候都可以得到这个结果。
		<br>Promise对象的状态改变，只有两种可能：从pending变为fulfilled和从pending变为rejected
+ 有了Promise对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，Promise对象提供统一的接口，使得控制异步操作更加容易。
+ Promise也有一些缺点。
	1) 无法取消Promise，一旦新建它就会立即执行，无法中途取消。
	2) 如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
	3) 当处于pending状态时，无法得知目前进展到哪一个阶段(刚刚开始还是即将完成)。
### 基本用法
```javascript
const promise = new Promise(function(resolve, reject) {
  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```
+ Promise实例生成以后，可以用then方法分别指定resolved状态和rejected状态的回调函数。Promise新建后就会立即执行，then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行
	```javascript
	let promise = new Promise(function(resolve, reject) {
	  console.log('Promise');
	  resolve();
	});
	promise.then(function() {
	  console.log('resolved.');
	});
	console.log('Hi!');
	// Promise
	// Hi!
	// resolved
	```
+ 注意，调用resolve或reject并不会终结Promise的参数函数的执行。
	```javascript
	promise.then(function(value) {
	// success
	}, function(error) {
	// failure
	});
	```
### Promise.prototype.then()
+ then方法返回的是一个新的Promise实例(注意，不是原来那个Promise实例)。因此可以采用链式写法，即then方法后面再调用另一个then方法。
### Promise.prototype.catch()
+ Promise.prototype.catch方法是.then(null, rejection)的别名，用于指定发生错误时的回调函数。
+ Promise对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个catch语句捕获。
+ 跟传统的try/catch代码块不同的是，如果没有使用catch方法指定错误处理的回调函数，Promise对象抛出的错误不会传递到外层代码，即不会有任何反应。
### Promise.prototype.finally()
+ finally方法用于指定不管Promise对象最后状态如何，都会执行的操作。该方法是ES2018引入标准的
	```javascript
	promise
	.then(result => {...})
	.catch(error => {...})
	.finally(() => {...});
	```
+ finally方法的回调函数不接受任何参数，这意味着没有办法知道，前面的Promise状态到底是fulfilled还是rejected。这表明，finally方法里面的操作，应该是与状态无关的，不依赖于Promise的执行结果。
### Promise.all()
<br>Promise.all方法用于将多个Promise实例，包装成一个新的Promise实例。
```javascript
const p = Promise.all([p1, p2, p3]);
/*
p的状态由p1、p2、p3决定，分成两种情况。
a) 只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
b) 只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。
*/
```
### Promise.race()
<br>Promise.race方法同样是将多个Promise实例，包装成一个新的Promise实例。
```javascript
const p = Promise.race([p1, p2, p3]);
/*
只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的Promise实例的返回值，就传递给p的回调函数。
*/
```
+ 应用
	```javascript
	const p = Promise.race([
	  fetch('/resource-that-may-take-a-while'),
	  new Promise(function (resolve, reject) {
	    setTimeout(() => reject(new Error('request timeout')), 5000)
	  })
	]);
	p
	.then(console.log)
	.catch(console.error);
	```
### Promise.resolve()
+ 有时需要将现有对象转为Promise对象，Promise.resolve方法就起到这个作用。
+ 需要注意的是，立即resolve的Promise对象，是在本轮“事件循环”(event loop)的结束时。setTimeout(fn, 0)在下一轮“事件循环”开始时执行
	```javascript
	setTimeout(function () {
	  console.log('three');
	}, 0);
	Promise.resolve().then(function () {
	  console.log('two');
	});
	console.log('one');
	// one
	// two
	// three
	```
### Promise.reject()
+ Promise.reject(reason)方法也会返回一个新Promise实例，该实例的状态为rejected。
+ Promise.reject()方法的参数，会原封不动地作为reject的理由，变成后续方法的参数。
	```javascript
	const thenable = {
	  then(resolve, reject) {
	    reject('出错了');
	  }
	};
	Promise.reject(thenable)
	.catch(e => {
	  console.log(e === thenable)
	})
	// true
	```
______________
# Generator
### Generator函数的语法
1. 简介
	+ Generator函数是ES6提供的一种异步编程解决方案。
	+ Generator函数有多种理解角度。语法上，首先可以把它理解成，Generator函数是一个状态机，封装了多个内部状态。
	+ 执行Generator函数会返回一个遍历器对象，也就是说，Generator函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历Generator函数内部的每一个状态。
	+ 形式上，Generator函数是一个普通函数，但是有两个特征。一是，function关键字与函数名之间有一个星号；二是，函数体内部使用yield表达式，定义不同的内部状态。
	+ Generator函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。
	+ 每次调用遍历器对象的next方法，就会返回一个有着value和done两个属性的对象。value属性表示当前的内部状态的值，是yield表达式后面那个表达式的值；done属性是一个布尔值，表示是否遍历结束
2. yield表达式
	+ yield表达式与return语句既有相似之处，也有区别。相似之处在于，都能返回紧跟在语句后面的那个表达式的值。区别在于每次遇到yield，函数暂停执行，下一次再从该位置继续向后执行，而return语句不具备位置记忆的功能。一个函数里面，只能执行一次(或者说一个)return语句，但是可以执行多次(或者说多个)yield表达式。
	+ yield表达式只能用在Generator函数里面，用在其他地方都会报错。另外，yield表达式如果用在另一个表达式之中，必须放在圆括号里面。yield表达式用作函数参数或放在赋值表达式的右边，可以不加括号。
3. next方法的参数
	+ yield表达式本身没有返回值，或者说总是返回undefined。next方法可以带一个参数，该参数就会被当作上一个yield表达式的返回值。
		```javascript
		function* foo(x) {
		  var y = 2 * (yield (x + 1));
		  var z = yield (y / 3);
		  return (x + y + z);
		}
		var a = foo(5);
		a.next() // Object{value:6, done:false}
		a.next() // Object{value:NaN, done:false}
		a.next() // Object{value:NaN, done:true}
		var b = foo(5);
		b.next() // { value:6, done:false }
		b.next(12) // { value:8, done:false }
		b.next(13) // { value:42, done:true }
		/*
		注意，由于next方法的参数表示上一个yield表达式的返回值，所以在第一次使用next方法时，传递参数是无效的。V8引擎直接忽略第一次使用next方法时的参数，只有从第二次使用next方法开始，参数才是有效的。
		*/
		```
4. for...of循环
	+ for...of循环可以自动遍历Generator函数时生成的Iterator对象，且此时不再需要调用next方法。
	+ 需要注意，一旦next方法的返回对象的done属性为true，for...of循环就会中止，且不包含该返回对象。
		```javascript
		function* foo() {
		  yield 1;
		  yield 2;
		  yield 3;
		  yield 4;
		  yield 5;
		  return 6;
		}
		for (let v of foo()) {
		  console.log(v);  // 1 2 3 4 5
		}
		```
5. Generator.prototype.throw()
6. Generator.prototype.return()
	+ Generator函数返回的遍历器对象，还有一个return方法，可以返回给定的值，并且终结遍历Generator函数。
	+ 如果return方法调用时，不提供参数，则返回值的value属性为undefined。
	+ 如果Generator函数内部有try...finally代码块，那么return方法会推迟到finally代码块执行完再执行
7) next()、throw()、return()的共同点
	+ next()是将yield表达式替换成一个值。
	+ throw()是将yield表达式替换成一个throw语句。
	+ return()是将yield表达式替换成一个return语句。
8) yield*表达式
	+ yield*表达式，用来在一个Generator函数里面执行另一个Generator函数。
	+ 从语法角度看，如果yield表达式后面跟的是一个遍历器对象，需要在yield表达式后面加上星号，表明它返回的是一个遍历器对象。这被称为yield*表达式。
	+ 实际上，任何数据结构只要有Iterator接口，就可以被yield*遍历。
	+ yield命令后面如果不加星号，返回的是整个数组，加了星号就表示返回的是数组的遍历器对象
		```javascript
		let read = (function* () {
		  yield 'hello';
		  yield* 'hello';
		})();
		read.next().value // "hello"
		read.next().value // "h"
		```
	+ yield* 取出嵌套数组的所有成员
		```javascript
		function* iterTree(tree) {
		  if (Array.isArray(tree)) {
		    for(let i=0; i < tree.length; i++) {
		      yield* iterTree(tree[i]);
		    }
		  } else {
		    yield tree;
		  }
		}
		const tree = [ 'a', ['b', 'c'], ['d', 'e'] ];
		for(let x of iterTree(tree)) {
		  console.log(x);
		}
		```
	+ yield* 语句遍历完全二叉树
		```javascript
		function Tree(left, label, right) {
		  this.left = left;
		  this.label = label;
		  this.right = right;
		}
		/*
		前序：中、左、右
		中序: 左、右、中
		后序：左、右、中
	  下面是中序(inorder)遍历函数。由于返回的是一个遍历器，所以要用generator函数。
		函数体内采用递归算法，所以左树和右树要用yield*遍历函数体内采用递归算法，所以左树和右树要用yield*遍历
		*/
		function* inorder(t) {
		  if (t) {
		    yield* inorder(t.left);
		    yield t.label;
		    yield* inorder(t.right);
		  }
		}
		// 下面生成二叉树
		function make(array) {
	 	  // 判断是否为叶节点
		  if (array.length == 1) return new Tree(null, array[0], null);
		  return new Tree(make(array[0]), array[1], make(array[2]));
		}
		let tree = make([[['a'], 'b', ['c']], 'd', [['e'], 'f', ['g']]]);
		// 遍历二叉树
		var result = [];
		for (let node of inorder(tree)) {
		  result.push(node);
		}
		cosole.log(result)  // ['a', 'b', 'c', 'd', 'e', 'f', 'g']
		```
9. Generator函数的this
	+ Generator函数总是返回一个遍历器，ES6规定这个遍历器是Generator函数的实例，也继承了Generator函数的prototype对象上的方法。
		```javascript
		function* g() {}
		g.prototype.hello = function () {
		  return 'hi!';
		};
		let obj = g();
		obj instanceof g // true
		obj.hello() // 'hi!'
		```
	+ 如果把g当作普通的构造函数，并不会生效，因为g返回的总是遍历器对象，而不是this对象。可通过如下改造：
		```javascript
		function* F() {
		  this.a = 1;
		  yield this.b = 2;
		}
		var f = F.call(F.prototype);
		f.next();  // Object {value: 2, done: false}
		f.next();  // Object {value: undefined, done: true}
		f.a // 1
		f.b // 2
		```
	+ Generator函数不能跟new命令一起用，会报错。可通过如下改造：
		```javascript
		function* gen() {
		  this.a = 1;
		  yield this.b = 2;
		}
		function F(){
		  return gen.call(gen.prototype)
		}
		var f = new F()
		f.next();  // Object {value: 2, done: false}
		f.next();  // Object {value: undefined, done: true}
		f.a // 1
		f.b // 2
		```
10. Generator与状态机
	```javascript
	var clock = function* () {
	  while (true) {
	    console.log('Tick!');
	    yield;
	    console.log('Tock!');
	    yield;
	  }
	};
	```
11. Generator与协程
	+ 协程：可以并行执行、交换执行权的线程(或函数)
	+ Generator函数是ES6对协程的实现，但属于不完全实现。Generator函数被称为“半协程”(semi-coroutine)，意思是只有Generator函数的调用者，才能将程序的执行权还给Generator函数。如果是完全执行的协程，任何函数都可以让暂停的协程继续执行。
	+ 如果将Generator函数当作协程，完全可以将多个需要互相协作的任务写成Generator函数，它们之间使用yield表达式交换控制权。
12. Generator与上下文
	+ 它执行产生的上下文环境，一旦遇到yield命令，就会暂时退出堆栈，但是并不消失，里面的所有变量和对象会冻结在当前状态。等到对它执行next命令时，这个上下文环境又会重新加入调用栈，冻结的变量和对象恢复执行。
### Generator函数的异步应用
1. ES6诞生以前，异步编程的方法，大概有下面四种。
	1) 回调函数
	2) 事件监听
	3) 发布/订阅
	4) Promise 对象
2. Thunk
	1) 编译器的“传名调用”实现，往往是将参数放到一个临时函数之中，再将这个临时函数传入函数体。这个临时函数就叫做Thunk函数。
		```javascript
		function f(m) {
		  return m + 2;
		}
		f(x + 5);
		// 等同于
		var thunk = function () {
		  return x + 5;
		};
		function f(thunk) {
		  return thunk() + 2;
		}
		```
	2) JavaScript语言是传值调用，它的Thunk函数含义有所不同。在JavaScript语言中，Thunk函数替换的不是表达式，而是多参数函数，将其替换成一个只接受回调函数作为参数的单参数函数。
		```javascript
		// 正常版本的readFile(多参数版本)
		fs.readFile(fileName, callback);
		// Thunk版本的readFile(单参数版本)
		var Thunk = function (fileName) {
		  return function (callback) {
		    return fs.readFile(fileName, callback);
		  };
		};
		var readFileThunk = Thunk(fileName);
		readFileThunk(callback);
		```
	3) 任何函数，只要参数有回调函数，就能写成Thunk。函数的形式下面是一个简单的Thunk函数转换器。
		```javascript
		const Thunk = (fn) => (...args) => (callback) => fn.call(this, ...args, callback)
		// 使用上面的转换器，生成fs.readFile的Thunk函数。
		var readFileThunk = Thunk(fs.readFile);
		readFileThunk(fileA)(callback);
		```
	4) Thunk函数真正的威力，在于可以自动执行Generator函数。下面就是一个基于Thunk函数的Generator执行器。
		```javascript
		function run(fn) {
		  var gen = fn();
		  function next(err, data) {
		    var result = gen.next(data);
		    if (result.done) return;
		    result.value(next);
		  }
		  next();
		}
		var g = function* (){
		  var f1 = yield readFileThunk('fileA');
		  var f2 = yield readFileThunk('fileB');
		  // ...
		  var fn = yield readFileThunk('fileN');
		};
		run(g);
		/*
		run函数，就是一个Generator函数的自动执行器。内部的next函数就是Thunk的回调函数。
		next函数先将指针移到Generator函数的下一步(gen.next方法)，然后判断Generator函数是否结束(result.done属性)，如果没结束，就将next函数再传入Thunk函数(result.value属性)，否则就直接退出。
		*/
		```
3. co模块
	1) 基本用法
		<br>co模块可以让你不用编写Generator函数的执行器。
		```javascript
		var co = require('co');
		co(function* gen(){}).then(()=>{
		  console.log('finished')
		})
		```
	2) co模块的原理
		+ Generator就是一个异步操作的容器。它的自动执行需要一种机制，当异步操作有了结果，能够自动交回执行权。两种方法可以做到这一点。
			+ 回调函数。将异步操作包装成Thunk函数，在回调函数里面交回执行权。
			+ Promise对象。将异步操作包装成Promise对象，用then方法交回执行权。
		+ co模块其实就是将两种自动执行器(Thunk函数和Promise对象)，包装成一个模块。使用co的前提条件是，Generator函数的yield命令后面，只能是Thunk函数或Promise对象。
	3) 基于Promise对象的自动执行
		```javascript
		var fs = require('fs');
		var readFile = function (fileName){
		  return new Promise(function (resolve, reject){
		    fs.readFile(fileName, function(error, data){
		      if (error) return reject(error);
		      resolve(data);
		    });
		  });
		};
		var gen = function* (){
		  var f1 = yield readFile('/etc/fstab');
		  var f2 = yield readFile('/etc/shells');
		  console.log(f1.toString());
		  console.log(f2.toString());
		};
		function run(gen){
		  var g = gen();
		  function next(data){
		    var result = g.next(data);
		    if (result.done) return result.value;
		    result.value.then(function(data){
		      next(data);
		    });
		  }
		  next();
		}
		run(gen);
		```
	4) 处理并发的异步操作
	<br>co支持并发的异步操作，即允许某些操作同时进行，等到它们全部完成，才进行下一步。这时，要把并发的操作都放在数组或对象里面，跟在yield语句后面。
__________
# aync函数
### 含义
+ ES2017标准引入了async函数，使得异步操作变得更加方便。async函数是什么？一句话，它就是Generator函数的语法糖。
+ async函数对Generator函数的改进，体现在以下四点。
	1) 内置执行器
		<br>Generator函数的执行必须靠执行器，所以才有了co模块，而async函数自带执行器。
	2) 更好的语义。
		<br>async和await，比起星号和yield，语义更清楚了。async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果。
	3) 更广的适用性。
		<br>co模块约定，yield命令后面只能是Thunk函数或Promise对象，而async函数的await命令后面，可以是Promise对象和原始类型的值(数值、字符串和布尔值，但这时等同于同步操作)。
	4) 返回值是Promise。
		+ async函数的返回值是Promise对象，这比Generator函数的返回值是Iterator对象方便多了。你可以用then方法指定下一步的操作。
		+ 进一步说，async函数完全可以看作多个异步操作，包装成的一个Promise对象，而await命令就是内部then命令的语法糖。
### await命令
+ 正常情况下，await命令后面是一个Promise对象。如果不是，会被转成一个立即resolve的Promise对象。只要一个await语句后面的Promise变为reject，那么整个async函数都会中断执行。
	```javascript
	async function f() {
	  return await 123;
	}
	f().then(v => console.log(v)) // 123
	```
+ await命令后面的Promise对象如果变为reject状态，则reject的参数会被catch方法的回调函数接收到。
### 错误处理
```javascript
async function f() {
  try {
    await new Promise(function (resolve, reject) {
      throw new Error('出错了');
    });
  } catch(e) {}
  return await('hello world');
}
```
### 使用注意点
1) await命令后面的Promise对象，运行结果可能是rejected，所以最好把await命令放在try...catch代码块中。
2) 多个await命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。
	```javascript
	let foo = await getFoo();
	let bar = await getBar();
	/*
	getFoo和getBar是两个独立的异步操作(即互不依赖)，被写成继发关系。这样比较耗时，因为只有getFoo完成以后，才会执行getBar，完全可以让它们同时触发。
	*/
	let [foo, bar] = await Promise.all([getFoo(), getBar()]);
	```
3) await命令只能用在async函数之中，如果用在普通函数，就会报错。
### async函数的实现原理
+ async函数的实现原理，就是将Generator函数和自动执行器，包装在一个函数里。
### 异步遍历器
+ Iterator接口是一种数据遍历的协议，只要调用遍历器对象的next方法，就会得到一个对象，表示当前遍历指针所在的那个位置的信息。next方法返回的对象的结构是{value, done}。
+ 这里隐含着一个规定，next方法必须是同步的，只要调用就必须立刻返回值。也就是说，一旦执行next方法，就必须同步地得到value和done这两个属性。如果遍历指针正好指向同步操作，当然没有问题，但对于异步操作，就不太合适了。目前的解决方法是，Generator函数里面的异步操作，返回一个Thunk函数或者Promise对象，即value属性是一个Thunk函数或者Promise对象，等待以后返回真正的值，而done属性则还是同步产生的。
+ ES2018引入了”异步遍历器“(Async Iterator)，为异步操作提供原生的遍历器接口，即value和done这两个属性都是异步产生。
1. 异步遍历的接口
	+ 最大的语法特点，就是调用遍历器的next方法，返回的是一个Promise对象。
		```javascript
		async function f() {
		  const asyncIterable = createAsyncIterable(['a', 'b']);
		  const asyncIterator = asyncIterable[Symbol.asyncIterator]();
		  console.log(await asyncIterator.next());//{value:'a',done: false}
		  console.log(await asyncIterator.next());//{value:'b', done:false}
		  console.log(await asyncIterator.next());//{value:undefined,done:true}
		}
		```
	+ 异步遍历器的next方法是可以连续调用的，不必等到上一步产生的Promise对象resolve以后再调用。这种情况下，next方法会累积起来，自动按照每一步的顺序运行下去
	+ 另一种用法是一次性调用所有的next方法，然后await最后一步操作。
		```javascript
		async function runner() {
		  const writer = openFile('someFile.txt');
		  writer.next('hello');
		  writer.next('world');
		  await writer.return();
		}
		runner();
		```
2. for await...of
	+ for...of循环用于遍历同步的Iterator接口。新引入的for await...of循环，则是用于遍历异步的Iterator接口。
		```javascript
		async function f() {
		  for await (const x of createAsyncIterable(['a', 'b'])) {
		    console.log(x);
		  }
		}
		```
	+ 如果next方法返回的Promise对象被reject，for await...of就会报错，要用try...catch捕捉。
	+ for await...of循环也可以用于同步遍历器
3. 异步Generator函数
	+ 在语法上，异步Generator函数就是async函数与Generator函数的结合。
		```javascript
		async function* gen() {
		  yield 'hello';
		}
		const genObj = gen();
		genObj.next().then(x => console.log(x));
		// { value: 'hello', done: false }
		```
	+ 异步Generator函数内部，能够同时使用await和yield命令。可以这样理解，await命令用于将外部操作产生的值输入函数内部，yield命令用于将函数内部的值输出
4. yield* 语句
	+ yield*语句也可以跟一个异步遍历器。
		```javascript
		async function* gen1() {
		  yield 'a';
		  yield 'b';
		  return 2;
		}
		async function* gen2() {
		  // result 最终会等于 2
		  const result = yield* gen1();
		}
		```
	+ 与同步 Generator 函数一样，for await...of循环会展开yield*。
		```javascript
		(async function () {
	    for await (const x of gen2()) {
		    console.log(x);
		  }
		})();
		// a
		// b
		```
_______
# Class
### 简介
+ 类的数据类型就是函数，类本身就指向构造函数。prototype对象的constructor属性，直接指向“类”的本身。在类的实例上面调用方法，其实就是调用原型上的方法。
+ 构造函数的prototype属性，在 ES6的“类”上面继续存在。事实上，类的所有方法都定义在类的prototype属性上面。
+ 类的内部所有定义的方法，都是不可枚举的(non-enumerable)
+ 类和模块的内部，默认就是严格模式
+ 类必须使用new调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用new也可以执行。
+ 与ES5一样，实例的属性除非显式定义在其本身(即定义在this对象上)，否则都是定义在原型上(即定义在class上)。
+ 与ES5一样，类的所有实例共享一个原型对象
### Class表达式
```javascript
const MyClass = class {
  getClassName() {
    return this.name;
  }
};
```
### 私有方法和私有属性
<br>私有方法是常见需求，但ES6不提供，只能通过变通方法模拟实现。
1. function()定义在模块外，call调用
	```javascript
	class Widget {
	  foo (baz) {
	    bar.call(this, baz);
	  }
	}
	function bar(baz) {
	  return this.snaf = baz;
	}
	```
2. 将方法名命名为Symbol值
	```javascript
	const bar = Symbol('bar');
	const snaf = Symbol('snaf');
	export default class myClass{
	  // 公有方法
  	foo(baz) {
	    this[bar](baz);
	  }
  	// 私有方法
	  [bar](baz) {
	    return this[snaf] = baz;
	  }
	};
	```
3. 私有属性的提案：使用#表示
	```javascript
	class Foo {
	  #a;
	  #b;
	  #sum() { return #a + #b; }
	  printSum() { console.log(#sum()); }
	  constructor(a, b) { #a = a; #b = b; }
	}
	```
### this的指向
<br>类的方法内部如果含有this，它默认指向类的实例。但是，必须非常小心，一旦单独使用该方法，很可能报错。
```javascript
class Logger {
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }
  print(text) {
    console.log(text);
  }
}
const logger = new Logger();
const { printName } = logger;
printName(); // TypeError: Cannot read property 'print' of undefined
/*
 此时this会指向该方法运行时所在的环境。解决方法： 绑定this， bind()方法 或 箭头函数
*/
```
### Class的静态方法
+ 类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”
+ 如果静态方法包含this关键字，这个this指的是类，而不是实例
+ 父类的静态方法，可以被子类继承
+ 静态方法也是可以从super对象上调用的
### Class的静态属性和实例属性
1. ES6明确规定，Class内部只有静态方法，没有静态属性。
	```javascript
	class Foo {}
	Foo.prop = 1;
	Foo.prop // 1
	```
2. 目前有一个静态属性的提案，对实例属性和静态属性都规定了新的写法。以前，我们定义实例属性，只能写在类的constructor方法里面。有了新的写法以后，可以不在constructor方法里面定义。
	```javascript
	class MyClass extends React.Component {
	  static myStaticProp = 42;  // 类的静态属性
	  state = {                  // 类的实例属性
	    count: 0
	  };
	  constructor() {
	    console.log(MyClass.myStaticProp); // 42
	  }
	}
	```
### new.target属性
+ new是从构造函数生成实例对象的命令。ES6为new命令引入了一个new.target属性，该属性一般用在构造函数之中，返回new命令作用于的那个构造函数。如果构造函数不是通过new命令调用的，new.target会返回undefined，因此这个属性可以用来确定构造函数是怎么调用的。
+ Class内部调用new.target，返回当前Class。子类继承父类时，new.target会返回子类
### Class的继承简介
+ 表示父类的构造函数，用来新建父类的this对象。
+ 子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类自己的this对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象。
+ ES5的继承，实质是先创造子类的实例对象this，然后再将父类的方法添加到this上面(Parent.apply(this))。ES6的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到this上面(所以必须先调用super方法)，然后再用子类的构造函数修改this。
### super关键字
1. super这个关键字，既可以当作函数使用，也可以当作对象使用。
2. 第一种情况，super作为函数调用时，代表父类的构造函数。
	1) ES6要求，子类的构造函数必须执行一次super函数。
	2) super虽然代表了父类A的构造函数，但是返回的是子类B的实例，即super内部的this指的是B，因此super()在这里相当于A.prototype.constructor.call(this)
		```javascript
		class A {
		  constructor() {
		    console.log(new.target.name);
		  }
		}
		class B extends A {
		  constructor() {
		    super();
		  }
		}
		new A() // A
		new B() // B
		```
	3) 作为函数时，super()只能用在子类的构造函数之中，用在其他地方就会报错
3. 第二种情况，super作为对象时
	1) 在普通方法中，指向父类的原型对象；在静态方法中，指向父类。
	2) ES6规定，在子类普通方法中通过super调用父类的方法时，方法内部的this指向当前的子类实例
	3) 由于this指向子类实例，所以如果通过super对某个属性赋值，这时super就是this，赋值的属性会变成子类实例的属性
	4) 在子类的静态方法中通过super调用父类的方法时，方法内部的this指向当前的子类，而不是子类的实例
4. 最后，由于对象总是继承其他对象的，所以可以在任意一个对象中，使用super关键字
### 类的prototype属性和\_\_proto\_\_属性
1. 大多数浏览器的ES5实现之中，每一个对象都有__proto__属性，指向对应的构造函数的prototype属性。Class作为构造函数的语法糖，同时有prototype属性和__proto__属性，因此同时存在两条继承链。
	1) 子类的__proto__属性，表示构造函数的继承，总是指向父类。
	2) 子类prototype属性的__proto__属性，表示方法的继承，总是指向父类的prototype属性
2. 实例的__proto__属性
	<br>子类实例的__proto__属性的__proto__属性，指向父类实例的__proto__属性。也就是说，子类的原型的原型，是父类的原型
### 原生构造函数的继承
1. 原生构造函数是指语言内置的构造函数，通常用来生成数据结构。
2. 在ES5，原生构造函数是无法继承的。因为子类无法获得原生构造函数的内部属性，通过Array.apply()或者分配给原型对象都不行。原生构造函数会忽略apply方法传入的this，也就是说，原生构造函数的this无法绑定，导致拿不到内部属性
3. ES5是先新建子类的实例对象this，再将父类的属性添加到子类上，由于父类的内部属性无法获取，导致无法继承原生的构造函数。
4. ES6允许继承原生构造函数定义子类，因为ES6是先新建父类的实例对象this，然后再用子类的构造函数修饰this，使得父类的所有行为都可以继承。
### Mixin模式的实现
<br>Mixin 指的是多个对象合成一个新的对象，新对象具有各个组成成员的接口。
```javascript
function mix(...mixins) {
  class Mix {}
  for (let mixin of mixins) {
    copyProperties(Mix.prototype, mixin); // 拷贝实例属性
    copyProperties(Mix.prototype, Reflect.getPrototypeOf(mixin)); //拷贝原型属性
  }
  return Mix;
}
function copyProperties(target, source) {
  for (let key of Reflect.ownKeys(source)) {
    if (key !== "constructor" && key !== "prototype" && key !== "name") {
      let desc = Object.getOwnPropertyDescriptor(source, key);
      Object.defineProperty(target, key, desc);
    }
  }
}
```
_________
# Decorator
### 类的修饰
+ 基本上，修饰器的行为就是下面这样
	```javascript
	@decorator
	class A {}
	// 等同于
	class A {}
	A = decorator(A) || A;
	```
+ 修饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。这意味着，修饰器能在编译阶段运行代码。也就是说，修饰器本质就是编译时执行的函数。
### 方法的修饰
```javascript
class Person {
  @readonly
  name() { return `${this.first} ${this.last}` }
}
```
+ 如果同一个方法有多个修饰器，会像剥洋葱一样，先从外到内进入，然后由内向外执行
+ 修饰器只能用于类和类的方法，不能用于函数，因为存在函数提升。
+ 如果一定要修饰函数，可以采用高阶函数的形式直接执行
	```javascript
	function doSomething(name) {
	  console.log('Hello, ' + name);
	}
	function loggingDecorator(wrapped) {
	  return function() {
	    console.log('Starting');
	    const result = wrapped.apply(this, arguments);
	    console.log('Finished');
	    return result;
	  }
	}
	const wrapped = loggingDecorator(doSomething);
	```
### Mixin
<br> 在修饰器的基础上，可以实现Mixin模式。所谓Mixin模式，就是对象继承的一种替代方案，中文译为“混入”(mix in)，意为在一个对象之中混入另外一个对象的方法。
```javascript
export function mixins(...list) {
  return function (target) {
    Object.assign(target.prototype, ...list);
  };
}
```
__________
# Module的语法
### 概述
+ ES6模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量，(编译时加载)。CommonJS和AMD模块，都只能在运行时确定这些东西，导致完全没办法在编译时做“静态优化”，(运行时加载)。CommonJS模块就是对象，输入时必须查找对象属性。
+ 由于ES6模块是编译时加载，使得静态分析成为可能。有了它，就能进一步拓宽JavaScript的语法，比如引入宏(macro)和类型检验(type system)这些只能靠静态分析实现的功能。
+ ES6模块自动采用严格模式，同时，顶层的this指向undefined，即不应该在顶层代码中使用this。
### ES6的模块自动采用严格模式，不管你有没有在模块头部加上"use strict";。严格模式主要有以下限制。
+ 变量必须声明后再使用
+ 函数的参数不能有同名属性，否则报错
+ 不能使用with语句
+ 不能对只读属性赋值，否则报错
+ 不能使用前缀 0 表示八进制数，否则报错
+ 不能删除不可删除的属性，否则报错
+ 不能删除变量delete prop，会报错，只能删除属性delete global[prop]
+ eval不会在它的外层作用域引入变量
+ eval和arguments不能被重新赋值
+ arguments不会自动反映函数参数的变化
+ 不能使用arguments.callee
+ 不能使用arguments.caller
+ 禁止this指向全局对象
+ 不能使用fn.caller和fn.arguments获取函数调用的堆栈
+ 增加了保留字（比如protected、static和interface）
### export
+ export的写法
	```javascript
	// 写法1：
	export var m = 1;
	// 写法2：
	var m = 1;
	export {m}
	// 写法3
	var n = 1;
	export {n as m}
	/*当import导入时需要 {} , 同时需要知道所加载的变量名或函数名 */
	```
+ export语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。这一点与CommonJS规范完全不同。CommonJS模块输出的是值的缓存，不存在动态更新。
	```javascript
	export var foo = 'bar';
	setTimeout(() => foo = 'baz', 500);
	```
+ export和import可以出现在模块的任何位置，只要处于顶层就可以。如果处于块级作用内，就会报错。这是因为处于条件代码块之中，就没法做静态优化了。
### export default命令
+ 为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到export default命令，为模块指定默认输出。当import导入时不需要{}，可以用任意名称指向输出的方法。
	```javascript
	var a = 1;
	export default a
	/*
	本质上，export default就是输出一个叫做default的变量或方法，然后系统允许你为它取任意名字。等同于 export {a as default} 。正是因为export default命令其实只是输出一个叫做default的变量，所以它后面不能跟变量声明语句。
	*/
	```
+  一个模块只能有一个默认输出，因此export defaut命令只能使用一次
+ 因为export default命令的本质是将后面的值，赋给default变量，所以可以直接将一个值写在export default之后
	```javascript
	export default 42;
	```
### import
+ import命令输入的变量都是只读的，因为它的本质是输入接口。也就是说，不允许在加载模块的脚本里面，改写接口。但是如果接口是对象，可以改写属性。
+ import命令具有提升效果，会提升到整个模块的头部，首先执行。本质是因为import命令是编译阶段执行的，在代码运行之前。
+ 由于import是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。
+ 模块的整体加载，即用星号(+)指定一个对象，所有输出值都加载在这个对象上，同时这个对象不允许改变。
### import()
+ import命令能够接受什么参数，import()函数就能接受什么参数，两者区别主要是后者为动态加载。另外，import()函数与所加载的模块没有静态连接关系，这点也是与import语句不相同。
+ import()函数动态加载，可以用在任何地方，不仅仅是模块，非模块的脚本也可以使用。它是运行时执行，也就是说，什么时候运行到这一句，就会加载指定的模块。类似于Node的require方法，区别主要是前者是异步加载，后者是同步加载。
+ 适用场合
	1) 按需加载
	2) 条件加载
+ import()加载模块成功以后，这个模块会作为一个对象，当作then方法的参数。因此，可以使用对象解构赋值的语法，获取输出接口。
+ import()也可以用在async函数之中。

### 浏览器加载
1. 传统方法：`<script>`标签。
	1) 默认情况下，浏览器是同步加载JavaScript脚本，即渲染引擎遇到`<script>`标签就会停下来，等到执行完脚本，再继续向下渲染，如果是外部脚本，还必须加入脚本下载的时间
	2) 异步加载
		+ 标签打开defer或async属性，脚本就会异步加载。渲染引擎遇到这一行命令，会开始下载外部脚本，但是不会等它下载和执行，而是直接执行后面的命令。
		+ defer与aync的区别是：defer要等到整个页面在内存中正常渲染结束(DOM结构完全生成，以及其他脚本执行完成)，才会执行；async一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。一句话，defer是“渲染完再执行”, async是“下载完执行”。另外，如果有多个defer脚本，会按照它们在页面出现的顺序加载，而多个async脚本是不能保证加载顺序的。
2. 浏览器加载ES6模块
	+ 使用`<script>`标签，但是要加入`type="module"`属性。
	+ 浏览器对于带有`type="module"`的`<script>`，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了`<script>`标签的defer属性。
	+ 如果网页有多个`<script type="module">`，它们会按照在页面出现的顺序依次执行。一旦使用了async属性，`<script type="module">`就不会按照在页面出现的顺序执行，而是只要该模块加载完成，就执行该模块。
### ES6模块与CommonJS模块的差异
1. 两个重大差异
	- CommonJS模块输出的是一个值的拷贝，ES6模块输出的是值的引用。
	- CommonJS模块是运行时加载，ES6模块是编译时输出接口
2. 第二个差异是因为CommonJS加载是一个对象(即modele.export属性)，该对象只有在脚本运行完才会生成。而ES6模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。
3. CommonJS模块输出是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。
	```javascript
	//lib.js
	var counter = 3;
	function incCounter(){
	  counter++;
	}
	module.exports = {
	  counter:counter,
	  incCounter:incCounter
	};
	// main.js
	var mod = require('./lib');
	console.log(mode.counter)  //3
	mod.incCounter()
	console.log(mode.counter) //3
	/*
	这是因为mode.counter是一个原始类型的值，会被缓存。除非写成一个函数，才能得到内部变动后的值。
	*/
	```
	```javascript
	// lib.js
	var counter = 3;
	function incCounter(){
	  counter++;
	}
	module.export={
	  get counter(){
	    return counter
	  },
	  incCounter:incCounter
	} /*输出的counter属性实际上是一个取值器函数*/
	// main.js
	var mod = require('./lib');
	console.log(mode.counter)  //3
	mod.incCounter()
	console.log(mode.counter) //4
	```
+ ES6模块的运行机制与CommonJS不一样。JS引擎对脚本静态分析的时候，遇到模块加载命令import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。
+ 换句话说，ES6的import有点像Unix系统的“符号连接”，原始值变了，import加载的值也会跟着变。因此，ES6模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块。
	```javascript
	//lib.js
	export let counter = 3;
	export function incCounter(){
	  counter++
	}
	// main.js
	import { counter, incCounter } from './lib';
	console.log(counter) //3
	incCounter()
	console.log(counter) //4
	```
### Node加载
1. Node要求ES6模块采用.mjs后缀文件名。也就是说，只要脚本文件里面使用import或者export命令，那么就必须采用.mjs后缀名
2. 内部变量
	1) 首先，就是this关键字。ES6模块之中，顶层的this指向undefined；CommonJS模块的顶层this指向当前模块，这是两者的一个重大差异。
	2) 其次，以下这些顶层变量在ES6模块之中都是不存在的。arguments、require、module、exports、\_\_filename、\_\_dirname
3. ES6模块加载CommonJS模块
	+ CommonJS 模块的输出都定义在module.exports这个属性上面。Node的import命令加载CommonJS模块，Node会自动将module.exports属性，当作模块的默认输出，即等同于export default xxx。
	+ import命令加载上面的模块，module.exports会被视为默认输出。
### CommonJS模块规范 vs AMD模块规范
1. CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作。AMD规范则是非同步加载模块，允许指定回调函数
2. CommonJS特点
	+ 所有代码都运行在模块作用域，不会污染全局作用域。
	+ 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存
	+ 模块加载的顺序，按照其在代码中出现的顺序。
3. AMD规范使用define方法定义模块, 浏览器端一般采用AMD规范
### module.exports vs  exports |  export vs export default
1. module.exports vs  exports
	+ exports是引用module.exports的值；module.exports初始值为一个空对象{}，所以exports初始值也是{}；module.exports被改变的时候，exports不会被改变，而模块导出的时候，真正导出的执行是module.exports，而不是exports 。
		```javascript
		module={
		  exports:{}
		}
		exports=module.exports
		```
	+ 第一种情况，module.exports初始值为空对象，两个函数使用module.exports或exports都一样效果；第二种情况，module.exports初始值不为空对象，只能使用module.exports暴露接口，而不能使用exports暴露，会出现 xxx is not a function错误。
		```javascript
		// 1.js
		function sayHello() {
		  console.log('Hello');
		}
		function sayHi(name) {
		  console.log('Hi');
		}
		module.exports = {name:1};
		// 当使用这种方法导出时
		module.exports.sayHello = sayHello;
		// 2.js
		var a = require('./1.js')
		a.sayHello()    // Hello
		//当使用这种方法导出时
		exports.sayHello = sayHello;
		// 2.js
		var a = require('./1.js')
		a.sayHello()    // a.sayHello is not a function
		```
	+ javascript里面有一句话，函数即对象，test是对象，module.exports = test, 即相当于导出整个test对象。外面模块调用它的时候，能够调用test的所有方法。不过需要注意，只有是test的静态方法的时候，才能够被调用，prototype创建的方法，则属于test的私有方法。
		```javascript
		// 1.js
		var test = function(){}
		test.prototype.sayHello = sayHello
		test.sayHi = sayHi
		module.exports = test
		// 2.js
		var a = require('./1.js')
		a.sayHi()    // Hi
		a.sayHello()   // a.sayHello is not a function
		```
2. exports vs export
	+ export是es6引入的语法，用于导出模块中的变量，对象，函数，类。对应的导入关键字是import。
	+ exports是一个对象，不是语法，对应导入关键字是require (CommonJS)
3. export vs export default
	+ export default在一个模块中只能有一个，当然也可以没有；export在一个模块中可以有多个
	+ export对应的import必须加上{}
