# webpack + babel 配置  (webpack1.x -> 2.x -> 4.x)
### 提取第三方库
1. webpack1.x
	1) plguins
		```javascript
		new webpack.optimize.CommonsChunkPlugin('vendors', 'vendors.js', Infinity)
		```
	2) 入口文件配置, 加入vendors 如下：
		```javascript
		entry: {
		  bundle: './index.js',
		  vendors: ['react', 'react-dom']
		}
		```
	3) html文件引入
		<br> ` vendors.js`及`bundle.js`
2. 2.x 版本
	1) plugins
		```javascript
		new webpack.optimize.CommonsChunkPlugin({
		  name: 'vendor',
		  minChunks: function(module) {
		    return module.context &&  module.context.indexOf('node_modules') !== -1;
		  }
		}),
		new webpack.optimize.CommonsChunkPlugin({
		  name: 'manifest'
	  })
		```
	2) 入口文件
		```javascript
		entry: {
		  app: './src/index.js'
		}
		```
	3. html文件引入
		<br> ` manifest.js`及`vendor.js`
3. 4.x 版本
	1) plugins
		```javascript
		new webpack.optimize.SplitChunksPlugin({
		  cacheGroups: {
		    default: {
		      minChunks: 2,
		      priority: -20,
		      reuseExistingChunk: true,
		    },
		    //打包重复出现的代码
		    vendor: {
		      chunks: 'initial',
		      minChunks: 2,
		      maxInitialRequests: 5, // The default limit is too small to showcase the effect
		      minSize: 30000, // This is example is too small to create commons chunks
		      name: 'vendor'
		    },
		    // 合并css
		    styles: {
		      name: 'styles',
		      test: /\.scss|css$/,
		      chunks: 'all', // merge all the css chunk to one file
		      enforce: true
		    }
		  }
		}),
		new webpack.optimize.RuntimeChunkPlugin({
		  name: "manifest"
		})
		```
	2) 入口配置
		```javascript
		entry: {
		  app: './src/index.js'
		}
		```
	3) html文件引入
		+ 加载js文件 ` manifest.js`及`app.js`
		+ 若css分离，则加载 `style.css`
### 提取css文件
1. webpack1.x
	1) plugins
		```javascript
		var ExtractTextPlugin = require("extract-text-webpack-plugin");
		new ExtractTextPlugin("bundle.css")
		```
	2) `module.loaders`
		```javascript
		{
		  test: /\.(scss|css)$/,
		  loader: ExtractTextPlugin.extract("style-loader", "css-loader!postcss-loader!sass-loader")
		}
		```
	3) html文件引入
		<br> 引入打包后的css文件, `bundle.css`
2. 2.x 版本
	1) plugins 及 html文件引入同1.x 版本
	2) `module.rules`
		```javascript
		{
		  test: /\.(scss|css)$/,
		  loader: ExtractTextPlugin.extract({
		    fallback: 'style-loader',
		    use: 'css-loader!postcss-loader!sass-loader',
		  })
		}
	```
3. 4.x 版本
	1) plugins
		```javascript
		const MiniCssExtractPlugin = require("mini-css-extract-plugin")
		new MiniCssExtractPlugin({
		  filename: "[name].[chunkhash:8].css",
		  chunkFilename: "[id].css"
		})
		// SplitChunksPlugin 中的cacheGroups 加入
		styles: {
		  name: 'styles',
		  test: /\.scss|css$/,
		  chunks: 'all', // merge all the css chunk to one file
		  enforce: true
		}
		```
	2) `module.rules`
		```javascript
		{
		  test: /\.(scss|css)$/,
		  use: [
		    { loader: MiniCssExtractPlugin.loader,
		      options:{
		        publicPath: './'  // 背景图片路径问题
		      }            
		    },
		    { loader: "css-loader" },
		    { loader: "postcss-loader" },
		    { loader: "sass-loader" }
		  ]
		}
		```
	3) html文件引入css
		<br> 引入打包后的css文件, `styles.css`
### css自动补全
+ 使用autoprefixer 、postcss-loader；postcss-loader要放在css-loader之后， sass-loader之前
1. webpack1.x
	1) module.exports
		```javascript
		postcss: [autoprefixer({ browsers: ['last 2 versions'] })]
		```
