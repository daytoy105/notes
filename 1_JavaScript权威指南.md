# 数据类型
### 原始类型
<br> 数字、字符串、布尔值 、null 、undefined
1. null:存在该数据，但当前还不确定的值。可以通过给一个变量赋值null来清除变量的内容。（垃圾回收）
2. undefined:  声明的变量还未被初始化时，变量的默认值为undefined
	<br>不同点：
	 + `typeof null  // object `
	 + `typof undefined  // undefined`
	 + 转换成数字 :  undefined  // NaN    null  //0
	 <br>相同点：
	+ null 和 undefined 都不包含任何属性和方法
	+ 都是原始类型，隐式转换值为 null
	+ 可通过 === 判断null和undefined
3.typeof: 检测数据类型
### 数据类型转换
1) 算数计算中，任何类型都自动转为数字类型计算m
2) 在\+运算中，任何类型碰到字符串，都自动转换为字符串
	<br>\+运算: 字符串拼接
3) NaN
	+ 如果某个字符串无法转为数字，会转为NaN
	+ NaN和任何数字进行**计算**都返回NaN
	+ NaN和任何数字**做比较**都返回false
	+ 0/0
	+ `NaN == NaN ` // false
4) isNaN(x)
	+ 如果是NaN返回true; 如果不是NaN返回false
	+ 是数字返回false,如果不是数字，返回true
5) ==  和 ===
	+ == 值的比较，不比较类型，可进行类型转换
	+ === 值和类型的比较，不进行类型转换
### 内置对象
String、 Number、Boolean、 Array、 Function、 Object、 Date、 RegExp、 Math、 Error、 Global
_______
# 字符串
1. indexOf() 和 lastIndexOf()
	<br>返回关键字所在位置，如果没有找到，返回-1
2. String() 和 new String()
	+ String() 字符串类型转换
	+ new String() 创建一个包装类型
3. slice(start, end+1)  和 substring(start, end+1)  和 substr(start, count)
	+ substring() 不支持负数
	+	slice() 支持负数
	```javascript
	var str = '123456'
	console.log(str.substring(1,3), str.slice(1,3) , str.substr(1,3))  // 23  23  234
	```
	+ 对原始字符串没有任何影响
4. split()
	<br>分割为数组形式
5. replace()  和 match() 和 search()
	1) replace()  替换关键字
	2) match()
		+ i:  第一个匹配的关键字,index属性中保存了第一个匹配的关键字的位置
		+ ig: 保存所有匹配的关键字
	3) search() :同indexOf(), indexOf()不支持模式查找，search()是其模式查找版本
		<br>通过index==-1，判断是否包含关键字,返回找到的第一个元素的索引，没有返回-1
	```javascript
	var str = 'hello'
	console.log(str.match(/l/i))   //  [ 'l', index: 2, input: 'hello' ]
	console.log(str.match(/l/ig))  //  ['l','l']
	console.log(str.search(/l/i))  //2
	```
6. toLowerCase() 和 toUpperCase()
7. encodeURIComponent() 和 decodeURIComponent()
_________
# 正则表达式
### 正则表达式的定义
1. 字符类
	| 字符      | 匹配 |
	|----------|----------|
	| [...]    | 方括号内的任意字符
	| [^...]     | 不在方括号内的任意字符
	| .          | 除换行符和其他Unicode行终止行之外的任意字符
	| \w         | 任何ASCII字符组成的单词，等价于[a-zA-Z0-9]
	| \W         | 任何不是ASCII字符组成的单词，等价于[^a-zA-Z0-9]
	| \s         | 任何Unicode空白符
	| \S         | 任何非Unicode空白符的字符，注意\s和\W不同
	| \d         | 任何ASCII数字，等价于[0-9]
	| \D         | 除了ASCII数字之外的任何字符，等价于[^0-9]
	| [\b]       | 退格直接量（特例）
