# 基本概念
### 数据类型
ECMAScript中有5种简单数据类型: Undefined、Null、Boolean、Number、String，还有1种复杂数据类型Object。
+ undefined
	<br>在使用var声明变量但未对其加以初始化时，这个变量就是undefined。typeof undefined返回undefined, typeof未声明的变量也是返回undefined。<br>
  `String(undefined)   // undefined`
+ null
	<br>是一个空指针对象，而这也是typeof检测null值返回"object"的原因。<br>
	`String(null)   // null`
_____

# 变量、作用域和内存问题
### 基本类型和引用类型的值
1. 复制变量值 `(浅复制)`
	+ 如果从一个变量向另一个变量复制基本类型的值，会在变量对象上创建一个新值，然后把该值复制到为新变量分配的位置上。
	+ 当从一个变量向另一个变量复制引用类型的值时，同样也会将存储在变量对象中的值复制一份到新变量分配的空间中。不同的是，这个值的副本实际上是一个指针，而这个指针指向存储在堆中的一个对象。复制操作结束后，两个变量实际上将引用同一个对象。因此，改变其中一个变量，就会影响另一个变量。
2. 检测类型
	1) `typeof` 操作符是确定一个变量是字符串、数值、布尔值、还是undefined的最佳工具，如果变量是一个对象或者null，则typeof操作符都会返回"object"。
	2) `instanceof` 操作符检测是什么类型的对象。
		+ 如果变量是给定引用类型的实例，那么instanceof操作符就会返回true。
		+ 如果使用instanceof操作符检测基本类型的值，则该操作符始终会返回false，因为基本类型不是对象。
### 执行环境及作用域
+ `执行环境(execution context)`定义了变量或函数有权访问的其他数据，决定了它们各自的行为。每个执行环境都有一个与之关联的`变量对象(variable object)`, 环境中定义的所有变量和函数都保存在这个对象中。<br>
+ 当代码在一个环境中执行时，会创建变量对象的`作用域链(scope chain)`。作用域链的用途，是保证对执行环境有权访问的所有变量和函数的有序访问。作用域链的前端，始终是当前执行的代码所在环境的变量对象。如果这个环境是函数，则将其活动对象作为变量对象。活动对象在最开始时只包含一个变量，即`arguments`对象。作用域链中的下一个变量对象来自包含(外部)环境，而再下一个变量对象则来自下一个包含环境。这样，一直延续到全局执行环境;全局执行环境的变量对象始终是作用域链中的最后一个对象。<br>
+ `标识符`解析是沿着作用域链一级一级地搜索标识符的过程。搜索过程始终从作用域链的前端开始，然后逐级地向后回溯，直到找到标识符为止。如果没有找到这个标识符，则意味着该变量尚未声明。
1) 延长作用域链
	<br>虽然执行环境的类型总共只有两种——全局和局部(函数)，但是还是有其他办法来延长作用域链。这么说是因为有些语句可以在作用域链的前端临时增加一个变量对象，该变量对象会在代码执行后被移除。<br>
	- try-catch语句的catch块
	- with 语句

	<br>这两个语句都会在作用域前端添加一个变量对象。对with语句来说，会将指定的对象添加到作用域中。对catch语句来说，会创建一个新变量对象，其中包含的是被抛出的错误对象的声明。
2) 没有块级作用域
	<br>使用var声明的变量会自动被添加到最接近的环境中。在函数内部，最接近的环境就是函数的局部环境；在with语句中，最接近的环境是函数环境,如果初始化变量时没有使用var声明，该变量会自动被添加到全局环境。
### 垃圾收集
JavaScript是一门具有自动垃圾收集机制的编程语言。离开作用域的值将被自动标记为可以回收，因此将在垃圾收集期间被删除。
1) 标记清除
	<br>标记清除是目前主流的垃圾收集算法，这种算法的思想是给当前不使用的值加上标记，然后再回收其内存。
2) 引用计数
 	<br>引用计数，这种算法的思想是跟踪记录所有值被引用的次数。JavaScript引擎目前都不再使用这种算法；但在IE中访问非原生JavaScript对象(如DOM元素)时，这种算法仍然可能会导致问题。
	+ 当代码中存在循环引用现象时，引用计数算法就会导致问题。
	+ 解除变量的引用不仅有助于消除循环引用现象，而且对垃圾收集也有好处。为了确保有效地回收内存，应该及时解除不再使用的全局对象、全局对象属性以及循环引用变量的引用。
_____

# 引用类型
### Array 类型
1) 检测数组
	+ instanceof: 局限于一个网页或者一个全局作用域
	+ isArray(): 确定某个值是否为数组，不管它是在哪个全局执行环境中创建的
2) 栈方法
	+ 栈顶部   后进先出  (LIFO Last-In-First-Out)
	+ push()   pop()
3) 队列方法
	+ 先进先出 FIFO First-In-First-Out
	+ push()  shift()
	+ unshift() pop()  反向模拟队列
4) 重排序方法
	+ reverse()  反转数组项的顺序
	+ sort()
	<br>默认情况下，按升序排列数组项，sort()方法会调用每个数组的toString()，然后比较得到的字符串，即使数组中的每一项都是数值，sort()方法比较也是字符串。
5) 操作方法
	+ concat()
	<br>基于当前数组中的所有项创建一个新数组，具体来说，这个方法会先创建当前数组的一个副本，然后将接收到的参数添加到这个副本的末尾，最后返回新构建的数组。 `(不改变原数组)`
	+ slice()
	<br>基于当前数组中的一或多个项创建一个新数组，接收一或两个参数，即开始位置和结束位置(不包含)。  `(不改变原数组)`
	+ splice()  `(改变原数组)`
		<br>splice()方法始终都会返回一个数组，该数组中包含从原始数组中删除的项，(如果没有删除任意项，则返回一个空数组)
		+ 删除: 可以删除任意数量的项，只需指定2个参数:  要删除的第一项的位置，和要删除的项数。
		+ 插入: 可以向指定位置插入任意数量的项，只需要提供3个参数: 起始位置、0(要删除的项数)、要插入的项。如果要插入多个项，可再传入第四、第五以至任意多个项。
		+ 替换: 可以向指定位置插入任意的数量的项，且同时删除任意数量的项，只需指定3个参数，起始位置、要删除的项数、要插入的任意数量的项。
6) 位置方法
	+ lastIndexOf()
	+ indexOf()
7) 迭代方法
	+ every(): 对数组中的每一项运行给定函数，如果该函数对每一项都返回true， 则返回true。
	+ some(): 对数组中的每一项运行给定函数，如果该函数对任一项返回true，则返回true。
	+ filter(): 对数组中的每一项运行给函数，返回该函数会返回true的项组成的数组。
	+ forEach(): 对数组中的每一项运行函数。这个方法没有返回值。
	+ map(): 对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。
8) 缩小方法
	+ reduce()
	+ reduceRight()
	<br>这两个方法都会迭代数组的所有项，然后构建一个最终返回的值，其中，reduce()方法从数组的第一项开始，逐个遍历到最后。而reduceRight()则从数组的最后一项开始，向前遍历到第一项。
	<br>这两个方法都接收`两个参数`: 一个在每一项上调用的`函数`和(可选的)作为缩小基础的`初始值`。传给reduce()和reduceRight()的函数接收4个参数: 前一个值，当前值，项的索引和数组对象。
	```javascript
	var values = [1, 2, 3, 4, 5];
	var sum = values.reduce(function(prev, cur, index, array){
	  return prev + cur
	})
	console.log(sum); //15
	```
