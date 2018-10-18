# 基本技巧
1. 尽量少用全局变量
	<br> 全局变量的问题在于它们在整个JavaScript应用或Web页面内共享。它们生存于同一个全局命令空间内，总有可能发生命令冲突。
2. **避免使用eval()**
	+ eval()函数可将任意字符串当做一个JavaScript代码来执行。
	+ 使用eval()也包含一些安全隐患，因为这样做有可能执行被篡改过代码。这是在处理来自一个Ajax请求的JSON响应时常见的反模式。
	+ 使用new Function()构造函数和eval()比较类似，如果一定需要使用eval()，那么可以考虑使用new Function()来替代eval() 。这样做的一个潜在的好处是由于在new Function()中的代码将在局部函数空间中运行，因此代码中的任何采用var定义的变量不会自动成为全局变量。另一个避免自动成为全局变量的方法是将eval()调用封装到一个立即函数(IIFE)中。
		```javascript
		var jsstring = "var un = 1; console.log(un);";
		eval(jsstring); // 1
		jsstring = "var deux = 2; console.log(deux);";
		new Function(jsstring)(); //2
		jsstring = "var trois = 3 ; console.log(trois);";
		(function() {
		  eval(jsstring);
		}()); // 3
		console.log(typeof un, typeof deux, typeof trois)
		//number undefined undefined
		```
	+ 另一个new Function()和eval()的区别在于eval()会影响到作用域链，而Function更多地类似于一个沙盒。无论在哪里执行Function()，它都仅仅能看到全局作用域。因此对局部变量的影响比较小。
		```javascript
		(function(){
		  var local = 1;
		  eval("local = 3; console.log(local)";); //3
		  console.log(local) // 3
		}());
		(function(){
		  var local = 1;
		  Function("console.log(typeof local);")(); // undefined
		}())
		```
____
# 字面量和构造函数
### 对象
1. 对象字面量 `var obj = {} `
2. 来自构造函数的对象 : `var obj = new Object()` //反模式
	+ 字面量表示法的显著优点在于它仅需要输入更短的字符。优先选择字面量模式以创建对象的另一个原因在于它强调了该对象仅是一个可变哈希映射，而不是从对象中提取的属性或方法。
	+ 与使用object构造函数相对，使用字面量的另一个原因在于它并没有作用域解析。因为可能以同样的名字创建了一个局部构造函数，解释器需要从调用Object()的位置开始一直向上查询作用域链，直到发现全局object构造函数。
	+ 当传递给Object()构造函数的值是动态的，并且直到运行才能确定类型时，Object构造函数的这种行为可能会导致意料不到的结果。因此，总的来说，不要使用new Object()构造函数，相反应该使用更为简单、可靠的对象字面量模式。
3. 自定义构造函数 `var p = new Person()`
### 数组
1. new Array()
	+ 避开new Array()的另一个理由是用于避免构造函数中可能产生的陷阱。
	+ 当向Array构造函数传递单个数字时，它并不会成为第一个数组元素的值。相反，它却设置了数组的长度。
	+ 如果传递一个浮点数，则会报错`RangeError: invalid array length`
2. 检查数组性质
	+ Array.isArray()  //ES5
		```javascript
		if(typeof Array.isArray === 'undefined'){
		  Array.isArray = function(arg){
		    return Object.prototype.toString.call(arg) === "[object Array]"
			}
		}
		```
3. JSON
	+ 它是一种轻量级数据交换格式，对于JSON而言，它只是一个数组和对象字面量表示方法的组合。
	+ JSON和文字对象之间唯一的语法差异在于，在JSON中，属性名称需要包装在引号中才能成为合法的JSON。而在对象字面量中，仅当属性名称不是有效的标识符时才会需要引号。
	+ 此外，在JSON字符串中，不能使用函数或正则表达式字面量。
____

# 函数
1. 自定义函数
	+ 函数可以动态定义，也可以分配给变量。如果创建了一个新函数并且将其分配给保存了另外函数的同一个变量，那么就以一个新函数覆盖了旧函数。在某种程度上，回收了旧函数指针以指向一个新函数。而这一切发生在旧函数体的内部。在这种情况下，该函数以一个新的实现覆盖并重新定义了自身。
		```javascript
		var scareMe = function(){
		  alert('111');
		  scareMe = function() {
		    alert("222")
		  };
		};
		scareMe(); // 111
		scareMe() ///222
		```
	+ 当函数有一些初始化准备工作要做，并且仅需要执行一次，那么这种模式就非常有用。使用些模式可以显著地提升应用程序的性能，这是由于重新定义的函数仅执行了更少的工作。
	+ 这种模式的另一个名称是“`惰性函数定义`”(lazy fucntion definition)，因为该函数直到第一次使用时才被正确地定义，并且其具有向后惰性，执行了更少的工作。
	+ 该模式的其中一个缺点在于，当它重定义自身时已经添加到原始函数的任何属性都会丢失。此外，如果该函数使用了不同的名称，比如分配给不同的变量或者以对象的方法来使用，那么重定义部分将永远不会发生，并且将会执行原始函数体。