2. 重复
	<br>正则表达式的重复字符语法
	| 字符       | 含义
	|------------|----------|
	| {n,m}      | 匹配前一项至少n次,但不能超过m次
	| {n,}       | 匹配前一项n次或者更多次
	| {n}        | 匹配前一项n次
	| ?          | 匹配前一项0次或者1次，也就是说这一项是可选的，等价于{0,1}
	| +          | 匹配前一项1次或者多次，等价于{1,}
	| *          | 匹配前一项0次或者多次，等价于{0,}
	+ 贪婪匹配：匹配重复字符是可尽可能多地匹配，而且允许后续的正则表达式继续匹配
	+ 非贪婪匹配：尽可能少地匹配，在待匹配的字符后跟随一个问号即可：如"??"、"+?"、"*?"、"{1,5}?"
	+ 使用非贪婪的匹配模式所得到的结果可能和期望并一致。
		```javascript
		/a+b/
		/*
		当使用"aaab"作为匹配字符串时，它会匹配整个字符串。
		若为非贪婪模式/a+?b/时，用来匹配"aaab"，你期望它能匹配一个a和最后一个b，但实际上，这个模式却匹配了整个字符串，和该模式的贪婪匹配一模一样。
		这是因为正则表达式的模式匹配总是会寻找字符串中第一个可能匹配的位置。由于该匹配是从字符串的第一个字符开始的，
		因此这里不考虑它的子串中更短的匹配
		*/
		```
3. 选择、分组和引用
	1) 正则表达式的选择、分组和引用字符
		| 字符       | 含义
		|-----------|----------|
		| \|         | 选择
		| (...)      | 组合，将几个项组合为一个单元，这个单元可以通过"*"、"+"、"?"等加以修改，而且可以记住和这个组合相匹配的字符串以供此后的引用使用。
		| (?:...)    | 只组合，把项组合到一个单元，但不记忆与该 组相匹配的字符
		| \n        | 和第n个分组第一次匹配的字符相匹配，组是圆括号中的子表达式(也有可能是嵌套的),组索引是从左到右的左括号数，"?:"形式的分组不编码
	2) 选择 `|`
		<br> 选择项的尝试匹配次序是从左到右，直到发现了匹配项。如果左边选择项匹配，就忽略右边的匹配项，即便它产生更好的匹配。
	3) 组合 `()`
		+ 在正则表达式中，圆括号的另一个作用是在完整的模式中定义子模式。当一个正则表达式成功地和目标字符串相匹配时，可以从目标串中抽出和圆括号中的子模式相匹配的部分。
		+ 带圆括号的表达式的另一个用途是允许在同一正则表达式的后部引用前面的子表达式。这是通过在字符"\"后加一位或多位数字来实现的。这个数字指定了带圆括号的子表达式在正则表达式中的位置。如\1引用的是第一个带圆括号的子表达式
		+ 对正则表达式中前一个子表达式的引用，并不是指对子表达式模式的引用，而指的是与那个模式相匹配的文本的引用。这样，引用可以用于实施一条约束，即一个字符串各个单独部分包含的是完全相同的字符。eg:/(['"])[^'"]*\1/
		+ 同样，在正则表达式中不用创建带数字编码的引用，也可以对子表达式进行分组。以"(?:" 和 ")"来进行分组，这种改进的圆括号并不生成引用。
4. 指定匹配位置
	+ 像\b这样的元素不匹配某个可见的字符，它们指定匹配发生的合法位置。有时我们称这些元素为正则表达式的锚，因为它们将模式定位在搜索字符串的特定位置上
	+ 正则表达式中的锚字符
		| 字符       | 含义
		|-----------|----------|
	  | ^         | 匹配字符串的开头，在多选检索中，匹配一行的开头
	  | $         | 匹配字符串的结尾，在多选检索中，匹配一行的结尾
	 	| \b        | 匹配一个单词的边界，简言之，就是位于字符\w和\W之间的位置，或位于字符\w和字符串的开头或者结尾之间的位置
	 	| \B        | 匹配非单词边界的位置
	  | (?=p)     | 零宽正向先行断言，要求接下来的字符都与p匹配，但是不能包括匹配p的那些字符
	  | (?!p)     | 零宽负向先行断言，要求接下来的字符不与p匹配
5. 修饰符
	+ 正则表达式的修饰符，用以说明高级匹配模式的规则。
	+ 正则表达式修饰符
		| 字符     | 含义
		|----------|----------|
		| i        | 执行不区分大小写的匹配
		| g        | 执行一个全局匹配
		| m        | 多行匹配模式，^匹配一行的开头和字符串的开头，$匹配行的结束和字符串的结束
### 用于模式匹配的String方法
1. search()
	+ 它的参数是一个正则表达式，返回第一个与之匹配的子串的起始位置，如果找不到，它将返回-1.
	+ 如果search()的参数不是正则表达式，则首先会通过RegExp构造函数将它转换成正则表达式，search()方法不支持全局检索，因为它忽略正则表达式参数中的修饰符g。
2. replace()
	+ 用以执行检索与替换操作，其中第一个参数是一个正则表达式，第二个参数是要进行替换的字符串。
	+ 如果replace()的第一个参数是字符串而不是正则表达式，则replace()将直接搜索这个字符串，而不是像search()一样首先通过RegExp()将它转换为正则表达式。
	+ 如果在替换字符串中出现了$加数字，那么replace()将用与指定的子表达式相匹配的文本来远的这两个字符。这是一个非常有用的特性。
		```javascript
		let text =' "aaaa","bbb" , "ccc" '
		text = text.replace(/"([^"]*)"/g, '“$1”')
		/*
	  用它将一个字符串中的英文引号替换的为中文半角引号
    引号之间的内容(储存在$1中)没有被修改
		*/
		```
	+ replace()方法的第二个参数可以是函数，该函数能够动态地计算替换字符串
3. match()
	+ 它唯一参数就是一个正则表达式(或通过RegExp()构造函数将其转换为正则表达式)，返回的是一个由匹配结果组成的数组。
	+ 如果该正则表达式设置了修饰符g, 则该方法返回的数组包含字符串中的所有匹配结果
	+ 如果传入一个非全局的正则表达式，它返回的数组带有两个属性：index 和 input
4. spilt()
### RegExp对象
1. 简介
	+ RegExp()构造函数带有两个字符串参数，其中第二个参数是可选的，RegExp()用以创建新的RegExp对象。第一个参数包含正则表达式的主体部分，也就是正则表达式直接量中的两条斜杠之间的文本。
	+ 需要注意的是，不论是字符串直接量还是正则表达式，都使用"\"字符作为转义字符的前缀，因此当给RegExp()传入一个字符串表述的正则表达式时，必须将"\"替换成"\\"
		```javascript
		var reg = new RegExp(/\\d{5}/,'igm') // 注意 \\  需要转义
	 	```
	+ RegExp()的第二个参数是可选的，如果提供第二个参数，它就指定正则表达式的修饰符。
2. RegExp的属性
	1) source: 只读字符串，包含正则表达式的文本
	2) global：只读的布尔值，用以说明这个正则表达式是否带有修饰符g
	3) ignoreCase：只读的布尔值，用以说明正则表达式是否带有修饰符i
	4) multiline：只读的布尔值，用以说明正则表达式是否带有修饰符m
	5) lastIndex: 可读/写的整数，如果匹配模式带有g修饰符，这个属性存储在整个字符串中下一次检索的开始位置。