### Date 类型
1. toUTCString() 以特定于实现的格式完整的UTC日期
2. ECMAScript 5添加了Date.now()方法，返回表示调用这个方法时的日期和时间的毫秒数。
	```javascript
	var start = Date.now();
	doSomething();
	var stop = Date.now(),
	  result = stop - start
	//在不支持它的浏览器中使用+操作符把Date对象转换成字符串，也能达到同样的目的。
	var start = +new Date();
	doSomething();
	var stop = +new Date(),
	  result = stop - start
	```
### RegExp类型
1. 创建:  `var expression =  /pattern/flags;`
<br>使用正则表达式字面量和使用RegExp构造函数创建的正则表达式不一样。在ECMAScript 3中，正则表达式字面量始终会共享同一个RegExp实例，而使用构造函数创建的每一个新RegExp实例都是一个新实例。
2. RegExp实例方法
	1) exec()该方法是专门为捕获组而设计的。
		+ exec()接受一个参数，即要应用模式的字符串，然后返回包含第一个匹配项信息的数组; 或者在没有匹配项的情况下返回null。
		+ 返回的数组虽然是Array的实例，但包含两个额外的属性: `index` 和 `input` 。其中，index表示匹配项在字符串中的位置，而input表示应用正则表达式的字符串。在数组中，第一项是与整个模式匹配的字符串，其他项是与模式中的捕获组匹配的字符串(如果模式中没有捕获组，则该数组只包含一项)。
		```javascript
		var text = "mom and dad and baby"
		var pattern = /mom( and dad( and baby)?)?/gi
		var matches = pattern.exec(text);
		console.log(matches.index)     //0
		console.log(matches.input)     // "mom and dad and baby"
		console.log(matches[0])       // "mom and dad and baby"
		console.log(matches[1])       // " and dad and baby"
		console.log(matches[2])       // " and baby"
		```
	2) test()方法
		+ 接受一个字符串参数。在模式与该参数匹配的情况下返回true，否则，返回false。
		+ 在只想知道目标字符串与某个模式是否匹配，但是不需要知道其文本内容的情况下，使用这个方法非常方便。
		+ 经常出现在验证用户输入的情况下。
	3) RegExp构造函数属性

	|   长属性名       | 短属性名   | 说明 |
	|--------------|----------|----|
	|  input         | $_          | 最近一次要匹配的字符串，Opera未实现此属性|
	|  lastMatch     | $&          | 最近一次的匹配项，Opera未实现此属性|
	|  lastParen     | $+          | 最近一次匹配的捕获组。Opera未实现此属性|
	|  leftContext   | $\`          | input字符串中lastMatch之前的文本|
	|  multiline     | $*          | 布尔值。表示是否所有表达式都使用多行模式(Opera和IE外) |
	|  rightContext  | $'          | input字符串中lastMatch之后的文本|

### Function 类型
1. 语法
	```javascript
	var sum = new Function('num1', 'num2','return num1 + num2');
	/*
	不推荐使用:这种语法会导致解析两次代码(第一次是解析常规ECMAScript代码，第二次是解析传入构造函数中的字符串)，从而影响性能
	*/
	```
2. 没有重载
<br>ECMAScript中没有函数签名的概念，因为其函数参数是以一个包含零或多个值的数组的形式传递的。由于不存在函数签名特性，ECMAScript函数不能重载。
3. 作为值的函数
4. 函数内部的属性
	+ this: 引用的是函数据以执行的环境对象——或者也可以说是this值(当在网页的全局作用域中调用函数时，this对象引用的就是window)。
	+ arguments: 主要用途是保存函数参数，arguments还有一个callee属性，该属性是一个指针，指向拥有这个arguments对象的函数。
5. 函数属性和方法
	+ 属性:
		+ length: 函数希望接收的命名参数的个数
		+ prototype: 不可枚举 for...in 无法发现
	+ apply(): 接收两个参数，一个是在其中运行函数的作用域，另一个是参数数组
	+ call(): 与apply()方法的作用相同，区别在于接收参数方式的不同，传递给函数的参数必须逐个列举出来。
	+ 事实上，传递参数并非apply()和call()真正的用武之地，它们真正强大的地方是能够`扩充函数赖以运行的作用域`。
	```javascript
	window.color = "red"
	var o = {color: "blue"}
	function sayColor(){
	  console.log(this.color)
	}
	sayColor()  // red
	sayColor.call(this)   // red
	sayColor.call(window) // red
	sayColor.call(o)      // blue
	/*
	使用call()(或apply()) 来扩充作用域的最大好处，就是对象不需要与方法有任何耦合关系。
	*/
	```
	+ bind(): 这个方法会创建一个函数的实例，其this值会被绑定到传给bind()函数的值。
		```javascript
		window.color = "red"
		var o = { color: "blue" }
		function sayColor(){
		  console.log(this.color)
		}
		var objectSayColor = sayColor.bind(o)
		objectSayColor()  // blue
		```
### 基本包装类型
1. 基本包装类型
	+ 在读取模式中访问字符串，后台都会自动完成下列处理
		1) 创建String类型的一个实例
		2) 在实例上调用指定的方法
		3) 销毁这个实例
		```javascript
		var s1 = new String('some text')
		var s2 = s1.substring(2)
		s1 = null
		```
	+ 引用类型与基本包装类型的主要区别就是对象的生存期。使用new操作符创建的引用的实例， 在执行流离开当前作用域之前都一直保存在内存中。而自动创建的基本包装类型的对象，则只存在于一行代码的执行瞬间，然后立即被销毁。这意味着我们不能在运行时为基本类型添加属性和方法。
	+ 要注意的是，使用new调用基本包装类型的构造函数，与直接调用同名的转型函数是不一样的。
		```javascript
		var value = '25'
		var number = Numer(value)  //转型函数
		console.log(typeof number)   //"number"
		var obj = new Number(value)   // 构造函数
		console.log(typeof obj)       // "object"
		```
	+ 对基本包装类型的实例调用typeof会返回"object"，而且所有基本包装的对象都会被转换为布尔值true。
	+ Object构造函数也会像工厂方法一样，根据传入值的类型返回相应基本包装类型的实例。
		```javascript
		var obj = new Object("some text");
		console.log(obj instanceof String)   // true
		```
2. Boolean类型
	+ Boolean类型的实例重写了valueOf()方法，返回基本类型值的true或false。
	 	```javascript
		var falseObject = new Boolean(false);
		var result = falseObject && true;
		console.log(result);     // true
		var falseValue = false;
		result = falseValue && true
		console.log(result);  //false
		/*
		布尔表达式中的所有对象都会被转换为true
		*/
		```
	+ 基本类型与引用类型的布尔值还有两个区别:
		1) typeof操作符对基本类型返回"boolean"，而对引用类型返回"object"
		2) 由于Boolean对象是Boolean类型的实例，所以使用instanceof操作符测试Boolean对象会返回true，而测试基本类型的布尔值会回false。
		```javascript
		console.log(typeof falseObject)  // object
		console.log(typeof falseValue)   // boolean
		console.log(falseObject instanceof Boolean)  // true
		console.log(falseValue instanceof Boolean)   // false
		```
3. Number类型
	+ toFixed()
4. String类型
	1) 字符方法
		+ charAt(): 以单字符字符串的形式返回给定位置的那个字符
		+ charCodeAt(): 返回字符编码
		+ fromCharCode(): 接收一或多个字符编码，然后将它们转换成一个字符串。
			```javascript
			//数字 -> 字母 排序
			String.fromCharCode(65 + num)  // 123 -- ABC
			```
	2) 字符串拼接
		+ concat()
		+ \+ (更多选用)
	3) 基于子字符串创建新字符串的方法
		+ slice() 、 substr() 、 substring()
		+ 具体来说，`slice()`和`substring()`的第二个参数指定的是子字符串最后一个字符后面的位置(不包括)，而`substr()`的第二个参数指定的是返回的字符个数。如果没有传递第二个参数，则将字符串的长度作为结束位置。
		+ 都不会修改字符串本身的值。
		+ 在传递给这些方法的参数是`负数`的情况下，它们的行为就不尽相同了。其中，`slice()`方法会将传入的负值与字符串的长度相加；`substr()`方法将负的第一个参数加上字符串的长度，而将负的第二个参数转换为0； `substring()`方法会把所有的负值参数都转换为0，同时这个方法会将小的数作为开始位置，将大的数作为结束位置。
		```javascript
		var t = "hello world";
		console.log(t.slice(-3))         // "rld"
		console.log(t.substring(-3))     // "hello world"
		console.log(t.substr(-3))        // "rld"
		console.log(t.slice(3,-4))       // "lo w"
		console.log(t.substring(3,-4))   // "hel"
		console.log(t.substr(3,-4))      // ""
		```
	4) 字符串位置方法
		+ indexOf()、 lastIndexOf()
		+ 都可以接收可选的第二个参数，表示从字符串中的哪个位置开始搜索
	5) trim() 、trimLeft() 、trimRight()
	6) 字符串大小写转换方法
	 	+ toLowerCase()
	 	+ toUpperCase()
	7) 字符串的模式匹配方法
		+ match()
		+ search()
		+ split()
		+ replace() : 如果第二个参数是字符串，那么还可以使用一些特殊的字符序列，将正则表达式操作得到的值插入到结果字符串中。

		|      字符序列 | 替换文本 |
		|---------|------------------|
		|				$$      | $ |
		|				$&      | 匹配整个模式的子字符串。与RegExp.lastMatch的值相同 |
		| 			$'      | 匹配的子字符串之前的子字符串。与RegExp.leftContext的值相同 |
		| 			$`      | 匹配的子字符串之后的子字符串。与RegExp.rightContext的值相同 |
		| 			$n      | 匹配第n个捕获组的子字符串，其中n等于0~9。如`$1`是匹配第一个捕获组的子字符串。|
		| 			$nn     | 匹配第nn个捕获组的子字符串，其中nn等于01~99。如$01是匹配第一个捕获组的子字符串。|

		```javascript
		var text = "cat, bat, sat, fat";
		r = text.replace(/(.at)/g,"word ($1)");
		console.log(r)   // word (cat), word (bat), word (sat), word (fat)
		```
