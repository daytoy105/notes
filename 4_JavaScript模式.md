# 基本技术
1. 尽量少用全局变量
  <br> 全局变量的问题在于它们在整个JavaScript应用或Web页面内共享。它们生存于同一个全局命令空间内，总有可能发生命令冲突。
2. for循环
3. for-in循环
4. 不要增加内置的原型
5. **避免使用eval()**
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