3. RegExp的方法
	1) exec()
		+ 参数是字符串，返回的是匹配结果和本次匹配的完整信息，同string的match()的非全局搜索。 没有结果，返回null。属性index包含了发生匹配的字符位置，属性input引用的是正在检索的字符串
		+ 不管正则表达式是否具有全局修饰符g, exec()都会返回一样的数组
	2) test()
		<br>匹配时，返回true
	3) 在ECMAScript 5中，正则表达式直接的每次计算都会创建一个新的RegExp对象，每个新RegExp对象具有各自的lastIndex属性，这势必会大大减少"残留"lastIndex对程序造成的意外影响
### 应用
1. 常用验证
	+ 手机号：` (\+86)?\s1[3578]\d{9}`
	+ 座机号：` \d{3,4}-\d{7,8} `
	+ 身份证： `\d{15}(\d{2}[0-9xX])?   `
	+ 邮箱：  ` /^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/`
2. 去除空格
	```javascript
	String.prototype.trim = function () {
	  return this.replace(/(^\s*)|(\s*$)/g,'');
	};
	```
3. 获得url参数
	```javascript
	function getUrlQuery(name) {
	  var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i');
	  var r = window.location.search.substr(1).match(reg);
	  if (r != null) {
	    return unescape(r[2]);
	  }
	  return null;
	}
	```