### 单体内置对象
1. Global对象
	1) 不属于任何其他对象的属性和方法，最终都是global对象的属性和方法，如:
	isNaN()、isFinite()、parseInt()
	2) encodeURI()、encodeURIComponent()
		+ `encodeURI()`主要用于整个URI，而`encodeURIComponent()`主要用于对URI的某一段进行编码。
		+ 它们的主要区别在于，encodeURI()不会对本身属于URI的特殊字符进行编码，例如冒号、正斜杠、问号、井号；而encodeURIComponent()则会对它发现的`任何非标准字符`进行编码。
	3) decodeURI()、decodeURIComponent()
	4) eval()方法
		+ eval()方法就像是一个完整的ECMAScript解析器，它只接受一个参数，即要执行的字符串。
		+ 通过eval()执行的代码被认为是包含该次调用的执行环境的一部分，因此被执行的代码具有与该执行环境相同的作用域链。这意味着通过eval()的执行的代码可以引用在包含环境中定义的变量。
		+ 严格模式下，在外部不能访问eval()中创建的任何变量或函数。
		+ 能够解释代码字符串的能力非常强大，但也非常危险，因此在使用eval()时必须极为谨慎，特别是用它执行用户输入数据的情况下。否则，可能会有恶意用户输入威胁你的站点或应用程序安全的代码(即所谓的代码注入)。
2. Math对象
	+ Math.min()、Math.max()
	+ Math.floor()、Math.ceil()、Math.round()
	+ Math.random()
		<br>Math.random() 的取值范围 [0,1)
		1) 包括下限数字(lower)不包括上限数字(upper)
			```javascript
			function random(lower, upper) {
			  return Math.floor(Math.random() * (upper - lower)) + lower;
			}
			```
		2) 包括下限数字(lower)也包括上限数字(upper)
			```javascript
			function random(lower, upper) {
			  return Math.floor(Math.random() * (upper - lower +1)) + lower;
			}
			```
# 面向对象的程序设计
### 理解对象
1. 数据属性
	1) `[[Configurable]]`: 表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。(对象上定义，默认true)
	2) `[[Enumerable]]`: 表示能否通过for-in 循环返回属性，对象上定义，默认true。
	3) `[[Writable]]`: 表示能否修改属性的值，对象上定义，默认true。
	4) `[[Value]]`: 包含这个属性的数据值，默认值 undefined。
	5) `Object.defineProperty()`: 修改属性默认的特性，`三个参数`: 属性所在的对象、属性的名字和一个描述符对象。其中，描述符对象的属性必须是: configurable、enumerable、writable、value。可以多次调用Object.defineProperty()方法修改同一个属性，但在把configurable特性设置为false之后就会有限制了。
2. 访问器属性
	+ 访问器属性不包含数据值;它们包含一对getter和setter函数。
	+ 访问器属性4个特性
		1) `[[Configurable]]`: 表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。(对象上定义,默认值为true)
		2) `[[Enumerable]]`: 表示能否通过for-in 循环返回属性。(对象上定义，默认true)
		3) `[[Get]]`: 读取属性时调用的函数，默认值undefined。
		4) `[[Set]]`: 写入属性时调用的函数，默认值undefined。
	+ 访问器属性不能直接定义，必须使用Object.defineProperty()来定义。
	+ `Object.defineProperties()`: 通过描述符一次定义多个属性。这个方法接收`两个对象参数`: 第一个对象是要添加和修改其属性的对象，第二个对象的属性与第一个对象中要添加或修改的属性一一对应。
3. 读取属性的特性
	+ Object.getOwnPropertyDescriptor(): 只能用于实例属性，要取得原型属性的描述符，必须直接在原型对象上调用Object.getOwnPropertyDescriptor()
### 创建对象
1. 工厂模式
	<br>工厂模式是软件工程领域一种广为人知的设计模式，这种模式抽象了创建具体对象的过程。
 	```javascript
	function createPerson(name) {
	  var o = new Object()
	  o.name = name;
	  o.sayName = function() {
	    console.log(this.name)
	  }
	  return o
	}
	var person1 = createPerson('Hi')
	var person2 = createPerson('Ha')
	```
	<br>工厂模式虽然解决了创建多个相似对象的问题，但却没有解决对象识别的问题(即怎样知道一个对象的类型)。
2. 构造函数模式
	```javascript
	function Person(name) {
	  this.name = name;
	  this.sayName = function() {
	    console.log(this.name)
	  }
	}
	var person1 = new Person('Hi')
	var person2 = new Person('Ha')
	```
	1) 构造函数模式与工厂模式，创建对象的不同之处
		+ 没有显式地创建对象
		+ 直接将属性和方法赋给了this对象
		+ 没有return语句
	2) new操作符，调用构造函数的步骤:
		+ 创建一个新对象
		+ 将构造函数的作用域赋给新对象(因此this就指向了这个新对象)
		+ 执行构造函数中的代码(为这个新对象添加属性)
		+ 返回新对象
		```javascript
		function New(f) {
		  return function() {
		    var o = {
		      "__proto__": f.prototype
		    };
		    f.apply(o, arguments);
		    return o;
		  }
		}
		```
	<br>创建自定义的构造函数意味着将来可以将它的实例标识为一种特定的类型；而这正是构造函数模式胜过工厂模式的地方。
	<br>构造函数模式主要问题，在于每个方法都要在每个实例上重新创建一遍。不同实例上的同名函数是不相等的。
	```javascript
	console.log(person1.sayName == person2.sayName)   // false
	```