2. 初始化时分支
	+ 初始化时分支[Init-time branching, 也称为加载时分支(load-tim branching)]是一种优化模式。当知道某个条件在整个程序生命周期内都不会发生改变的时候，仅对该条件测试一次是很有意义的。浏览器嗅探器(或功能检测)、查明DOM元素的计算样式或附加的事件处理程序等是可以受益于初始化时分支模式的场景。
3. Curry化
	+ 使函数理解并处理部分应用的过程就称为Curry过程(Currying)
	+ 当新函数是基于现有函数，并加上部分参数列表创建时。
________
# 对象创建模式
### 声明依赖关系
+ 在函数或模块顶部声明代码所依赖的模块是一个非常好的主意，该声明仅涉及创建一个局部变量并使其指向所需的模块。
+ 优点：
	1) 显式的依赖声明向用户表明了他们确定需要的特定脚本文件已经包含在该页面中。
	2) 在函数顶部的前期声明可以很容易发现并解析依赖
	3) 解析局部变量的速度总是比解析全局变量要快，这导致了更好的性能。当使用这种依赖声明时，全局符号解析仅会在函数中执行一次。在此之后将会使用局部变量，这种解析速度也快得多。
	4) 类似于YUICompressor和Google闭包编译器的这些高级小工具可以重命名局部变量，这导致了更小的代码量，但是这些工具不会对全局变量进行重命名，因为这样做是不安全的。
### 私有属性和方法
1. 私有成员
	<br>虽然JavaScript语言中并没有用于私有成员的特殊方法，但可以使用`闭包(closure)`来实现这种功能。构造函数创建了一个闭包，而在闭包范围内部的任意变量都不会暴露给构造函数以外的代码。然后，这些私有变量仍然可以用于公共方法中：即定义在构造函数中，且作为返回对象的一个部分暴露给外部的方法。
	```javascript
	function Gadget() {
	  var name = 'iPod';   //私有成员
	  // 公共方法  (特权方法)
	  this.getName = function() {
	    return name;
	  };
	}
	var toy = new Gadget()
	console.log(toy.name) // undefined
	console.log(toy.getName()) //iPod
	```
2. 特权方法
<br> 特权方法(Privileged Method), 指那些可以访问私有成员的公共方法的一个名称。
3. 私有性失效
	<br> 当直接从一个特权方法中返回私有变量，且该变量恰好是一个对象或者数组，那么外面的代码依然可以访问该私有变量，这是因为它通过引用传递的。
	```javascript
	function Gadget() {
	  //私有成员
	  var specs = {
	    screen_width: 320,
	    screen_height: 480,
	    color: "white"
	  };
	  // 公共方法  (特权方法)
	  this.getSpecs = function() {
	    return specs;
	  };
	}
	var toy = new Gadget()
	specs = toy.getSpecs()
	specs.color = "black"
	console.log(toy.getSpecs().color) //black
	```
	+ 解决这个问题的一种方法是，使getSpecs()返回一个新对象，而该对象仅包含客户关注的原对象中的数据，即`最低授权原则`(Principle of Least Authority，POLA))，其中规定了应该永远不要给予超过需要的特权。
	+ 当需要传递所有的数据时，另外一种解决方法使用一个通用性的对象克隆(object-cloning)函数以创建specs对象的副本。
4. 对象字面量以及私有性
	+ 使用对象字面量的情况下，可以使用一个额外的匿名即时函数创建闭包来实现私有性。
		```javascript
		var myobj;
		(function() {
		  //私有成员
		  var name = 'hi'
		  myobj = {
		    getName: function() {
		      return name;
		    }
		  };
		}())
		console.log(myobj.getName()) //hi
		```
	+ 另一种实现，"`模块模式`"的基础框架
		```javascript
		var myobj = (function() {
		  //私有成员
		  var name = 'hi'
		  // 公共方法  (特权方法)
		  return {
		    getName: function() {
		      return name;
		    }
		  };
		}())
		console.log(myobj.getName()) //hi
		```