4. 获得cookie
	```javascript
	function getCookie(name) {
	  var arr2, reg = new RegExp("(^| )" + name + "=([^;]*)(;|$)")
	  if (arr2 = document.cookie.match(reg)) {
	    return unescape(arr2[2])
	  } else {
	    return ''
	  }
	}
	```
_______
# 对象
1. Object.create()
<br>第一个参数是对象原型， 第二个参数是对象的属性描述
	```javascript
	//通过原型继承创建一个对象
	function inherit(p) {
	  if (p === null) throw TypeError()
	  if (Obect.create)
	    return Object.create(p)
	  var t = typeof p;
	  if (t !== 'object' && t !== 'function') thorw TypeError()

	  function f() {}
	  f.prototype = p;
	  return new f()
	}
	```
2. 删除属性 delete
	<br>只能删除自有属性，不能删除继承属性
3. 检测属性
	+ in 运算符 : 自有属性，继承属性，同时可以区分不存在的属性和存在但值为undefined的属性。
	+ !== ：判断一个属性是否是undefined
	+ hasOwnProperty()  ：检测自有属性，继承属性 false
	+ propertyIsEnumerable() : 检测自有属性且可枚举
4. 枚举属性
	+ for...in 循环
		<br>循环遍历对象自身的和继承的可枚举属性。
	+ Object.keys
		<br>返回一个数组， 包括对象自身的（不含继承的）所有可枚举属性的键名。
	+ Object.getOwnPropertyNames():
		<br>返回对象的所有自有属性的名称（包含不可枚举的属性）
5. Object.getOwnPropertyDescriptors(): 获得对象自有属性的属性描述符
	<br>Object.getOwnPropertyDescriptors方法的另一个用处，是配合Object.create方法，将对象属性克隆到一个新对象。这属于浅拷贝
	```javascript
	const clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
	```
6. Object.defineProperty(obj, key , obj): 设置属性
	```javascript
	var a = {};
	Object.defineProperty(o,'x',{value:1,writable:true,enumerable:false,configurable:true})
	```
7. 检测类是否继承了另一个类
	+ Object.getPrototypeOf()
8. 检测对象是否是另一个对象的原型（或处在原型链中）
	+ isPrototypeOf()
_____
# 数组
1. join()  ` （不修改原数组）`
2. reverse()  `（修改原数组）`
	<br>原数组中的元素颠倒顺序，返回逆序数组。
3. sort()    ` （修改原数组）`
	<br>默认按照字母表排序。
	```javascript
	[222,4,1111,33].sort(function(a,b){return a-b})
	```
4. concat()   ` （不修改原数组）`
	```javascript
	//创建并返回一个新数组
	var a = [1,2,3]
	a.concat(4,5) //  [1,2,3,4,5]
	a.concat([4],[5]) //  [1,2,3,4,5]
	```
5. slice()  ` （不修改原数组）`
	```javascript
	var a = [1,2,3]
	console.log(a.slice(0,2), a)   // [1,2]   [1,2,3]
	```
6. splice() ` (修改原数组)`
	<br>splice()的前两个参数指定了需要删除的数组元素，后面的任意个参数指定了需要插入到数组中的元素。
	```javascript
	var a = [1,2,3]
	console.log(a.splice(0,2), a)   // [1,2]   [3]
	```
7. push() 和 pop(): 数组尾部插入或删除元素   // (修改原数组)
	+ push() : 插入元素， 返回数组新的长度
	+ pop() : 删除元素， 返回删除的元素
8. unshift() 和 shift() : 数组头部插入或删除元素  // (修改原数组)
	+ unshift() : 插入元素， 返回数组新的长度
	+ shift() : 删除元素， 返回删除的元素
9. forEach()   // （不修改原数组）
	<br>循环遍历数组元素
	```javascript
	arr.forEach(function(v,i,arr){
	  // some code
	}) //不能使用break 中止遍历
	```
10. map()    `（不修改原数组）`
	<br> 将调用的数组的每个元素传递给指定的函数，并返回一个数组，它包含该函数的返回值。
11. filter()   `（不修改原数组）`
	<br>返回的数组元素是调用的数组的一个子集
12. every () 和 some()
	<br>数组的逻辑判定：返回true 或 false
13. reduce() 和 reduceRight()
	<br>将数组元素进行合并，生成单个值。（化简函数）
 	```javascript
	var a = [1,2,3]
	var sum = a.reduce((x,y)=>x+y,0)   // 数组求和
	```