3. 原型模式
	1) 创建
	  ```javascript
	  function Person(){}
	  Person.prototype.name = 'Ha'
	  Person.prototype.sayName = function(){
	    console.log(this.name)
	  }
	  var person1 = new Person()
	  person1.sayName()
	  var person2 = new Person()
	  person2.sayName()
	  console.log(person1.sayName == person2.sayName)   // true
	  console.log(Person.prototype.constructor == Person)   // true
	  ```
	2) 理解原型对象
		<br>只要创建了一个新`函数`，就会根据一组特定的规则为该函数创建一个`prototype`属性，这个属性指向函数的`原型对象`。在默认情况下，所有原型对象都会自动获得一个`constructor`属性，这个属性包含一个指向prototype属性所在函数的指针
		<br>当调用构造函数创建一个新`实例`后，该实例的内部将包含一个指针(内部属性)，指向构造函数的原型对象。ECMA-262第5版本管这个指针叫`[[Prototype]]`。Firefox，safari和Chrome在每个对象上都支持一个属性`__proto__`。
		+ instanceof: 确定原型和实例的关系<br>
    	`person1 instanceof Person`
		+ isPrototypeOf(): 确定原型和实例的关系<br>
    	`Person.prototype.isPrototypeOf(person1)`
		+ Object.getPrototypeOf(): 返回`[[Prototype]]`的值<br>
    	`Object.getPrototypeOf(person1) == Person.prototype`
		+ hasOwnProperty(): 检测一个属性是在于实例中，还是存在于原型中
	3) 原型与in操作符
		+ 单独使用时，`in`操作符会在通过对象能够访问给定属性时返回true, 无论该属性存在于实例中还是原型中。
		+ 同时使用hasOwnProperty()方法和in操作符，可以确定该属性到底是存在于对象中，还是存在于原型中
			```javascript
			function hasPrototypeProperty(object, name){
			  return !object.hasOwnProperty(name) && (name in object);
			}
			```
		+ 在使用for-in循环时，返回的是所有能够通过对象访问的、可枚举的属性(实例属性 + 原型属性)
		+ Object.keys(): 获得对象上所有可枚举的实例属性
		+ Object.getOwnPropertyNames(): 获得所有实例属性，无论是否可枚举。
	4) 更简单的原型语法
		```javascript
		function Person(){}
		Person.prototype = {
		  name: 'Ha',
		  age: 29,
		  sayName: function(){}
		}
		/*
		这种方式重写了默认的prototype对象，constructor属性不再指向Person
		*/
		```
		```javascript
		// 重设构造函数
		Object.defineProperty(Person.prototype, "constructor", {
		  eumerable:false,
		  value: Person
		})
		```
	5) 原型的动态性
		<br>尽管可以随时为原型添加属性和方法，并且修改能够立即在所有实例中反映出来，但如果是重写整个原型对象，那么情况就一样了。我们知道，调用构造函数时会为实例添加一个指向最初原型的[[Prototype]]指针，而把原型修改为另外一个对象就等于切断了构造函数与最初原型之间的联系。请记住，实例中的指针仅指向原型，而不指向构造函数。
	6) 原型对象的问题
		+ 省略了为构造函数传递初始化参数这一环节，结果所有实例在默认情况下都将取得相同的属性值。
		+ 原型模式的最大问题是由其共享的本性所导致的。原型中所有属性是被很多实例共享的，这种共享对于函数非常合适，对于那些包含基本值的属性倒也说得过去，毕竟通过在实例上添加一个同名属性，可以隐藏原型中的对应属性，然而，对于包含引用类型值的属性来说，问题就比较突出了。
		```javascript
		function Person() {}
		Person.prototype = {
		  constructor: Person,
		  name: 'Ha',
		  friends: ["Shelby", "Court"],
		  sayName: function() {
		    console.log(this.name)
		  }
		}
		var person1 = new Person()
		var person2 = new Person()
		person1.friends.push("Van")
		console.log(person1.friends) // "Shelby,Court,Van"
		console.log(person2.friends) // "Shelby,Court,Van"
		console.log(person1.friends === person2.friends) // true
		```
4. 组合使用构造函数模式和原型模式
	<br>构造函数模式用于定义实例属性，而原型模式用于定义方法和共享的属性，结果，每个实例都会有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存。另外，这种模式还支持向构造函数传递参数。
	```javascript
	function Person(name) {
	  this.name = name;
	  this.friends = ["Shelby", "Court"]
	}
	Person.prototype = {
	  constructor: Person,
	  sayName: function() {
	    console.log(this.name)
	  }
	}
	var person1 = new Person('Ha');
	var person2 = new Person('Xi');
	person1.friends.push("Van")
	console.log(person1.friends) //"Shelby,Court,Van"
	console.log(person2.friends) //"Shelby,Court"
	console.log(person1.friends === person2.friends) //false
	console.log(person1.sayName === person2.sayName) // true
	```
	<br>这种构造函数与原型混成的模式，是目前在ECMAScript中使用最广泛、认同度最高的一种创建自定义类型的方法。可以说，这是用来定义引用类型的一种默认模式。
5. 动态原型模式
	<br>动态原型模式，把所有信息都封闭在了构造函数中，而通过在构造函数中初始化原型(仅在必要的情况下)，又保持了同时使用构造函数和原型的优点。换句话说，可以通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型。
	```javascript
	function Person(name) {
	  this.name = name;
	  if (typeof this.sayName != "function") {
	    Person.prototype.sayName = function() {
	      console.log(this.name)
	    }
	  }
	}
	var person1 = new Person("Ha")
	person1.sayName() // Ha
	```
	<br>使用动态原型模式时，不能使用对象字面量重写原型。如果在已经创建了实例的情况下重写原型，会切断现有实例与新原型之间的联系。
6. 寄生构造函数模式
	<br>这种模式的基本思想是创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回新创建的对象；
	```javascript
	function Person(name) {
	  var o = new Object()
	  o.name = name;
	  o.sayName = function() {
	    console.log(this.name)
	  }
	  return o
	}
	var person1 = new Person('Ha')
	person1.sayName() // 'Ha'
	```
	<br>除了使用new操作符并把使用的包装函数叫做构造函数之外，这个模式跟工厂模式其实是一模一样的。构造函数在不返回值的情况下，默认会返回新对象实例，而通过在构造函数的末尾添加一个return语句，可以重写调用构造函数时返回的值。
	<br>这个模式可以在特殊的情况下用来为对象创建构造函数。如创建一个具有额外方法的特殊数组，由于不能直接修改Array构造函数，因此可以使用这个模式
	<br>关于寄生构造函数模式，返回的对象与构造函数或者与构造函数的原型属性之间没有关系；不能依赖instanceof操作符来确定对象类型
7. 稳妥构造函数模式
	<br>稳妥对象最适合在一些安全的环境中(这些环境中会禁止使用this和new)，或者在防止数据被其他应用程序。
	<br>稳妥构造函数遵循与寄生构造函数类似的模式，但有两点不同: 一是新创建对象的实例不引用this，二是不使用new操作符调用构造函数
	```javascript
	function Person(name) {
	  var o = new Object()
	  o.name = name;
	  o.sayName = function() {
	    console.log(name)
	  }
	  return o
	}
	var person1 = Person('Ha')
	person1.sayName() // 'Ha'
	```