5. 原型和私有性
	+ 当将私有成员与构造函数一起使用时，其中的一个缺点在于每次调用构造函数以创建对象时，这些私有成员都会被重新创建。
	+ 构造函数中添加到this中的任何成员都实际上面临以上问题。为了避免复制工作以及节省内存，可以将常用属性和方法添加到构造函数的prototype属性中。这样，通过同一个构造函数创建的多个实例可以共享常见的部分数据。此外，还可以在多个实例中共享隐藏的私有成员。
	```javascript
	function Gadget() {
	  var name = 'iPod';
	  this.getName = function() {
	    return name;
	  };
	}
	Gadget.prototype = (function() {
	  var broswer = 'Mobile Webkit';
	  return {
	    getBroswer: function() {
	      return broswer;
	    }
	  };
	}())
	var toy = new Gadget()
	console.log(toy.getName()) //iPod
	console.log(toy.getBroswer()) //Mobile Webkit
	```
6. 将私有方法揭示为公共方法
	+ 揭示模式(revelation pattern)可用于将私有方法暴露为公共方法。当为了对象的运转而将所有功能放置在一个对象中以及想尽可能地保护该对象是至关重要的时候，这种揭示模式就显得非常有用。
### 模块模式
+ 与其他语言不同的是，JavaScript并没有包(package)的特殊语法，但是模块模式提供了一种创建自包含非耦合代码片段的有利工具，可以将它视为黑盒功能，并且可以根据所编写软件的需求添加、替换或删除这些模块。
1. 模块模式是多种模式的组合，也就是以下模式的组合：
	1) 命名空间
	2) 即时函数
	3) 私有和特权成员
	4) 声明依赖
	```javascript
	MYAPP.namespace('MYAPP.utilites.array')
	MYAPP.utilites.array = (function() {
	  //依赖
	  var uobj = MYAPP.utilites.object,
	    ulang = Object.utilites.lang,
	    //私有属性
	    array_string = "[object Array]";
	  // 私有方法
	  //....
	  // var变量定义结束
	  // 公有api
	  return {
	    inArray: function() {}
	    // ....更多方法和属性
	  };
	}())
	```
2. 揭示模块模式
	```javascript
	MYAPP.utilites.array = (function() {
	  //私有属性
	  var array_string = "[object Array]",
	    // 私有方法
	    inArray = function() {},
	    isArray = function() {};
	  // 揭示公有api
	  return {
	    isArray: isArray,
	    indexOf: inArray
	  };
	}())
	```
3. 创建构造函数的模块
	<br>前面的例子中创建了一个对象MYAPP.utilites.array，但有时候使用构造函数创建对象更为方便。它们之间唯一的区别在于包装了模块的即时函数最终将会返回一个函数，而不是返回一个对象。
	```javascript
	MYAPP.namespace('MYAPP.utilites.Array')
	MYAPP.utilites.Array = (function() {
	  //依赖
	  var uobj = MYAPP.utilites.object,
	    ulang = Object.utilites.lang,
	    //私有属性和方法
	    Constr;
	  // var变量定义结束
	  // 公有api——构造函数
	  Constr = function(o) {
	    this.elements = this.toArray(o)
	  };
	  // 公有api——原型
	  Constr.prototype = {
	    constructor: MYAPP.utilites.array,
	    toArray: function(obj) {}
	  };
	  // 返回要分配给新命名空间的构造函数
	  return Constr;
	}());
	var arr = new MYAPP.utilites.Array(obj)
	```
4. 将全局变量导入到模块中
	<br>在常见的变化模式中，可以将参数传递到包装了模块的即时函数中，可以传递任何值，但是通常这些都是对全局变量、甚至是全局对象本身的引用。导入全局变量有助于加速即时函数的全局符号解析的速度，因为这些导入的变量成为了该函数的局部变量。
	```javascript
	MYAPP.utilites.module = (function(app, global) {
	  // 引入全局对象以及现在被转换为局部变量的全局应用程序命名空间对象
	}(MYAPP,this));
	```
### 沙箱模式
+ 沙箱模式(sandbox pattern)解决了`命名空间模式`的如下几个缺点：
	1) 对单个全局变量的依赖变成了对应用程序的全局变依赖。在命名空间模式中，是没有办法使用同一个应用程序或库的两个版本运行在同一个页面中，因为这两者都需要同一个全局符号名，比如全局变量MYAPP。
	2) 对这种以点分割的名字来说，需要输入更长的字符，并且在运行时需要解析更长的时候，比如MYAPP.utilites.Array