2. 2.x 版本
	1) plugins
		```javascript
		new webpack.LoaderOptionsPlugin({
		  options: {
		    postcss: function() {
		      return [require('autoprefixer')];
		    }
		  }
		})
		```
3. 4.x 版本
	1) 额外配置文件 `postcss.config.js`
		```javascript
		module.exports = {
		  plugins: [
		    require("autoprefixer")({ browsers: ['last 2 versions'] })
		  ]
		};
		```
	2) postcss的options配置
		```javascript
		{
		  loader: "postcss-loader",
		  options: {
		    plugins: [require("autoprefixer")({ browsers: ['last 2 versions'] })]
		  }
		}
		```
### 生产环境压缩
1. webpack1.x
	```javascript
	plugins.push(
	  new webpack.optimize.UglifyJsPlugin({
	    output: {
	      comments: false, // remove all comments
	    },
	    compress: {
	      warnings: false
	    }
	  })
	)
	```
2. 2.x 版本
	```javascript
	new webpack.optimize.UglifyJsPlugin({
	  compress: {
	    warnings: false
	  }
	})
	```
3. 4.x 版本
	+ 没有UglifyJsPlugin， 默认生产环境已经压缩，但是需要指定环境
	+ module.exports
		```javascript
		mode: 'development'  //要指定环境
		```
### 路由动态加载
1. webpack 1.x 、react-router 2.4.1 、 react 15.3.1 、 add-module-exports
	1) output配置
		```javascript
		output: {
		  path: './dist',
		  publicPath: 'dist/',
		  filename: '[name].js',
		  chunkFilename: 'chunk.[name].js', // 路由的按需加载需要配置这一项
		},
		```
	2) loader：`add-module-exports`
	3) 路由 `require.ensure()`方式
		+ routes.js
			```javascript
			const routes = {
			  childRoutes: [
			    {
			      path: '/',
			      component: require('./containers/Container.js'),
			      indexRoute: {
			        getComponent(nextState, callback) {
			        require.ensure([], require => {
			          callback(null, require('./containers/App.js'))
			        }, 'app')
			        }
			      },
			      childRoutes: [{
			        path: 'Main/:pid',
			        component: require('./containers/Main.js'),
			        indexRoute: {
			          getComponent(nextState, callback) {
			            require.ensure([], require => {
			            callback(null, require('./containers/Index.js'))
			            }, 'index')
			          }
			        }]
			      }]
			    }
			  ]
			}
			```
		+ app入口文件配置
			```javascript
			import routes from './src/routes'   // 引入路由配置
			render(
			  <Provider store={store}>
			    <Router  routes={routes}  history={hashHistory} />
			  </Provider>,
			  document.getElementById('app')
			)
		```
2. webpack 2.x 、react-router 4.x 、 react 15.6.x
		```javascript
		const asyncComponent = loadComponent => (
		  class AsyncComponent extends React.Component {
		    state = {
		      Component: null,
		    }
		    componentWillMount() {
		      if (this.hasLoadedComponent()) {
		        return;
		      }
		      loadComponent()
		        .then(module => module.default)
		        .then((Component) => {
		          this.setState({ Component });
		        })
		        .catch((err) => {
		          console.error(`Cannot load component in <AsyncComponent />`);
		          throw err;
		        });
		    }
		    hasLoadedComponent() {
		      return this.state.Component !== null;
		    }
		    render() {
		      const { Component } = this.state;
		      return (Component) ? <Component { ...this.props }/> : null;
		    }
		  }
		);
		// react-router 4
		const Index = asyncComponent(() => import(/* webpackChunkName: "index" */ "containers/index.js"))
		const Personal = asyncComponent(() => import(/* webpackChunkName: "personal" */ "containers/personal.js"))
		const Recruit = asyncComponent(() => import(/* webpackChunkName: "recruit" */ "containers/recruit.js"))
		<Router>
		  <div>
		    <Route exact path="/" component={Index} />
		    <Route  path="/list/:type" component={Index} />
		    <Route exact path="/recruit" component={Recruit} />
		    <Route  path="/recruit/:id" component={Recruit} />
		    <Route  path="/personal" component={Personal} />
		  </div>
		</Router>
		```