###  继承
许多OO语言都支持两种继承方式: 接口继承和实现继承。接口继承只继承方法签名，而实现继承则继承实际的方法。
<br>ECMAScript中只支持实现继承，而且其实现继承主要是依靠原型链来实现的。其基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。
1. 原型链
	1) 基本概念
		<br>构造函数、原型、实例的关系: 每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。那么，假如让原型对象等于另一个类型的实例，此时的原型对象将包含一个指向另一个原型的指针，相应对，另一个原型中也包含着一个指向另一个构造函数的指针。假如另一个原型又是另一个类型的实例，那么上述关系依然成立，如此层层递进，就构造了实例与原型的链条。这就是所谓的原型链。
	2) 谨慎地定义方法
		+ 子类型有时候需要重写超类型中的某个方法，或者需要添加超类型中不存在的某个方法，但不管怎样，给原型添加方法的代码一定要放在替换原型的语句之后
		+ 通过原型链接实现继承时，不能使用对象字面量创建原型方法，因为这样做会重写原型链。
	3) 原型链的问题
	 <br>最主要的问题来自包含引用类型值的原型，在通过原型来实现继承时，原型实际上会变成另一个类型的实例，于是，原先的实例属性也就顺理成章地变成了现在的原型属性。
		```javascript
		function SuperType(){
		  this.colors = ["red","blue","green"]
		}
		function SubType(){}
		SubType.prototype = new SuperType()
		var instance1 = new SubType()
		instance1.colors.push("black");
		console.log(instance1.colors)    // "red,blue,green,black"
		var instance2 = new SubType()
		console.log(instance2.colors)    //　"red,blue,green,black"
		```
2. 借用构造函数 (也叫伪造对象或者经典继承)
	<br>解决原型中包含引用类型值所带来问题, 借用构造函数的基本思想: 在子类型构造函数的内部调用超类型构造函数。函数只是在特定环境中执行代码的对象，因为通过使用apply()和call()方法也可以在(将来)新创建的对象上执行构造函数。
	```javascript
	function SuperType(){
	  this.colors = ["red","blue","green"]
	}
	function SubType(){
	  SuperType.call(this)
	}
	var instance1 = new SubType()
	instance1.colors.push("black")
	console.log(instance1.colors)   // "red,blue,green,black"
	var instance2 = new SubType()
	console.log(instance2.colors)   //"red,blue,green"
	```
	1) 传递参数
	<br>相对于原型链而言，借用构造函数有一个很大的优势，即可以在子类型构造函数中向超类型构造函数传递参数。
	2) 借用构造函数的问题
	<br>如果仅仅是借用构造函数，那么也将无法避免构造函数模式存在的问题——方法都在构造函数中定义，因为函数复用就无从谈起了，而且在超类型的原型中定义的方法，对子类型而言也是不可见的，结果所有类型都只能使用构造函数模式。考虑到这些问题，借用构造函数的技术也是很少单独使用的。
3. 组合继承 (也叫伪经典继承)
	<br>指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式，其背后的思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。这样，既通过在原型上定义方法实现了函数复用，又能够保证每个实例都有它自己的属性
	```javascript
	function SuperType(name) {
	  this.name = name;
	  this.colors = ["red", "blue", "green"]
	}
	SuperType.prototype.sayName = function() {
	  console.log(this.name)
	}

	function SubType(name, age) {
	  SuperType.call(this, name) // 第二次调用 SuperType()
	  this.age = age
	}
	SubType.prototype = new SuperType() // 第一次调用  SuperType()
	SubType.prototype.sayAge = function() {
	  console.log(this.age)
	}
	var instance1 = new SubType("Ha", 29)
	instance1.colors.push("black")
	console.log(instance1.colors) //"red,blue,green,black"
	instance1.sayName() //"Ha"
	instance1.sayAge() //29
	var instance2 = new SubType("Xi", 27)
	console.log(instance2.colors) //"red,blue,green"
	console.log(instance2.sayName) //"Xi"
	console.log(instance2.sayAge) //27
	/*
	 组合继承最大的问题就是无论什么情况下，都会调用两次超类型构造函数:一次是在创建子类型原型的时候;另一次是在子类型构造函数内部。
	 没错，子类型最终会包含超类型对象的全部实例属性，但我们不得不在调用子类型构造函数时重写这些属性。
	 */
	 ```
4. 原型式继承
	```javascript
	function object(o){
	  function F(){}
	  F.prototype = o;
	  return new F();
 	}
	/*
	函数内部，先创建了一个临时性的构造函数，然后将传入的对象作为 这个构造函数的原型，最后返回了这个临时类型的一个新实例。从本质上讲，object()对传入其中的对象进行了一次浅复制。
	*/
	```
	<br>ECMAScript 5通过Object.create()方法规范化了原型式继承。这个方法接收两个参数: 一个用作新对象原型的对象和(可选的)一个为新对象定义额外属性的对象。在传入一个参数的情况下，Object.create()与object()方法的行为相同。
5. 寄生式继承
	<br>寄生式继承的思路与寄生构造函数和工厂模式类似，即创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后返回对象
	```javascript
	function createAnother(original){
	  var clone = object(original);
	  clone.sayHi = function(){
	    console.log('hi');
	  };
	  return clone;
	}
	```
	+ 使用寄生式继承来为对象添加函数，会由于不能做到函数复用而降低效率
6. 寄生组合式继承
	<br>解决组合继承两次调用超类型构造函数的问题。
	<br>寄生组合式继承，通过借用构造函数来继承属性，通过原型链接的混成形式来继承方法。其背后的基本思路是: 不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非就是超类型原型的一个副本而已。本质上，就是使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型。基本模式如下:
	```javascript
	function inheritPrototype(subType, superType){
	  var prototype = object(superType.prototype);
	  prototype.constructor = subType;
	  subType.prototype = prototype;
	}
	/*
	在函数内部，第一步是创建超类型原型的一个副本，第二步是为创建的副本添加constructor属性，从而弥补因重写原型而失去的默认的constructor属性。最后一步，将新创建的对象(即副本)赋值给子类型的原型。
	*/
	function SuperType(name){
	  this.name = name;
	  thsis.colors = ["red","blue","green"]
	}
	SuperType.prototype.sayName = function(){
	  console.log(this.name)
	}
	function SubType(name, age){
	  SuperType.call(this,name)
	  this.age = age
	}
	inheritPrototype(SubType, SuperType)
	SubType.prototype.sayAge = function(){
	  console.log(this.age)
	}
	```
_____
# 函数表达式
### 递归
```javascript
function factorial(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * arguments.callee(num - 1);
  }
}
//在严格模式下，不能通过脚本访问arguments.callee
var factorial = (function f(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * f(num - 1)
  }
});
```
### 闭包
1. 闭包
	<br>是指有权访问另一个函数作用域中的变量的函数。创建闭包的常见方式，就是在一个函数内部创建另一个函数。
	```javascript
	function createFunction() {
	  var result = new Array()
	  for (var i = 0; i < 10; i++) {
	    result[i] = function() {
	      return i;
	    }
	  }
	  return result;
	} // 每个函数返回都是 10
	//添加闭包
	function createFunction() {
	  var result = new Array()
	  for (var i = 0; i < 10; i++) {
	    result[i] = function(num) {
	      return function() {
	        return num
	      }
	    }(i)
	  }
	  return result
	}
	```