14. indexOf() 和 lastIndexOf()
	<br>返回找到的第一个元素的索引，没有返回-1
15. 判断数组类型
	+ Array.isArray()  (es5)
	```javascript
	//es3版本的实现
	var isArray = Function.isArray || function(o){
	  return typeof o === 'object' && Object.prototype.toString.call(o)==="[Object Array]"
	}
	```
_______
# 函数
1. 函数定义
	+ 函数表达式： 在定义之前不能调用
	+ 函数语句： 在定义之前能调用， 声明提前
2. 函数调用
	1) 作为函数
	2) 作为方法
	3) 作为构造函数
	4) 通过call() 和 apply()间接调用
		+ call() : 使用它自有的实参列表作为函数的实参
		+ apply(): 以数组的方式传入参数
		```javascript
		f.call(obj, 1,2)
		f.apply(obj,[1,2])
		```
3. this 关键字
	1) 在一般函数方法中使用 this 指代全局对象(非严格模式)，或undefined(严格格式下)
	2) 作为对象方法调用，this 指代上级对象
	3) 作为构造函数调用，this 指代new 的实例对象
	4) apply 或者call 调用 ，作用是改变函数的调用对象，此方法的第一个参数为改变后调用这个函数的对象，this指代第一个参数
	5) Node模块或ES6模块中，this返回的是当前模块
4. new 关键字
	1) 先创建了一个新的空对象
	2) 然后让这个空对象的__proto__指向函数的原型prototype
	3) 将对象作为函数的this传进去，如果return 是对象的话就直接返回 return 的内容，没有的话就返回创建的这个对象
	```javascript
	const a = new Foo();，
	//new 实际操作
	const o = new Object(); //创建了一个新的空对象o
	o.__proto__ = Foo.prototype; //让这个o对象的` __proto__`指向函数的原型`prototype`
	Foo.call(o);
	a = o;//将o对象赋给a对象
	```
5. 闭包
	<br>函数定义时的作用域链，到函数执行时依然有效。
	```javascript
	var counter = (function() {
	  var n = 1;
	  return function() {
	    return n++
	  }
	}())
	```
6. bind()方法
	<br>作用：将函数绑定到某个对象 （ES5）
	```javascript
	//es3版本的实现
	if (!Function.prototype.bind) {
	  Function.prototype.bind = function(o) {
	    var self = this,
	      _args = arguments
	    return function() {
	      var args = [],
	        i;
	      for (i = 1; i < _args.length; i++) args.push(_args[i])
	      for (i = 0; i < arguments.length; i++) args.push(arguments[i])
	      return self.apply(o, args)
	    }
	  }
	}
	```
7. isFunction()
	<br>检测一个对象是否是真正的函数（并具有函数方法）
	```javascript
	function isFunction(x){
	  return Obect.prototyp.toString.call(x)==="[Object Function]"
	}
	```
8. 函数式编程
	+ 高阶函数
______
# 类和模块
1. 类与原型
	<br>原型对象是类的唯一标识：当且仅当两个对象继承自同一个原型对象时，它们才是属于同一个类的实例。
2. 类与构造函数
	1) 构造函数是类的公有标识
	2) 检测对象是否属于某个类： instanceof
		<br>`	r instanceof Range  `
	3) constructor
		<br>`F.prototype.constructor == F`
3. JavaScript中的Java式继承
	1) 对于Java类的实例方法来说，实例字段可以用作局部变量，而不需要使用关键字this来引用它们。JS是无法模拟这个特性的，但可以使用with语句近似实现这个功能（并不推荐）
	2) 在Java中可以使用final 声明字段为常量，并且可以将字段和方法声明为privite,用以表示 它们是私有成员且在类的外面是不可见的。在JS 中没有这些关键字。但是可以使用一些命名上写法的约定来给出一些暗示。
		如：
		+ 哪些成员是不可修改的(大写字母命名)
		+ 哪些成员在类外部是不可见的（以下划线为前缀）
		+ 私有属性（使用闭包里的局部变量模拟）
		+ 常量属性 （const）
4. 类的扩充
	+ JS 中基于原型的继承机制是动态的：对象从其原型继承属性，如果创建对象之后，原型的属性发生变化，也会影响到继承这个原型的所有实例对象。
	+ Object.prototype
	+ Object.defineProperty()