+ 顾名思义，沙箱模式提供了一个可用于模块运行的环境，且不会对其他模块和个人沙箱造成任何影响。
1. 全局构造函数
	+ 在命名空间模式中，有一个全局对象；在沙箱模式中，则是一个全局构造函数，让我们称为Sandbox()。可以使用该构造函数创建对象并且还可以传递回调函数，它变成了代码的隔离沙箱运行环境。
	+ 使用沙箱的方法如下所示
		```javascript
		new Sanbox(['ajax', 'event'], function (box) {
		  //代码
		})
		/*
		  Sanbox构造函数可以接受一个或额外的配置参数，其中该参数指定了对象实例所需要的模块名。通配符“*”参数表示，使用所有可用模块，没有参数时，也认定是“*”
		*/
		```
2. 实现构造函数
	```javascript
	function Sandbox() {
	  var args = Array.prototype.slice.call(arguments),
	    callback = args.pop(), // 最后一个参数是回调函数
	    // 模块可以作为一个数组传递，或作为单独的参数传递
	    modules = (args[0] && typeof args[0] === "string") ? args : args[0],
	    i;
	  if (!(this instanceof Sandbox)) { // 确保该函数作为构造函数被调用  , (防止漏掉new)
	    return new Sandbox(modules, callback)
	  }
	  this.a = 1;
	  this.b = 2;
	  // 向核心'this'对象添加模块，不指定模块名称或指定“*”都表示使用所有模块
	  if (!modules || modules === '*') {
	    modules = [];
	    for (i in Sandbox.modules) {
	      if (Sandbox.modules.hasOwnProperty(i)) {
	        modules.push(i);
	      }
	    }
	  }
	  for (i = 0; i < modules.length; i++) {
	    Sandbox.modules[modules[i]](this)
	  }
	  callback(this)
	}
	Sandbox.prototype = {
	  name: "My Application",
	  getName: function() {
	    return this.name
	  }
	};
	```
### 静态成员
1. 公有静态成员 (ES6中class、static的ES5实现)
	+ 在JavaScript中并没有特殊语法来表示静态成员，但是可以通过使用`构造函数`并且向其`添加属性`这种方式，从而获得与"类式(class, 即具有class性质)"语言相同的语法。
		```javascript
		var Gadget = function() {}
		// 静态方法
		Gadget.isShiny = function() {
		  return "you bet";
		};
		// 向该原型添加一个普通方法
		Gadget.prototype.setPrice = function(price) {
		  this.price = price;
		};
		// 调用静态方法
		Gadget.isShiny() // you bet
		var iphone = new Gadget();
		iphone.setPrice(500)
		```
	+ 试图以静态方式调用一个实例方法是无法正常运行的。同样的，如果使用实例iphone对象调用静态方法也无法正常运行。
		```javascript
		typeof Gadget.setPrice; //undefined
		typeof iphone.isShiny; //undefined
		```
	+ 有时候也可以很方便地使静态方法与实例一起工作。只需要向原型中添加一个新的方法即可。
		```javascript
		Gadget.prototype.isShiny = Gadget.isShiny;
		iphone.isShiny() //you bet
		/*
		  如果在静态方法内部使用this要特别注意。当执行Gadget.isShiny()时，那么isShiny()内部的this将会指向Gadget构造函数。如果执行iphone.isShiny(),那么this将会指向iphone
		*/
		```
2. 私有静态成员
	+ 就私有静态成员而言，指的是成员具有如下属性：
		1) 以同一个构造函数创建的所有对象共享该成员。
		2) 构造函数外部不可访问该成员
	```javascript
	var Gadget = (function() {
	  var counter = 0,
	    NewGadget; // 静态变量/属性
	  NewGadget = function() {
	    counter += 1;
	  };
		// 特权方法
	  NewGadget.prototype.getLastId = function() {
	    return counter
	  };
	  return NewGadget;
	}())
	var iphone = new Gadget()
	iphone.getLastId(); //1
	var ipod = new Gadget()
	ipod.getLastId(); //2
	```