2. this对象
	1) 在全局函数中，this等于window(非严格模式)， 或者undefined(严格模式)
	2) 当函数作为某个对象的方法调用时，this等于那个对象，不过，匿名函数的执行环境具有全局性，因此其this对象通过指向window。
	3) 作为构造函数调用，this 指代new 的实例对象
	4) 通过apply() 或 call()改变函数执行环境的情况下，this就会指向其他对象。
	5) Node模块或ES6模块中，this返回的是当前模块
	```javascript
	var name = "The Window"
	var object = {
	  name: "My Object",
	  getNameFunc: function() {
	    return function() {
	      return this.name
	    }
	  }
	}
	console.log(object.getNameFunc()())
	// "The Window"(非严格模式)  undefined(严格模式)
	/*匿名函数的执行环境具有全局性，因此其this通过指向window*/
	var name = "The Winodw"
	var object = {
	  name: "My Object",
	  getName: function() {
	    return this.name;
	  }
	}
	object.getName() // "My Object"
	(object.getName)() //"My Object"
	(object.getName = object.getName)() // "The Window"，在非严格格式下
	```
3. 小结
	1) 递归函数应该始终使用arguments.callee来递归地调用自身，不要使用匿名函数(函数名可能会发生变化)
	2) 当在函数内部定义了其他函数时，就创建了闭包。闭包有权访问包含函数内部的所有变量，原理如下:
		+ 在后台执行环境中，闭包的作用域链包含着它自己的作用域、包含函数的作用域和全局作用域。
		+ 通常，函数的作用域及其所有变量都会在函数执行结束后被销毁。但是，当函数返回了一个闭包时，这个函数的作用域将会一直在内存中保存到闭包不存在为止。
	3) 使用闭包可以在JavaScript中模仿块级作用域，要点如下:
		+ 创建并立即调用一个函数，这样既可以执行其中的代码，又不会在内存中留下对该函数的引用。
		+ 结果就是函数内部的所有变量都会被立即销毁——除非将某些变量赋值给了包含作用域(即外部作用域)中的变量。
	4) 闭包还可以用于在对象中创建私有变量，相关概念和要点如下:
		+ 即使JavaScript中没有正式的私有对象属性的概念，但可以使用闭包来实现公有方法，而通过公有方法可以访问在包含作用域中定义的变量。
		+ 有权访问私有变量的公有方法叫做特权方法。
		+ 可以使用构造函数模式、原型模式来实现自定义类型的特权方法，也可以使用模块模式、增强的模块模式来实现单例的特权方法。
	5) JavaScript中的函数表达式和闭包都是极其有用的特性，利用它们可以实现很多功能。不过，因为创建闭包必须维护额外的作用域，所以过度使用它们可能会占用大量内存。
________________
# DOM
### 节点
1. 节点关系
	+ firstChild、 lastChild
	+ parentNode、 childNode
	+ nextSibling、 previousSibling
	+ hasChildNodes(): 在节点包含一个或多个子节点的情况下返回true
2. 插入节点
	+ appendChild(): 用于向childNodes列表的末尾添加一个节点。
	+ insertBefore: 在childNodes列表中特定的位置添加节点。接收两个参数: 要插入的节点和作为参照的节点
3. 替换节点
	+ replaceChild()
4. 删除节点
	+ removeChild()
5. 复制节点
	+ cloneNode():  用于创建调用这个方法的节点的一个完全相同的副本。cloneNode()方法接受一个布尔值参数，表示是否执行深复制。在参数为true的情况下，执行`深复制`，也就是复制节点及其整个子节点树; 在参数为false的情况下，执行`浅复制`，即只复制节点本身。
### Documents 类型
1. documentsElement、firstChild 和 childNodes[0] 都指向\<html\>
2. document.domain
	<br>由于跨域安全限制，来自不同子域的页面无法通过JavaScript。而通过将每个页面的documents.domain设置为相同的值，这些就可以互相访问对方包含的JavaScript对象了。
3. 查找元素
	1) 通过id: getElementById()
	2) 通过标签名: getElementsByTagName(), 返回包含零个或多个元素的NodeList。在HTML文档中，会返回一个HTMLCollection对象，如img
	3) 通过name属性: getElementsByName()
	4) 通过class: getElementsByClassName()
	5) 特殊集合
		+ documents.anchors: 包含文档中所有带name特性的\<a\>元素
		+ documents.forms:  包含文档中所有的\<form\>元素
		+ documents.images: 包含文档中所有的\<img\>元素
		+ documents.links: 包含文档中所有带href特性的a元素
### Element 类型
1. 取得特性: getAttribute()
2. 设置特性: setAttribute()
3. 删除特性: removeAttribute()
4. 创建元素: document.createElements()
5. 创建文本节点: document.createTextNode()

<br>DOM操作往往是JavaScript程序中开销最大的部分，而因访问NodeList导致的问题为最多。NodeList对象都是“动态的”,这就意味着每次访问NodeList对象，都会运行一次查询。有鉴于此，最好的办法就是尽量减少DOM操作。

### 选择符API
+ querySelector()
+ querySelectorAll()
### DOM2 级、 DOM3级
1. 框架:
	+ iframe.contentDocument   // IE8无效
	+ iframe.contentWindow.document;   // contentWindow 所有浏览器都支持
2. 样式
	1) style对象的一些属性和方法
		+ cssText: 通过它能访问style特性中的css代码
		+ length: 应用给元素的CSS属性的数量
		+ getPropertyValue(propertyName): 返回给定的属性的字符串值
		+ item(index): 返回给定位置的css属性的名称
		+ setProperty(propertyName,value,priority): 将给定的属性设置为相应的值
		+ removeProperty(propertyName): 从样式中删除给定属性
	2) 计算样式
		+ document.defaultView.getComputedStyle():  要取得计算样式的元素和一个伪元素字符串,若不需要伪元素，第二个参数可以是null. // IE8不支持
		+ ele.currentStyle // IE8
	3) 操作样式表
		+	document.styleSheets
	4) 元素大小
		+ 偏移量
			+ offsetHeight: 元素在垂直方向上占用的空间大小，包括元素高度、(可见的)水平滚动条的高度、上下边框高度。
			+ offsetWidth: 元素在水平方向上占用的空间大小，包括元素的宽度、(可见的)垂直滚动条的宽度，左右边框宽度。
			+ offsetLeft: 元素的左外边框至包含元素的左内边框之间的像素距离。
			+ offsetTop: 元素的上外边框至包含元素的上内边框之间的像素距离。
		+ 客户区大小
			+ clientWidth: 元素内容区宽度、左右内边距宽度
			+ clientHeight: 元素内容区高度、上下内边距高度
		+ 滚动大小
			+ scrollHeight: 在没有滚动条的情况下，元素内容的总高度
			+ scrollWidth: 在没有滚动条的情况下，元素内容的总宽度
			+ scrollLeft: 被隐藏在内容区域左侧的像素数
			+ scrollTop: 被隐藏在内容区域的上方的像素数
		+ 确定元素的大小
			+ getBoundingClientRect(): 包含4个属性，left、top、right、bottom
__________________
# 事件
### 事件流
 DOM级事件流: 规定的事件流包括三个阶段: 事件捕获阶段、处于目标阶段和事件冒泡阶段。`IE8`及以下版本不支持。
### 事件处理程序
+ 响应某个事件的函数就叫做事件处理程序(或事件侦听器)。事件处理程序的名字以"on"开头。<br>
	```javascript
	btn.onclick= function(){}   //绑定
	btn.onclick = null          //删除
	```