5. 构造函数的重载和工厂方法
	1) 重载===>(Java 属性的  多态)
	2) 实现：通过arguments
	```javascript
	function Set() {
	  this.values = {}
	  this.n = 0;
	  if (arguments.length == 1 && isArrayLike(arguments[0]))
	    this.add.apply(this, arguments[0])
	  else if (arguments.length > 0)
	    this.add.apply(this, arguments)
	}
	```
	3) 工厂方法 : 一个类的方法用以返回类的一个实例
		```javascript
		Set.fromArry = function(a) {
		  s = new Set()
		  a.add.apply(s, a)
		  return s
		}
		```
_______
# DOM
### 元素内容
1. innnerHTML   //开始标签和结束标签之间包含的HTML文本
2. textContent  // 获得不带标签的文本内容
3. innerText
	<br> IE8及以下的IE浏览器不支持标准的textContent属性，使用innerText实现类似的功能
### 属性
+ setAttribute()   //IE8及以下版本不支持此方法
+ getAttribute()
+ removeAttribute  // IE8及以下版本不支持此方法
+ hasAttributes()  // IE8及以下版本不支持此方法
### 查找元素
1. 通过HTML
	+ document.getElementById()
	+ document.getElementsByTagName()   // 返回子元素及间接子元素， 可排除空格影响
	+ document.getElementsByName()  // 通过name属性
	 	+ name属性只在少数HTML元素中有效，包括表单，表单元素，iframe, img
		+ 有些元素可以作为 document属性仅通过名字来选取
	```javascript
	//forms数组中，以每个form元素的id唯一标志
	var form=document.forms["form的id值"];
	var inputs=form["name属性值"]
	<form name = "f1"><form>
	var form = document.f1
	```
2. 通过CSS
	+ document.getElementsByClassName()   // IE8+
	+ document.querySelector()  和 document.querySelectorAll()
		<br>没有兼容性，原生浏览器api, 执行效率高，缺点：个别选择器无法使用，如否定选择器:not
3. document.body  和 document.head 和 document.documentElement
4. HTML 集合
	+ document.forms
	+ document.images
### 节点创建、替换、删除
1. 遍历
	+ parentNode
	+ childNodes
	+ firstChild
	+ lastChild
	+ nextSibling
	+ previoursSibling
	+ nodeType
	+ nodeValues
	+ nodeName
2. 创建：
	+ document.createElement('元素名')    // 创建元素
	+	document.createTextNode('text')     // 创建文本节点
	+	document.createDocumentFragment();  // 创建片段
	+	var img = new Image()   // 创建图片
	+	parent.appendChild(childNode)   // 在父节点尾部添加元素
	+	parent.insertBefore(childNode，parent.ChildNodes[n])  // 在指定位置n插入
3. 替换：
	+ parentNode.replaceChild(newNode, oldNode)
4. 删除：
	+ parentNode.removeChild(childNode)
### BOM 对象
1. window 对象
	1) scrollTo(0,100)
	2) scrollIntoView()
		<br>保证元素能在视口中可见，默认情况下，它试图将元素的上边缘放在或尽量接近视口的的上边缘，传入false参数，则将元素的下边缘放在视口的下边缘。
2. Navigator 对象
	+ userAgent: 返回由客户机发送服务器的user-agent 头部的值
	+ cookieEnabled: 返回当前浏览器是否启用cookie
3. Location 对象
	+ href: 返回完整的URL
	+	hash: 返回一个URL的锚部分
	+	host: 返回一个URL的主机名和端口    port : 端口  hostname : 主机名  protocol: 协议
	+	pathname: 路径名
	```javascript
	//在当前窗口打开，可后退
	window.open("url","self");
	<a href="url"></a>
	//在当前窗口打开，不可后退
	location.replace("url");
	//在新窗口打开，可重复打开
	window.open("url");
	<a href="url" target="_blank"></a>
	//在新窗口打开，不可重复打开
	window.open("url","自定义窗口名");
	<a href="url" target="自定义窗口名"></a>
	//页面内跳转到锚点
	location.hash="#锚点名";
	<a href="#锚点名"></a>
	```
	+ search: 返回一个URL的查询部分