3. 静态属性(公有和私有)使用会带来很多便利，它可以包含非实例相关的方法和数据，并且不会为每个实例重新创建静态属性。
### 链模式
+ 链模式(Chaining Pattern)可以能够一个接一个的调用对象的方法，而无需将前一个操作返回的值赋给变量，并且无需将调用分割为多行。
+ 当创建的方法其返回的是无任何意义的值时，可以使它们返回this, 即正在使用的对象的实例。
+ 链模式的优点和缺点
	+ 优点：可以节点一些输入的字符，创建更简洁的代码；有助于分割函数，以创建更简短、具有特定功能的函数，提高代码的可维护性。
	+ 缺点：以这种方式编写的代码更加难以调试。
________

# 代码复用模式
`优先使用对象组合，而不是类继承`
### 类式继承模式#1——默认模式
+ 可复用继承函数inherit()
	```javascript
	function inherit(C, P){
	  C.prototype = new P()
	} // 一个对象的原型是另一个对象的实例
	```
+ 缺点：
	1) 其中一个缺点在于同时继承了两个对象的属性，即添加到this的属性以及原型属性, 在绝大多数的时候，并不需要这些自身的属性，因为它们很可能是指向一个特定的实例，而不是复用。
	2) 另一个使用通用inherit()函数的问题在于它并`不支持将参数传递`到子构造函数中。若需要在子构造函数将参数传递到父构造函数中，每次需要一个新子对象时都必须重新执行这种继承机制，而且该机制其效率低下，其原因在于最终会反复地重新创建父对象。
### 类式继承模式#2——借用构造函数
+ 本模式解决了从子构造函数到父构造函数的参数传递问题。本模式借用了父构造函数，它传递子对象以绑定到this, 并且还转发任意参数。
	```javascript
	function Child(a, c, b, d){
	  Parent.apply(this, arguments);
	}
	```
+ 优缺点
	+ 优点：在于可以获得`父对象自身成员`的真实副本，并且也不会存在子对象意外覆盖父对象属性的风险。
	+ 缺点：在于无法从原型中继承任何东西，并且原型也仅是添加可重用方法以及属性的位置，它并不会为每个实例重新创建原型。
### 类式继承模式#3——借用和设置原型
+ 本模式主要思想是结合前两种模式，即先借用构造函数，然后还设置子构造函数的原型使其指向一个构造函数创建的新实例。
  ```javascript
  function Child(a, c, b, d){
    Parent.apply(this, arguments);
  }
  Child.prototype = new Parent()
  ```
+ 优缺点
	+ 优点：在于对象能够获得父对象本身的成员副本以及指向父对象中可复用功能(以原型成员方式实现的那么功能)的引用。同时，子对象也能够将任意参数传递到父构造函数中。
	+ 缺点：父构造函数被调用了两次，因此这导致了其效率低下的问题。最后，自身的属性会被继承两次。
### 类式继承模式#4——共享原型
+ 不同于前面需要两次调用父构造函数的模式，共享原型模式的经验法则在于：可复用成员应该转移到原型中而不是放置在this中。因此，出于继承的目的，任何值得继承的东西都应该放置在原型中实现。所以，可以仅将子对象的原型与父对象的原型设置为相同的即可。
	```javascript
	function inherit(C, P){
	  C.prototype = P.prototype
	}// 这种模式，们添加到this中的任何成员都不会被继承
	```
+ 优缺点: 能够提供简短而迅速的原型链查询，这是由于所有的对象实际上共享了同一个原型。但这同时也是一个缺点，因为如果在继承链下方的某处存在一个子对象修改了原型，它将会影响到所有的父对象和祖先对象。
### 类式继承模式#5——临时构造函数
+ 通过断开父对象与子对象的原型之间的直接链接关系，从而解决了共享同一个原型所带来的问题，而且同时还能够继承受益于原型链带来的好处。
	```javascript
	function inherit(C, P){
	  var F = function() {}
	  F.prototype = P.prototype;
	  C.prototype = new F()
	}
	```
+ 存储超类
	<br>在上面的模式的基础上，还可以添加一个指向原始父对象的引用，该属性称为uber(因为super是js的保留字，es6)。
	```javascript
	function inherit(C, P){
	  var F = function() {}
	  F.prototype = P.prototype;
	  C.prototype = new F();
		C.uber = P.prototype;
	}
	```
+ 重置构造函数指针(圣杯模式)
	```javascript
	var inherit = (function() {
	  var F = function() {}
	  return function(C, P) {
	    F.prototype = P.prototype;
	    C.prototype = new F();
	    C.uber = P.prototype;
	    C.prototype.constructor = C;
	  }
	}())
	```
