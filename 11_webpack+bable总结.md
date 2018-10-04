# Webpack
	1 多页面应用程序
		const config = {
			entry: {
				pageOne: './src/pageOne/index.js',
				pageTwo: './src/pageTwo/index.js',
				pageThree: './src/pageThree/index.js'
			}
		};
		· 这是什么？我们告诉 webpack 需要 3 个独立分离的依赖图（如上面的示例）。
		· 为什么？在多页应用中，（译注：每当页面跳转时）服务器将为你获取一个新的 HTML 文档。页面重新加载新文档，并且资源被重新下载。然而，这给了我们特殊的机会去做很多事：使用 CommonsChunkPlugin 为每个页面间的应用程序共享代码创建bundle。由于入口起点增多，多页应用能够复用入口起点之间的大量代码/模块，从而可以极大地从这些技术中受益。
	2 loader 
		1) 简介
			· loader 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。loader 可以将所有类型的文件转换为 webpack 能够处理的有效模块。 (import引入文件)
			· loader 被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能极其强大，可以用来处理各种各样的任务  (new操作符创建实例)
			· 当链式调用多个 loader 的时候，请记住它们会以相反的顺序执行。取决于数组写法格式，从右向左或者从下向上执行。
		2) API
			· 所谓loader只是一个导出为函数的JavaScript模块。loader runner 会调用这个函数，然后把上一个loader产生的结果或者资源文件(resource file)传入进去。函数的this上下文将由webpack填充，并且loader runner具有一些有用方法，可以使loader改变为异步调用方式，或者获取query参数。
			· 第一个loader的传入参数只有一个：资源文件(resource file)的内容。compiler需要得到最后一个loader产生的处理结果。这个处理结果应该是String 或者 Buffer，代表了模块的JavaScript源码。另外还可以传递一个可选的SourceMap结果(格式为JSON对象)
			· 如果是单个处理结果，可以在同步模式中直接返回。如果有多个处理结果，则必须调用this.callback()。在异步模式中，必须调用this.async()，来指示loader runner等待异步结果，它会返回this.callback()回调函数，随后loader必须返回undefined并且调用该回调函数。
	3 plugin
		· webpack 插件是一个具有 apply 属性的 JavaScript 对象。apply 属性会被 webpack compiler 调用，并且 compiler 对象可在整个编译生命周期访问。
	4 Node.js API (参考loader的例子)
		1) webpack()
			导入的wepack函数需要传入一个webpack配置对象，当同时传入回调函数时就会执行webpack compiler
		2) compiler 实例
			· compiler 实例提供了run(callback) 、 watch(watchOptions, handler) 方法。
			· run()用于触发所有编译时工作，完成之后，执行给定的callback函数，最终记录下来的概括信息(stats)和错误，应该在这个callback函数中获取
			· stats对象会被作为webpack()回调函数的第二个参数传入，可以通过它获取到代码编译过程中的有用信息，包括错误和警告(如果有的话), 计时信息 ， module 和 chunk 信息。 stats.toJson(option) 
		3)自定义文件系统
			默认情况下，webpack使用普通文件系统来读取文件并将文件写入磁盘。但是，还可以使用不同类型的文件系统(内存(memory),webDAV等)来更改输入输出行为。为了实现这一点，可以改变inputFileSystem或outputFileSystem。 例如，可以使用memory-fs替换默认的outputFileSystem,以将文件写入到内存中，而不是写入到磁盘。
	5 模块方法(module methods)
		1) ES6(推荐)
			· import 
				通过import 以静态的方法，导入另一个通过export 导出的模块
					eg:
						import MyModule form './my-module.js'     // export default
						import { NamedExport } from './other-module.js'   //export 
			· export
					默认导出整个模块，或具名导出模块
						eg:
							export var count = 5;
							export function test(){}
							var count = 5
							export default count   // 默认导出
			· import()
				动态地加载模块，调用import()之处，被作为分离的模块起点，意思是，被请求的模块和它引用的所有子模块，会分离到一个单独的chunk中。
				import()特性依赖于内置的Promise。如果想在低版本浏览器使用import()，记得使用像es6-promise 或promise-polyfill这样的polyfill库，来预先填充Promise环境。
				import规范不允许控制模块的名称或者其他属性，因为"chunks"只是webpack中的一个概念，幸运的是，webpack中可以通过注释接收一些特殊的参数，而无须破坏规定： 
					import(
						/*webpackChunkName:"my-chunk-name"*/
						/*webpackMode:"lazy"*/
						'module'
					)
		2) CommonJS
			· CommonJS致力于为浏览器之外的JavaScript指定一个生态系统。
			· require.ensure是wepack特有的，已经被import()取代。
				require.ensure(dependencies:String[],callback:function(require),errorCallback:function(error),chunkName:String)
			· 给定dependencies参数，将其对应的文件，将其对应文件拆分到一个单独的bundle中，此bundle会被异步加载。
				eg:
					require.ensure(['b'].function(require){
						var c = require('c')
						},'index')    
			· 当使用CommonJS模块语法时，这是动态加载依赖的唯一方法。
		3) AMD
			· AMD(Asynchronous Module Definition)是一种定义了写入模块接口和加载模块接口的JavaScript规范
			· define (通过factory / value 导出)
			· require (AMD版本)
				与require.ensure类似, 给定dependenices参数，将其对应的文件拆分到一个单独的bundle中，此bundle会被异步加载。然后会调用callback回调函数，并入dependencies数组中的每一项的导出。
					eg:
					 require(['b'],function(b){
							var c = require("c")
						})   // 这里没有提供命名chunk名称的选项

# babel
	1 babel-polyfill
		Babel 默认只转换新的JavaScript句法(syntax)，而不会转换新API，比如，Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign）都不会转码。