4. History 对象
	+ back()
	+ forword()
	+ go()
	+ pushState()  replaceState()   // h5 新加的两个方法 用与ajax() 实现单页跳转
5. Screen 对象
	+ availHeight
	+ availWidth  // 浏览器屏幕的宽高 ， 不包括任务样
	+ Heigth
	+ Width   // 浏览器屏幕的宽高
### 原生事件
```javascript
var EventUtil = {
  // 绑定事件
  addHandler: function(element, type, handler) {
    if (element.addEventListener) {
      element.addEventListener(type, handler, false);
    } else if (element.attachEvent) { // IE 8
      element.attachEvent("on" + type, handler);
    } else {
      element["on" + type] = handler;
    }
  },
  // 移除事件
  removeHandler: function(element, type, handler) {
    if (element.removeEventListener) {
      element.removeEventListener(type, handler, false);
    } else if (element.detachEvent) {
      element.detachEvent("on" + type, handler);
    } else {
      element["on" + type] = null;
    }
  },
  // 获得事件对象
  getEvent: function(event) {
    return event ? event : window.event;
  },
  // 获得事件目标
  getTarget: function(event) {
    return event.target || event.srcElement;
  },
  // 阻止默认行为事件
  preventDefault: function(event) {
    if (event.preventDefault) {
      event.preventDefault();
    } else {
      event.returnValue = false;
    }
  },
  // 阻止事件冒泡
  stopPropagation: function(event) {
    if (event.stopPropagation) {
      event.stopPropagation();
    } else {
      event.cancelBubble = true;
    }
  }
};
```
+ Javascript事件委托
	<br>原理：使用事件委托技术能让你避免对特定的每个节点添加事件监听器；相反，事件监听器是被添加到它们的父元素上，利用冒泡的原理，把事件加到父级上，触发执行效果。
_____
# javascript中获取dom元素的高度和宽度
1. 页面宽高
	+ 网页可见区域宽： document.body.clientWidth
	+ 网页可见区域高： document.body.clientHeight
	+ 网页可见区域宽： document.body.offsetWidth (包括边线的宽)
	+ 网页可见区域高： document.body.offsetHeight (包括边线的高)
	+ 网页正文全文宽： document.body.scrollWidth
	+ 网页正文全文高： document.body.scrollHeight
	+ 网页被卷去的高： document.body.scrollTop
	+ 网页被卷去的左： document.body.scrollLeft
2. dom元素
	+ 元素的实际高度：document.getElementById("div").offsetHeight
	+ 元素的实际宽度：document.getElementById("div").offsetWidth
	+ 元素的实际距离左边界的距离：document.getElementById("div").offsetLeft
	+ 元素的实际距离上边界的距离：document.getElementById("div").offsetTop
_____
# 脚本化HTTP
### XMLHttpRequest
1. 实例化XMLHttpRequest 对象
	```javascript
	var xhr = new XMLHttpRequest()
	//兼容IE8及以下版本：
	function getXHR() {
	  var xhr;
	  if (window.XMLHttpRequest) {
	    xhr = new XMLHttpRequest(); //其它浏览器
	  } else {
	    xhr = new ActiveXObject('Microsoft.XMLHttp') //IE  (只支持IE8以上)
	  }
	  return xhr;
	}
	```
2. 打开请求
	<br> `xhr.open('get',url,true)` ** 第三个参数为false时，表示同步
3. 设置请求头
	<br> `xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded')`
4. 发送请求
	<br> xhr.send()
	+ get 请求没有主体，传递null或者省略这个参数
	+ post通常拥有主体，同时应设置setRequestHeader()
4. 查询响应头
	+ getResponseHeader()
	+ getAllResponseHeaders()
	+ XMLHttpRequest会自动处理cookie, 它会从getAllResponseHeaders()头返回集合中过滤掉cookie头。
5. readyState:请求状态
	+ 0---> 尚未初始化
	+ 1---> 正在发送请求
	+ 2---> 请求完成
	+ 3---> 正在响应
	+ 4---> 响应完毕
6. readystatechange
7. 进度事件
	```javascript
	xhr.onprogress = function(e) {
	  if (e.lengthComputable) {
	    progress.innerHTML = Math.round(100 * e.loaded / e.total) + '% complete'
	  }
	}
	```