3. `4.x` 版本
		<br>和`2.x` 相同

### module的配置
1. `webpack 1.x`
	```javascript
	module: {
		loaders: [
			{
				test: /\.js$/,
				exclude: /node_modules/,
				loader: 'babel'
			},
			{...}
		]
	}
	```
2) 2.x 版本
	```javascript
	module: {
		rules: [
			{
				test: /\.(js|jsx)$/,
				use: ['babel-loader'],
				exclude: /node_modules/,
				include: path.join(__dirname, 'src')
			},
			{...}
		]
	}
	```
3) 4.x 版本
	<br>同2.x 相同
______________
# react    (react 15 -> 16 + )
### 生命周期

### context


### ref


### render()


### ReactDOM

————————————————————————————————
# react-router  2.x  -->  4.x
### Router/Route 的改变
1. react-router 2.x
	```javascript
	import { Router, Route, IndexRoute, hashHistory } from 'react-router'
	<Router history={hashHistory}>
	  <Route path='/' component={App} />
	  <Route path='/about' component={About} />
	</Router>
	```
2. react-router 4.x
	```javascript
	import { HashRouter as Router, Route } from 'react-router-dom';
	// V4 Router组件里只能渲染一个组件
	<Router>
	  <div>
	    <Route path='/' component={App} />
	    <Route path='/about' component={About} />
	  </div>
	</Router>
	```
### 嵌套路由
1. react-router 2.x
	```javascript
	<Router history={hashHistory}>
	  <Route path="/" component={App}>
	    <IndexRoute component={Home}/>
	    <Route path="/repos" component={Repos}/>
	    <Route path="/about" component={About}/>
	  </Route>
	</Router>
	/*
	  IndexRoute就是解决这个问题，显式指定Home是根路由的子组件，即指定默认情况下加载的子组件.
	  使用 this.props.children 来嵌套子组件
	*/
	```
2. react-router 4.x
	```javascript
	import { HashRouter as Router, Route, Switch } from 'react-router-dom';
	<Router>
	 <Route path="/" component={(props) => (
	    <App {...props}>
	      <Switch>
	        <Route path='/repos' component={Repos} />
	        <Route path='/about' component={About} />
	      </Switch>
	    </App>
	  )}/>
	</Router>
	/*
	  v4 中已经没有<IndexRoute>了，但可以使用<Route exact>来达到同样的效果
	  <Switch> 来启用排他路由, 只需要在路由列表里匹配一个路由
	*/
	```
### props.params
1. react-router 2.x
	`this.props.params.paramName`
2. react-router 4.x
	`this.props.match.params.paramName`
### location.query
1. react-router 2.x
	`this.props.location.query`
2. react-router 4.x
	+ V4 去掉了location.query，只能使用search来获取，为了让其跟浏览器一样
	+ this.props.location.search
### 路由的跳转
1. react-router 2.x
	```javascript
	// context对象
	export default class extends Component{
	  static contextTypes: {
	    router: React.PropTypes.object
	  }
	  handleSubmit(event) {
	    this.context.router.push(path)
	  }
	}
	```
2. react-router 4.x
	```javascript
	import { withRouter } from 'react-router'
	@withRouter
	class Home extends Component{
	  handleSubmit(event) {
	    this.props.history.push(path)
	  }
	}
	/*
	  这个组件没有由<Route>直接渲染，那么我们可以使用withRouter()高阶组件。
	*/
	```
### Link
1. react-router 2.x
	```javascript
	import { Link} from 'react-router'
	// 高亮颜色
	<Link to="/about" activeClassName="active">About</Link>
	```
2. react-router 4.x
	```javascript
	import { Link} from 'react-router-dom'
	// Link 不再支持 activeClassName
	import { NavLink } from 'react-router-dom'
	<NavLink to="/about" activeClassName="active">About</NavLink>
	```
### 路由勾子
1. react-router 2.x
	+ onEnter 、 Leave
	```javascript
	const requireAuth = (nextState, replace) => {
	  if (!auth.isAdmin()) {
	    // Redirect to Home page if not an Admin
	    replace({ pathname: '/' })
	  }
	}
	export const AdminRoutes = () => {
	  return (
	    <Route path="/admin" component={Admin} onEnter={requireAuth} />
	  )
	}
	```