### Klass
```javascript
var klass = function(Parent, props) {
  var Child, F, i;
  //1. 新的构造函数
  Child = function() {
    if (Child.uber && Child.uber.hasOwnProperty("__construct")) {
      Child.uber.__construct.apply(this, arguments);
    }
    if (Child.prototype.hasOwnProperty("__construct")) {
      Child.prototype.__construct.apply(this, arguments);
    }
  };
  // 2. 继承
  Parent = Parent || Object;
  F = function() {};
  F.prototype = Parent.prototype;
  Child.prototype = new F();
  Child.uber = Parent.prototype;
  Child.prototype.constructor = Child;
  // 3.添加实现方法
  for (i in props) {
    if (props.hasOwnProperty(i)) {
      Child.prototype[i] = props[i];
    }
  }
  // 返回该"class"
  return Child;
}
```
### 原型继承
```javascript
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
} // ES5 Ojbect.create()
```
### 通过复制属性实现继承
1. 浅复制
	<br>它仅遍历父对象的成员并将其复制出来，由于JavaScript中的对象是通过引用而传递的，如果改变了子对象的属性，并且该属性恰好是一个对象，那么这种操作表示也正在修改父对象。
 	```javascript
	function extend(parent, child) {
	  var i;
	  child = child || {};
	  for (i in parent) {
	    if (parent.hasOwnProperty(i)) {
	      child[i] = parent[i];
	    }
	  }
	  return child;
	}
	```
2. 深复制
	<br>深度复制(deep copy)意味着属性检查，如果即将复制的属性是一个对象或者一个数组，这样的话，它将会`递归遍历`该属性并且还会将该属性中的元素复制出来。
	```javascript
	function extendDeep(parent, child) {
	  var i,
	    toStr = Object.prototype.toString,
	    astr = "[object Array]";
	  child = child || {};
	  for (i in parent) {
	    if (parent.hasOwnProperty(i)) {
	      if (typeof parent[i] === "object") {
	        child[i] = (toStr.call(parent[i]) === astr) ? [] : {};
	        extendDeep(parent[i], child[i]);
	      } else {
	        child[i] = parent[i];
	      }
	    }
	  }
	  return child;
	}
	```
+ **在本模式中没有涉及任何原型，本模式仅与对象以及它们自身的属性相关**
3. 混入
	<br>mix-in模式并不是复制一个完整的对象，而是从多个对象中复制出任意的成员并将这些成员组合成一个新对象。mix-in实现比较简单，只需遍历每个参数，并且复制出传递给该函数的每个对象中的每个属性。
	```javascript
	function mix() {
	  var arg, prop, child = {};
	  for (arg = 0; arg < arguments.length; arg++) {
	    for (prop in arguments[arg]) {
	      if (parent.hasOwnProperty(i)) {
	        child[prop] = arguments[arg][prop];
	      }
	    }
	  }
	  return child;
	}
	```
### 借用方法
1. 有时候，可能恰好仅需要现有对象其中一个或两个方法。在想要重用这些方法的同时，但是又确实不希望与源对象形成父-子继承关系。在这种情况下，可以通过使用借用方法(borrowing method)模式来实现，而这是受益于call()和apply()函数方法。
2. apply()和call()这两者之间的唯一区别在于apply()可以接受传递给将被调用方法的参数数组，call()仅逐个的接受参数。可以传递对象、任意参数以及借用方法，并将它们绑定到目标对象中以作为this本身的成员。
	```javascript
	var one = {
	  name: "one",
	  say: function(greet) {
	    return greet + ',' + this.name;
	  }
	}
	var two = {
	  name: "two"
	}
	one.say.apply(two, ['hello']) // hello, two
	```
3. 绑定this
	+ 若将函数赋值赋值给一个全局变量，或者将该函数作为回调函数来传递，此时的this将指向全局环境，导致代码无法按预期运行。为了修复对象与方法之间的关系，可以使用bind()
		```javascript
		function bind(o, m) {
		  return function () {
		    return m.apply(o, [].slice.call(arguments));
		  };
		} // 需要额外闭包开销， [] 和 Array的差别，在于[]需要创建额外的空数组开销
		```
	+ Function.prototype.bind()  // ES5
		```javascript
		if (typeof Function.prototype.bind === "undefined") {
		  Function.prototype.bind = function(o) {
		    var fn = this,
		      slice = Array.prototype.slice,
		      args = slice.call(arguments, 1);
		    return function() {
		      return fn.apply(o, args.concat(slice.call(arguments)));
		    };
		  };
		}
		```
________