### 跨域http请求
1. XHR2 支持cros
	<br>注意点：
	+ 如果给XMLHttpRequest的open()方法传入用户名和密码，那么它们不会通过跨域请求发送（这使分布式密码破解攻击成为可能）
	+ 跨域请求通常不会包含其他任何的用户证书：cookie和HTTP身份验证令牌(token)通常不会作为请求的内容部分发送且任何作为跨域响应来接收的cookie都会丢弃。如果跨域请求需要这几种凭证才能成功，那么必须在用send()发送请求前设置XMLHttpRequest的withCredentials为true。
	+ 测试withCredentials的存在性是测试浏览器是否支持cors的一种方式
2. JSONP
	<br>JSONP中P的意义所在：当通过`<script>`元素调用数据时，响应内容必须用JS函数名和圆括号包裹起来，而不是发送一段纯JSON数据。
	```javascript
	//浏览器端
	function resolveJson(result) {
	  console.log(result.name);
	}
	var jsonpScript = document.createElement("script");
	jsonpScript.type = "text/javascript";
	jsonpScript.src = "http://www.qiute.com?callbackName=resolveJson";
	document.getElementsByTagName("head")[0].appendChild(jsonpScript);
	//服务器端：
	resolveJson({ name: 'qiutc' });
	```
3. Comet: 基于服务器端推送事件
________
# 客户端存储
### cookie
1. 使用cookie的方式繁琐
2. 只适合存储少量的文本数据 （4kb左右）
3. 任何以cookie形式储存的数据，不论服务器端是否需要，每一次HTTP请求都会把这些数据传输到服务器端。（自动在浏览器和服务器之间传输）
4. 属性介绍
	+ max-age: （expires）
		<br>默认有效期与浏览器进程的日期一致，关闭浏览器，cookie失效。设置max-age属性，可以设置有效期，浏览器会将cookie存储在一个文件中，过了有效期会删除文件。
	+ domain:
		<br>设置访问域 （不同域之间）
	+ path:
		<br>可访问文件路径 （同域下的不同文件路径下）
	+ secure
		+ cookie默认是以不安全的形式传递的，secure设置为true后，只能通过https或其他安全协议传递。
		+ cookie对浏览器的其他子页面，标签页都是共享的。
### localStorage & sessionStorage
1. localStorage
	+ localStorage存储的数据是永久性的，除非web应用刻意删除存储的数据
	+ localStorage的作用域是限定在文档源级别的，即协议、主机名、端口三者确定。同源的文档间共享同样的localStorage数据，不同的窗口或标签页面中也共享。（可读可写可覆盖）
	+ localSotrage和存储事件都是采用广播机制的，浏览器会对目前正在访问同样站点的所有窗口发送消息。
2. sessionStorage
	+ sessionStorage存储的数据是临时性，浏览器窗口或标签页面之间刷新或关闭，sessionStorage即失效。
	+ sessionStorage作用域也是限定在文档源级别的，同时还限定在窗口中。如果同源的文档在不同的浏览器标签页中，它们互相之间拥有不同的sessionStorage数据，无法共享。哪怕这两个标签渲染的是同一个页面，运行同一个脚本。
3. 存储api (localStorage  & sessionStorage)
	+ setItem(key, value)
	+ getItem(key)
	+ removeItem(key)
	+ clear()
_____

#  地理位置信息
	+ navigator.geolocation

# 历史记录和管理
### 路由的实现方式
1. 监听window的hashchange事件 // 单面路由跳转  (#a)
2. h5的hisotry api方式
	+ history.pushState()
	+ history.replaceState()
	+ window.onpopstate = function(){}
_____
# 跨域消息传递
### postMessage()
+ window方法， 允许非同源脚本调用，该方法允许有限的通信，通过异步消息传递的方式，在来自不同源的脚本之间。"跨文档消息传递"
+ postMessage() 接收两个参数：
	+ 第一个是消息传递内容，可以是任意基本类型和可以复制对象,对于可以接收字符串的，应使用JSON.stringify()序列化。
	+ 第二个参数是指定的窗口的源。（协议，主机名，端口）。'*' 表示传递给任意窗口，'/' 表示当前窗口和指定窗口同源。
+ 目标窗口
	+ message事件， 拥有三个事件对象 data  source origin