1. DOM2级事件处理
	1) removeEventListener()中的事件处理程序函数必须与传入addEventListener()
	中的相同
		```javascript
		btn.addEventListener('click',handler,false)
		btn.removeEventListener('click',handler,false)
		```
	2) 大多数情况下，都是将事件处理程序添加到事件流的冒泡阶段，这样可以最大限度地兼容各种浏览器。最好只在需要在事件到达目标之前截获它的时候将事件处理程序添加到捕获阶段。
2. IE事件处理程序
	<br>由于IE8及更早版本只支持事件冒泡，所以能过attachEvent()添加的事件处理程序都会被添加到冒泡阶段。添加多个attachEvent()时，以反序触发相应的事件。
	```javascript
	btn.attachEvent('onclick',handler)
	btn.detachEvent('onclick',handler)
	```
### 事件对象
1. DOM中的事件对象
<br>在事件处理程序内部，对象this始终等于currentTarget的值，而target则只包含事件的实际目标。如果直接将事件处理程序指定给了目标元素，则this、currentTarget 和 target 包含相同的值。
	+ preventDefault(): 阻止特定事件的默认行为
	+ stopPropagation(): 用于立即停止事件在DOM层次中的传播
2. IE 中的事件对象
	+ 在使用DOM0级方法添加事件处理程序时，event对象作为window对象的一个属性存在。
	+ 如果事件处理程序是使用attachEvent()添加的，那么就会有一个event对象作为参数被传入到事件处理程序函数中。
	+ 因为事件处理程序的作用域是根据指定它的方式来确定的，所以不能认为this会始终等于事件目标，最好还是使用event.srcElement
	```javascript
	btn.onclick = function(){
	  console.log(window.event.scrElement === this)   //true
	}
	btn.attachEvent('onclick',function(event){
	  console.log(event.srcElement === this)    // false
	})
	```
	+ returnValue, 值为false ，可以阻止默认行为
	+ cancelBubble， 值为true, 可以停止事件冒泡。由于IE不支持事件捕获，所以只能取消事件冒泡，但stopPropagation()可以同时取消事件捕获和冒泡。
### 跨浏览器的事件处理
```javascript
var EventUtil = {
  addHandler: function(ele, type, handler) {
    if (ele.addEventListener) {
      ele.addEventListener(type, handler, false);
    } else if (ele.attachEvent) {
      ele.attachEvent('on' + type, handler);
    } else {
      ele['on' + type] = handler;
    }
  },
  removeHandler: function(ele, type, handler) {
    if (ele.removeEventListener) {
      ele.removeEventListener(type, handler, false);
    } else if (ele.detachEvent) {
      ele.detachEvent('on' + type, handler);
    } else {
      ele['on' + type] = null;
    }
  },
  getEvent: function(event) {
    return event ? event : window.event;
  },
  getTarget: function(event) {
    return event.target || event.srcElement;
  },
  preventDefault: function(event) {
    if (event.preventDefault) {
      event.preventDefault();
    } else {
      event.returnValue = false;
    }
  },
  stopPropagation: function(event) {
    if (event.stopPropagation) {
      event.stopPropagation();
    } else {
      event.cancelBubble = true;
    }
  }
};
```
### 事件类型
1. 焦点事件
 + focus 、 blur : 不冒泡， 也可以在捕获阶段侦听到它们
 + focusin、focusout : 冒泡
2. textInput事件
	<br>当用户在可编辑区域中输入字符时，触发这个事件
### 内存和性能
1. 在JavaScript中，添加到页面上的事件处理程序数量将直接关系到页面的整体运行性能。导致这一问题的原因是多方面的。
	1) 每个函数都是对象，都会占用内存; 内存中的对象越多，性能就越差
	2) 必须事先指定所有事件处理程序而导致的DOM访问次数，会延迟整个页面的交互就绪时间
2. 事件委托
	1) 对“事件处理程序过多”问题的解决方案就是事件委托。事件委托利用了事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。
	2) 最适合采用事件委托技术的事件包括click、mousedown、mouseup、keydown、keyup、keypress。
3. 一般来说，最好的做法是在页面卸载之前，先通过onunload事件处理程序移除所有事件处理程序。在此，事件委托技术再次表现出它的优势——需要跟踪的事件处理程序越少，移除它们就越容易。对这种类似撤销的操作，我们可以把它想象成: 只要是通过onload事件处理程序添加的东西，最后都要通过onunload事件处理程序将它们移除
### 模拟事件
+ createEvent()
______
# ajax
1. 默认情况下，在发送XHR请求的同时，还会发送下列头部信息:
	1) Accept: 浏览器能够处理的内容类型
	2) Accept-Charset: 浏览器能够显示的宏字符集
	3) Accept-Encoding: 浏览器能够处理的压缩编码
	4) Accept-Language: 浏览器当前设置的语言
	5) Connection: 浏览器与服务器之间连接的类型
	6) Cookie: 当前页面设置的任何Cookie
	7) Host: 发出请求的页面所在的域
	8) Referer: 发出请求的页面的URI
	9) User-Agent: 浏览器的用户代理字符串
2. setRequestHeader()方法可以设置自定义的请求头部信息。这个方法接收两个参数: 头部字段的名称和头部字段的值。要成功发送请求头部信息，必须在调用open()方法之后且调用send()方法之前调用setRequestHeader()
3. FormData
	<br>使用FormData的方便这处体现在不必明确地在XHR对象上设置请求头部。XHR对象能够识别传入的数据类型是FormData的实例，并配置适当的头部信息。
### 跨源资源共享
1) 通过XHR实现Ajax通信的一个主要限制，来源于跨域安全策略。默认情况下，XHR对象只能访问与包含它的页面位于同一个域中的资源。
2) CORS(Cross-Origin Resource Sharing, 跨源资源共享),背后的基本思想，就是使用自定义的HTTP头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败。 服务器如果认为这个请求可以接受，就在Access-Control-Allow-Origin头部中回发相同的源信息(如果是公共资源，可以是`'*'`)
### 其他跨域技术
1. 图像Ping
	+ 一个网页可以从任何网页中加载图像，不用担心跨域不跨域，这也是在线广告跟踪浏览量的主要方式。
	+ 动态创建图像经常用于图像Ping。图像Ping是与服务器进行简单、单身的跨域通信的一种方式。请求的数据是通过查询字符串形式发送的，而响应可以是任意内容，但通过是像素图或204响应。通过图像Ping, 浏览器得不到任何具体的数据，但通过侦听load和error事件，它能知道响应是什么时候接收的。
	+ 图像Ping最常用于跟踪用户点击页面或动态广告曝光次数。图像Ping有两个主要的缺点，一是只能发送GET请求，二是无法访问服务器的响应文本。因此，图像Ping只能用于浏览器与服务器间的单向通信。
2. JSONP
	+ JSONP由两部分组成: 回调函数和数据。回调是当响应来时应该在页面中调用的函数。回调函数的名字一般是在请求中指定的。而数据就是传入回调函数中的JSON数据。
	+ JOSNP与图像Ping相比，它的优点在于能够直接访问响应文本，支持在浏览器与服务器之间的双向通信。不过，JSONP也有两点不足。
		1) 首先，JSONP是从其他域中加载代码执行。如果其他域不安全，很可能会在响应中夹带一些恶意代码，而此时除了完全放弃JSONP调用之外，没有办法追究。因为在使用不是你自己运维的Web服务时，一定得保证它安全可靠。
		2) 其次，要确定JSONP请求是否失败并不容易。