# 设计模式
### 单例模式
+ 单例(Singleton)模式的思想在于保证一个特定类仅有一个实例。这意味着当第二次使用同一个类创建新对象的时候，应该得到与第一次所创建对象完全相同对象。
+ 可以认为每次在使用对象字面量创建对象的时候，实际上就正在创建一个单例，并且不涉及任何特殊语法。
1. 使用new操作符
	+ JavaScript中并没有类，但是JavaScript中具有new语法可使用构造函数来创建对象，而且有时可能需要这种语法的单例实现。这种思想在于当使用同一个构造函数以new操作符来创建多个对象时，应该仅获得指向完全相同的对象的新指针。
2. 静态属性中的实例
	```javascript
	function Universe() {
	  if (typeof Universe.instance === 'object') {
	    return Universe.instance;
	  }
	  this.start = 0;
	  this.bang = "Big";
	  Universe.instance = this; // 缓存
	  return this; //隐式返回
	} //缺点是instance属性是公开的，可能会被无意修改
	```
3. 闭包中的实例
	```javascript
	function Universe() {
	  var instance = this;  //缓存实例
	  this.start = 0;
	  this.bang = "Big";
	  // 重写构造函数
	  Universe = function(){
	    return instance;  // 闭包访问instance
	  };
	}  // 闭包开销, 重写构造函数会丢失所有在初始定义和重定义时添加的属性。
	```
4. 调整原型构造函数指针
	```javascript
	function Universe() {
	  var instance;
	  // 重写构造函数
	  Universe = function Universe() {
	    return instance;
	  };
	  Universe.prototype = this; // 保留原型属性
	  instance = new Universe(); //实例
	  instance.constructor = Universe; // 重置构造函数指针
	  instance.start = 0;
	  instance.bang = "Big";
	  return instance
	}
	```
5. 将构造函数和实例包装在即时函数中
	```javascript
	var Universe;
	(function() {
	  var instance;
	  Universe = function Universe() {
	    if (instance) {
	      return instance;
	    }
	    instance = this
	    this.start = 0;
	    this.bang = "Big";
	  };
	}())
	/*
	  在第一次调用构造函数时，它会创建一个对象，并且使得私有instance指向该对象。从第二次调用之后，该构造函数仅返回该私有变量。
	*/
	```
### 工厂模式
+ 设置工厂模式的目的是为了创建对象。它通常在类或类的静态方法中实现。具有下列目标：
	+ 当创建相似对象时执行重复操作
	+ 在编译时不知道具体类型(类)的情况下，为工厂用户提供一种创建对象的接口
### 迭代器模式
+ 在迭代器模式中，通常有一个包含某种数据集合的对象。该数据可能存储在一个数据结构内部，而要提供一种简单的方法能够访问数据结构中每个元素。对象的消费者并不需要知道如何组织数据，所有需要做的就是取出单个数据进行工作。
+ 在迭代器模式中，对象需要提供一个next()方法。依次调用next()必须返回下一个连续的元素。
### 装饰者模式
+ 装饰者模式的一个比较方便的特征在于其预期行为的可定制和可配置特性。可以从仅具有一些基本功能的普通对象开始，然后从可用装饰资源池中选择需要用于增强普通对象的那些功能，并且按照顺序进行装饰，尤其是当装饰顺序很重要的时候。
### 策略模式
+ 策略模式支持在运行时选择算法。代码的客户端可以使用同一个接口来工作，但是它却根据客户正在试图执行任务的上下文，从多个算法中选择用于处理特定任务的算法。
### 外观模式
+ 外观(facade)模式是一种简单的模式，它为对象提供了一个可供选择的接口。这是一种非常好的设计实践，可保持方法的简洁性并且不会使它们处理过多的工作。如：
	```javascript
	var myevent = {
	  stop: function(e){
	    e.preventDefault();
	    e.stopPropagation();
	  }
	};
	```