2. react-router 4.x
	+ 在react-router V4中去掉了on****的路由生命周期的钩子，但是你可以在组件中用componentDidMount 或 componentWillMount代替 onEnter，可以用componentWillUpdate 或 componentWillReceiveProps代替 onUpdate，你可以用componentWillUnmount代替 onLeave。
### 总结
+ React Router 2路由集中在一个地方。布局和页面嵌套是通过 <Route> 组件的嵌套而来的。布局和页面组件是完全纯粹的，它们是路由的一部分。
+ React Router 4不再主张集中式路由了。相反，路由规则位于布局和 UI 本身之间

# webpack 其他
1 当前环境
	//判断当前运行环境是开发模式还是生产模式
	const nodeEnv = process.env.NODE_ENV || 'development';
	const isPro = nodeEnv === 'production';
	console.log("当前运行环境：", isPro ? 'production' : 'development')
	new webpack.DefinePlugin({
		'process.env': {
				'NODE_ENV': JSON.stringify(nodeEnv)
		}
	})
	// package.json
		"scripts": {
			"dev": "set NODE_ENV=development&& webpack-dev-server --config webpack.config.js",
			"build": "set NODE_ENV=production&& webpack -p --config webpack.config.js"
		}
2 多入口页面配置
3 热替换  
	1) node + express + webpack-dev-middleware + webpack-hot-middleware
	2) webpack-dev-server
	webpack1.x  2.x 4.x 相同
		// plugins 配置
		new webpack.HotModuleReplacementPlugin()
		// package.json
		"scripts": {
			"dev": "set NODE_ENV=development && webpack-dev-server --config webpack.config.js --port 80",
			"build": "set NODE_ENV=production&& webpack -p --config webpack.config.js"
		},
	3) 区别
4 babel中所用的loader及其作用
5 .babelrc文件 配置  
	{
		presets: ['es2015', 'stage-2', 'react'],
		plugins: [
			'transform-decorators-legacy',
			"transform-class-properties",
			'transform-runtime',
			["import", { libraryName: "antd", style: "css" }],  // antd按需
			'add-module-exports'  // 路由动态加载
		]
	}
6 dependencies 和  devDependencies
	· 在 package.json 文件里面提现出来的区别就是，使用 --save-dev 安装的 插件，被写入到 devDependencies 对象里面去
	· 而使用 --save 安装的插件，责被写入到 dependencies 对象里面去。
	那 package.json 文件里面的 devDependencies  和 dependencies 对象有什么区别呢？
	· devDependencies  里面的插件只用于开发环境，不用于生产环境
	· dependencies  是需要发布到生产环境的
	· 我们在发布的时候，可以将dependencies里的所有包，打包成一个vendor.js文件，因为这个文件都是三方库，代码几乎不太会改变，这样，这部分代码就能很好的被游览器缓存利用了

# react  （15.x -> 15.6-> 16.3）
1 组件、高阶组件
2 数据流 state props
3 setState
4 事件
5 生命周期
6 优化
	immutable
	shouldComponentUpdate()
	key
7 react 16 新特性
	了解，但还未使用状态。
	语法上并没有什么区别

# 路由
	1 路由按需加载 + 组件按需加载
	2 路由v4原理
	3 js原生实现路由
		1) '#' 形式
			window.onload = function(){}
			window.onhashchange()  
		2) historyapi
			window.onpopstate = function(){}
			window.onload = function(){}
			history.replaceState()
			history.pushState()

	4 路由缓存页面数据

# redux
	1 redux 原理   actions  reducer
	2 异步请求 、跨域  nginx 配置  
		fetch  封装
		promise  -> async await

# 注意点
	1 页面白屏
		授权问题   
		页面解耦
	2 页面跳转缓存路由信息


# node 接口  （从koa1.x  --> koa2.x）
	1 koa
	2 路由 koa-router
	3 mysql
	4 启动  pm2
	5 测试 postman
	6 升级
		generator yield -> async await
		this  -> ctx