# HTML5脚本编程
### 跨文档消息传递 (cross-document-messaging) XDM
+ 跨文档消息传递指的是来自不同域的页面间传递消息。
+ postMessage()接收两个参数: 一条消息和一个表示消息接收方来自哪个域的字符串。第二个参数对保障安全通信非常重要，可以防止浏览器把消息发送到不安全的地方。第二个参数如果是`'*'`, 则表示可以把消息发送给来自任何域的文档。
+ 接收到XDM消息时，会触发window对象的message事件。这个事件是以异步形式触发的。传递给onmessage处理程序的事件对象包含以下三方面的重要消息。
	+ data: 作为postMessage()第一个参数传入的字符串数据。
	+ origin: 发送消息的文档所在的域
	+ source: 发送消息的文档的window对象的代理。这个代理对象主要用于在发送上一条消息的窗口中调用postMessage()方法。
### 拖拽事件
1. 拖动元素，触发事件
	1) dragstart
	2) drag
	3) dragend
2. 放置目标上，触发的事件
	1) dragenter
	2) dragover
	3) dragleave 或 drop
3. dataTransfer对象
	1) 两个主要方法: setData() 和 getData()
	2) dropEffect
		+ none: 不能把拖动的元素放在这里。
		+ move: 应该把拖动的元素移动在放置目标
		+ copy: 应该把拖动的元素复制到放置目标
		+ link: 表示放置目标会打开拖动的元素(拖动的元素必须是一个链接，有URL)
	<br>**必须在ondragenter事件处理程序中针对放置目标来设置。只有搭配effectAllowed属性才有用。**
	3) effectAllowed
		+ uninitialized: 没有给被拖动的元素设置任何放置行为
		+ none: 被拖动的元素不能有任何行为
		+ copy: 只允许值为"copy"的dropEffect
		+ all : 允许任意dropEffect
	<br>**必须在ondragstart事件处理程序中设置effectAllowed属性**
___________________
# 错误处理与调试
下面几种避免浏览器响应JavaScript错误的方法
1. 在可能发生错误的地方使用try-catch语句，这样你还有机会以适当的方式对错误给出响应，而不必没用浏览器处理错误的机制、
2. 使用window.onerror事件处理程序，这种方式可以接受try-catch不能处理的所有错误。
3. 常见的错误类型
	1) 类型转换错误
	2) 数据类型错误
	3) 通信错误
4. 把错误记录到服务器
	<br>要建立这样一种JavaScript错误记录系统，首先需要在服务器上创建一个页面(或者一个服务器入口点)用于处理错误数据。
	```javascript
	function logErrot(sev, msg){
	  var img = new Image()
	  img.src = 'log.php?sev='+encodeURIComponent(sev)+'&msg='+encodeURIComponent(msg)
	}
	```
	<br>**使用Image对象发送请求的原因**
	+ 所有浏览器都支持Image对象，包括那些不支持XMLHttpRequest对象的浏览器
	+ 可以避免跨域限制。通常都是一台服务器要负责处理多台服务器的错误，而这种情况下使用XMLHttpRequest是不行的。
	+ 在记录错误的过程中出现问题的概率比较低
_____________
# 高级技巧
### 高级函数
1. 安全的类型检测
	```javascript
	function  isArray(value){
	  return Object.prototype.toString.call(value) == '[object Array]'
	}
	function isFunction(value){
	  return Object.prototype.toString.call(value) == '[object Function]'
	}
	function isRegExp(value){
	  return Object.prototype.toString.call(value) == '[object RegExp]'
	}
	var isNativeJSON = window.JSON && Object.prototype.toString.call(JSON) == '[object JSON]'
	```
2. 作用域安全的构造函数
	```javascript
	function Polygon(sides) {
	  if (this instanceof Polygon) {
	    this.sides = sides;
	    this.getArea = function() {
	      return 0;
	    };
	  } else {
	    return new Polygon(sides)
	  }
	}

	function Rectangle(width, height) {
	  Polygon.call(this, 2);
	  this.width = width;
	  this.height = height;
	  this.getArea = function() {
	    return this.width * this.height;
	  };
	}
	Rectangle.prototype = new Polygon(); // ***不能省略，否则不能继承Plygon
	var rect = new Rectangle(5, 10)
	console.log(rect.sides)
	```
3. 函数绑定
	```javascript
	function bind(fn, context){
	  return function(){
	    return fn.apply(context, arguments);
	  };
	}
	/*
	1) 在bind()中创建了一个闭包，闭包使用apply()调用传入的函数，并给apply()传递context对象和参数。注意这里使用arguments对象是内部函数的，而非bind()的。当调用返回的函数时，它会在给定环境中执行被传入的函数并给出所有参数。
	2) 只要是将某个函数指针以值的形式进行传递，同时该函数必须在特定环境中执行，被绑定函数的效用就突显出来了，它们主要用于事件处理程序以及setTimeout()和setInterval()。然后，被绑定函数与普通函数相比有更多的开销，它们需要更多内存，同时也因为多重函数调用稍微慢一点，所以最好只在必要时使用。
	*/
	```
4. 函数柯里化
	<br>与函数绑定紧密相关的主题是函数柯里化，它用于创建已经设置好了一个或多个参数的函数。函数柯里化的基本方法和函数绑定是一样的: 使用一个闭包返回一个函数。两者的区别在于，当函数被调用时，返回的函数还需要设置一些传入的参数。
	```javascript
	function curry(fn) {
	  var args = Array.prototype.slice.call(arguments, 1);
	  return function() {
	    var innerArgs = Array.prototype.slice.call(arguments);
	    var finalArgs = args.concat(innerArgs);
	    return fn.apply(null, finalArgs); //null， 表示不考虑执行环境
	  }
	}
	```
	<br>**加强版本bind()**
	```javascript
	function bind(fn, context) {
	  var args = Array.prototype.slice.call(arguments, 2);
	  return function() {
	    var innerArgs = Array.prototype.slice.call(arguments);
	    var finalArgs = args.concat(innerArgs);
	    return fn.apply(context, finalArgs);
	  };
	}
	```
2. 防篡改对象
	1) 不可扩展对象
		+ Object.preventExtensions(): 不能给调用的对象添加新的属性和方法，但是可以修改和删除已有的成员。
		+ Object.isExtensible(): 检测对象是否可扩展
	2) 密封的对象
		+ Object.seal(): 被密封的对象不可扩展，不能删除属性和方法，只能修改属性值。
		+ Object.isSealed(): 检测密封的对象
	3) 冻结的对象
		+ Object.freeze(): 最严格的防篡改的级别的是冻结对象，冻结的对象既不可扩展，又是密封的，而且对象属性的[Writable]特性会被设置为false.
		+ Object.isFrozen(): 检测冻结对象
___________________
# 数据存储
### Cookie
+ HTTP Cookie,通常直接叫做cookie,最初是在客户端用于存储会话信息的。该标准要求服务器对任意HTTP请求发送Set-Cookie HTTP头作为响应的一部分，其中包含会话信息。
+ cookie在性质上是绑定在特定的域名下的。当设定了一个cookie后，再给创建它的域名发送请求时，都会包含这个cookie。每个域的cookie总数是有限的。
+ 浏览器中对于cookie的尺寸也有限制。大多数浏览器都有大约4096B(加减1)的长度限制。尺寸限制影响到一个域下所有的cookie，而并非每个cookie单独限制。
+ cookie的构成
	1) 名称
	2) 值
	3) 域: cookie对于哪个域是有效的。所有向该域发送的请求中都会包含这个cookie信息。
	4) 路径
	5) 失效时间: 设置的失效日期是个以前的时间，cookie会被立刻删除。
	6) 安全标志: 指定后，cookie只有在使用SSL连接的时候才会发送到服务器。