### 代理模式
+ 在代理设计模式中，一个对象充当另一个对象的接口。它与外观模式的区别在于，在外观模式中所拥有的是合并了多个调用的便利方法。代理则介于对象的客户端和对象本身之间，并且对该对象的访问进行保护。
+ 这种模式可能看起来像是额外的开销，但是出于性能因素的考虑它却非常有用。代理充当了某个对象(也称为“本体对象”)的守护对象，并且试图使本体对象做尽可能少的工作。
+ 使用这种模式的其中一个例子是我们可以称为`延迟初始化`(lazy initialization)的方法。假设初始化本体对象的开销非常大，而恰好又在客户端初始化对象以后，应用程序实际上却从来没有使用过它。在这种情况下，代理可以通过替换本体对象的接口来解决这个问题。代理接收初始化请求，但是直到该本体对象明确的将被实际使用之前，代理从不会将该请求传递给本体对象。
### 中介者模式
+ 通过使对象之间相互并不直接通信，而是仅通过一个中介者对象进行通信，从而促进形成松散耦合。
### 观察者模式
+ 该模式的另外一个别名是`订阅/发布`(subscriber/publisher)模式
+ 设计这种模式背后的主要动机是促进形成松散耦合。在这种模式中，并不是一个对象调用另一个对象的方法，而是一个对象订阅另一个对象的特定活动并在状态改变后获得通知。订阅者也称为观察者，而被观察的对象称为发布者或者主题。当发生了一个重要的事件时，发布者将会通知(调用)所有订阅者并且可能经常以事件对象的形式传递消息。
________

# DOM和浏览器模式
### 关注分离
+ 关注分离(spearation of concerns)也体现了`渐进增强`(progress enhancement)的思想，最简单的用户终端可以具有基本的体现(仅能显示HTML文档), 并随着用户终端能力的改进而获取更佳的用户体验。如果浏览器支持CSS，那么用户将可看到文档更好的表现方式。如果浏览器支持JavaScript，那么该文档更大程度上看起来像一个应用程序，并将获取更多增强用户体验的特性。
### DOM脚本
+ DOM访问的代价是昂贵的，它是制约JavaScript性能的主要瓶颈。这是因为DOM通常是独立于JavaScript引擎而实现的。从浏览器的视角看，采用该方法是有意义的，因为有的JavaScript应用程序可能根本就不需要DOM。
+ 总之，DOM的访问应该减少到最低。这意味着：
	1) 避免在循环中使用DOM访问
	2) 将DOM引用分配给局部变量，并使用这些局部变量。
	3) 在可能的情况下使用selector API。
	4) 当在HTML容器中重复使用时，缓存重复的次数。
+ 更新DOM会导致浏览器重新绘制屏幕，也会经常导致reflow(也就是重新计算元素的几何位置)，这样会带来巨大的开销。当需要创建一个相对比较大的子树，应该在子树完全创建之后再将子树添加到DOM树中。这时可以采用文档碎片(document fragment)技术来容纳所有节点。
	<br>`va frag = document.createDocumentFragment()`
+ 在为DOM树添加新节点时文档碎片是非常有用的。但在更新DOM现有部分时，仍然可以批处理提交修改。具体的方法是：为需要修改的子树的根节点建立一个克隆镜像，然后对该克隆镜像做所有的修改操作，在完全修改操作后用克隆镜像替换原来的子树。
	```javascript
	var oldnode = document.getElementById('result'),
	clone = oldnode.cloneNode(true);  // ture 深度克隆
	//处理克隆对象
	oldnode.parentNode.replaceChild(clone, oldnode)
	```
### 载入策略
1. 延迟加载
	+ 关于在页面载入完成后，转入外部文件的这种技术称为延迟加载。通常将一大段代码切为两部分是十分有益的：一部分代码是用于初始化页面并将事件处理器附加到UI元素上的。第二部分代码只在用户交互或者其他条件下才用得上。
	+ 这样做的目的是希望渐进式地载入页面，尽可能快地提供目前需要使用的信息，而其余的内容可以在用户浏览该页面时在后台载入。
	+ 载入第二部分JavaScript代码的方法非常简单，只需要再一次为head或者body添加动态脚本元素。
		```javascript
		window.onload = function(){
		  var script = document.createElement("script");
		  script.src = "xx.js";
		  document.documentElement.firstChild.appendChild(script)
		}
		```
2. 按需加载
	```javascript
	function require(file, callback) {
	var script = document.createElement("script");
	// IE
	script.onreadystatechange = function() {
	  if (script.readyState === 'loaded' || script.readyState === 'complate') {
	    script.onreadystatechange = null;
	    callback();
	  }
	};
	script.onload = function() {
	  callback();
	}
	script.src = "xx.js";
	  document.getElementsByTagName('head')[0].appendChild(script)
	}
	```
3. 预加载JavaScript
	```javascript
	var preload;
	if ( /*@cc_on!@*/ false) {
	  preload = function(file) {
	    new Image().src = files;
	  };
	} else {
	  preload = function(file) {
	    var obj = document.createElement('object'),
	      body = document.body;
	    obj.width = 0;
	    obj.height = 0;
	    obj.data = file;
	    body.appendChild(obj)
	  }
	}
	```
