# 初入React世界
### react简介
1. 专注视图层
	+ React并不是完整的MVC/MVVM框架，它专注于提供清晰、简洁的View（视图）层解决方案。而又与模板引擎不同，React不仅专注于解决View层的问题，又是一个包括View和Controller的库。对于复杂的应用，可以根据应用场景自行选择业务层框架，并根据需要搭配React-Router、Redux等来使用。
2. Virtual DOM
	+ React把真实DOM树转换成JavaScript对象树，也就是Virtual DOM。
	+ 每次数据更新后，重新计算Virtual DOM，并和上一次生成的Virtual DOM做对比，对发生变化的部分做批量更新。React也提供了直观的shouldComponentUpdate生命周期回调，来减少数据变化后不必要的Virtual DOM对比过程，以保证性能。
3. 函数式编程
	+ 函数式编程，对应的是声明式编程，它是人类模仿自己逻辑思考方式发明出来的。声明式编程的本质是lambda演算。试想当我们操作数组的每个元素并返回一个新数组时，如果是计算机的思考方式，则是需要一个新数组，然后遍历原数组，并计算赋值；如果是人的思考方式，则是构建一个规则，这个过程就变成构建一个f函数作用在数组上，然后返回新数组。这样，计算可以被重复利用。
	+ React把过去不断重复构建UI的过程抽象成了组件，且在给定参数的情况下约定渲染对应的UI界面。React能充分利用很多函数式方法去减少冗余代码。此外，由于它本身就是简单函数，所以易于测试。可以说，函数式编程才是React的精髓。
### JSX语法
1. JSX的由来
	+ JSX与React有什么关系呢？简单来讲，React为方便View层组件化，承载了构建HTML结构化页面的职责。从这点上来看，React与其他JavaScript模板语言有着许多异曲同工之处，但不同之处在于React是通过创建与更新虚拟元素(virtual element)来管理整个Virtual DOM的。
	+ 其中，虚拟元素可以理解为真实元素的对应，它的构建与更新都是在内存中完成的，并不会真正渲染到DOM中去。在React中创建的虚拟元素可以分为两类，DOM元素(DOM element)与组件元素(component element)，分别对应着原生DOM元素与自定义元素。
	1) DOM元素
		```javascript
		{
		  type: 'button',
		  props: {
		    className: 'btn btn-blue',
		    children: [{
		      type: 'em',
		      props: {
		        children: 'Confirm'
		      }
		    }]
		  }
		}
		```
    + 在React中，到处都是可以复用的元素，这些元素并不是真实的实例，它只是让React告诉开发者想要在屏幕上显示什么。我们无法通过方法去调用这些元素，它们只是不可变的描述对象。
	2) 组件元素
		```javascript
		const Button = ({ color, text }) => {
		  return {
		    type: 'button',
		    props: {
		      className: `btn btn-${color}`,
		      children: {
		        type: 'em',
		        props: {
		          children: text,
		        },
		      },
		    },
		  };
		}
		/*自定义构建的元素*/
		```
2) JSX基本语法
	+ JSX的官方定义是类XML语法的ECMAScript扩展。
	+ JSX将HTML语法直接加入到JavaScript代码中，再通过翻译器Babel转换到纯JavaScript后由浏览器执行。
	+ 元素属性
		+ for  -> htmlFor
		+ class -> className
	+ html转义：
		 `<div dangerouslySetInnerHTML={{__html:'&copy; 2018'}}/>`
3) element
	+ 元素是React中最小的构建单元。不同于浏览器的DOM元素，React的元素是一个普通对象，描述了对于一个DOM节点或者组件，在屏幕上呈现的样子。创建一个React元素的成本很低，使用React.createElement()。元素创建之后是不可变的。
	+ React.createElement(type, props, children)，返回React元素,其中type的类型：string类型的标签、组件如(class/function)、Fragment。
	+ React.cloneElement(type, props, children)，复制并返回React元素，其中type的类型为React元素(element)。 key和ref也将会一起拷贝。
### React组件
+ 组件封装的基本思路就是面向对象思想。
+ React组件基本上由3个部分组成——属性(props)、状态(state)以及生命周期方法。
+ React的所有组件都继承自顶层类React.Component。它的定义非常简洁，只是初始化了React.Component方法，声明了props、context、refs等，并在原型上定义了setState和forceUpdate方法。
+ 一个组件的创建可以通过多种方式声明，可以是带有一个render()方法的类，也可以是一个函数，这两种情况下，它都把属性props作为输入，把返回的一棵元素树作为输出显示在屏幕上。
1. ES6 classes
	```javascript
	import React, { Component } from 'react';
	class Button extends Component {
	  constructor(props) {
	    super(props);
	  }
	  static defaultProps = {
	    color: 'blue',
	    text: 'Confirm',
	  };
	  render() {
	    const { color, text } = this.props;
	    return (
	      <button className={`btn btn-${color}`}>
	        <em>{text}</em>
	      </button>
	    );
	  }
	}
	```
2. 无状态函数
	```javascript
	const Button = ({ color = 'blue', text = 'Confirm' }) => (
	<button className={`btn btn-${color}`}>
	  <em>{text}</em>
	  </button>
	)
	```
	+ 无状态组件只传入props和context两个参数；也就是说，它不存在state，也没有生命周期方法，组件本身即是classes方式构建方法中的render方法。
	+ 在适合的情况下，我们都应该且必须使用无状态组件。无状态组件调用时不会创建新实例，它创建时始终保持了一个实例，避免了不必要的检查和内存分配，做到了内部优化。
3. 条件渲染
	1) 元素变量方式
		```javascript
		render(){
		  var ele = ''
		  if (isLoggedIn) {
		    ele = <UserGreeting />;
		  }else{
		    ele = <GuestGreeting />;
		  }
		  return(
		    <div>
		      { ele }
		    </div>
		  );
		}
		```
	2) 行内判断的方式 &&
		```javascript
		render(){
		  var data = ['React', 'Re: React', 'Re:Re: React'];
		  return(
		    <div>
		      {
		        data.length &&
		        <h2>You have {data.length} unread messages.</h2>
		      }
		    </div>
		  );
		}
		```
	3) 行内判断的方式： 三元判断
		```javascript
		render(){
		  return(
		    <div>
		      {
		        data.length?
		        <h2>You have {data.length} unread messages.</h2>
		        :null
		      }
		    </div>
		  );
		}
		```
### React数据流
+ 在React中，数据是`自顶向下`单向流动的，即从父组件到子组件。这条原则让组件之间的关系变得简单且可预测。
1. state
	+ 当组件内部使用库内置的setState方法时，最大的表现行为就是该组件会尝试重新渲染。
	+ setState是一个异步方法，一个生命周期内所有的setState方法会合并操作。
	+ 组件内部的私有变量，受组件控制。state对象中的数据，渲染和数据流相关的。如`var this.tm = setTimeout()`中的`tm`等与`渲染`或`数据流`无关的变量，不必放在state中。
2. props
	+ props是React用来让组件之间互相联系的一种机制。
	+ 在propTypes支持的基本类型中，函数类型的检查是propTypes.func，对于布尔类型的检查是propTypes.bool。
	+ state和props的更新是异步方式的，所以不能用它们直接计算出下一个state或props，需要把它们放在一个方法内。
		```javascript
		// Wrong
		this.setState({
		  counter: this.state.counter + this.props.increment,
		});
		// Correct
		this.setState((state, props) => ({
		  counter: state.counter + props.increment
		}));
		```
	+ Render Props
		+ 组件中的render()方法返回一个React元素并调用，常见的有React-Router。
		+ 它能够动态决定什么需要渲染的，并有效地改变它的渲染结果。
		```javascript
		render(){
		  return (
		    <Mouse render={mouse => (
		      <Cat mouse={mouse} />
		    )}/>
		  )
		}
		```
		+ 和PureComponent使用时要注意，若<Mouse>组件继承自PureComponent，则当前组件调用时，每次都会重新渲染render函数，<Mouse>组件的shouldComponentUpdate将返回true。解决方法，可将props定义为实例方法。
		```javascript
		class MouseTracker extends React.Component {
		  constructor(props) {
		    super(props);
		    this.renderTheCat = this.renderTheCat.bind(this);
		  }
		  renderTheCat(mouse) {
		    return <Cat mouse={mouse} />;
		  }
		  render() {
		    return (
		      <Mouse render={this.renderTheCat} />
		    );
		  }
		}
		```
### React生命周期
&ensp;&ensp;&ensp;&ensp;React组件的生命周期根据广义定义描述，可以分为挂载、渲染和卸载这几个阶段。当渲染后的组件需要更新时，我们会重新去渲染组件，直至卸载。因此，我们可以把React生命周期分成两类：
1) 当组件在挂载或卸载时；
2) 当组件接收新的数据时，即组件更新时。
### React与DOM
+ ReactDOM中的API非常少，只有findDOMNode、unmountComponentAtNode和render
1. findDOMNode
	+ DOM真正被添加到HTML中的生命周期方法是componentDidMount和componentDidUpdate方法。在这两个方法中，我们可以获取真正的DOM元素。
	+ React提供的获取DOM元素的方法有两种，其中一种就是ReactDOM提供的findDOMNode。
2. render
	+ 为什么说只有在顶层组件我们才不得不使用ReactDOM呢？这是因为要把React渲染的Virtual DOM渲染到浏览器的DOM当中，就要使用render方法。
		```javascript
		ReactDOM.render(
		  <div>MyComponent</di>,
		   document.getElementById('app')
		);
		```
	+ 当组件在初次渲染之后再次更新时，React不会把整个组件重新渲染一次，而会用它高效的DOM diff算法做局部的更新。
3. refs
	+ 表示为对组件真正实例的引用，其实就是ReactDOM.render()返回的组件实例。
	+ 但在组件内，JSX是不会返回一个组件的实例的，refs就是为此而生的，它是React组件中非常特殊的prop，可以附加到任何一个组件上，组件被调用时会新建一个该组件的实例，而refs就会指向这个实例。
	+ ref的使用场景
		+ 操作focus事件、text文本选择、媒体播放事件
		+ 触发动画
		+ 与第三方库的集合(如jq)
	1) refs可以是一个回调函数，这个回调函数会在组件被挂载后立即执行
		```javascript
		import React, { Component } from 'react';
		class App extends Component {
		  constructor(props){
		    super(props);
		    this.handleClick = this.handleClick.bind(this);
		  }
		  handleClick() {
		    if (this.myTextInput !== null) {
		      this.myTextInput.focus();
		    }
		  }
		  render() {
		    return (
		      <div>
		        <input type="text" ref={(ref) => this.myTextInput = ref} />
		        <input type="button" value="Focus the text input" onClick={this.handleClick} />
		      </div>
		    );
		  }
		}  // 这种方式 ，会被调用两次，首先是null, 然后才是DOM元素
		// 方式二
		import React, { Component } from 'react';
		class App extends Component {
		  constructor(props){
		    super(props);
		    this.handleClick = this.handleClick.bind(this);
		    this.myTextInput = null;
		    this.setTextInputRef = element => {
		      this.myTextInput = element;
		    };
		  }
		  handleClick() {
		    if (this.myTextInput !== null) {
		      this.myTextInput.focus();
		    }
		  }
		  render() {
		    return (
		      <div>
		        <input type="text" ref={this.setTextInputRef} />
		        <input type="button" value="Focus the text input" onClick={this.handleClick} />
		      </div>
		    );
		  }
		}
		/* 这种方式 ，会被调用两次，首先是null, 然后才是DOM元素 */
		```
	2) refs同样支持字符串。对于DOM操作，不仅可以使用findDOMNode获得该组件DOM，还可以使用refs获得组件内部的DOM。
		```javascript
		import React, { Component } from 'react';
		import ReactDOM from 'react-dom';
		class App extends Component {
		  componentDidMount() {
		    //myComp是Comp的一个实例，因此需要用findDOMNode转换为相应的DOM
		    const myComp = this.refs.myComp;
		    const dom = ReactDOM.findDOMNode(myComp);
		  }
		  render() {
		    return (
		      <div>
		        <Comp ref="myComp" />
		      </div>
		    );
		  }
		}
		```
4. React之外的DOM操作
<br>&ensp;&ensp;&ensp;&ensp;React的声明式渲染机制把复杂的DOM操作抽象为简单的state和props的操作，因此避免了很多直接的DOM操作。不过，仍然有一些DOM操作是React无法避免或者正在努力避免的。举一个明显的例子，如果要调用HTML5 Audio/Video的play方法和input的focus方法，React就无能为力了，这时只能使用相应的DOM方法来实现。
_______
# 漫谈React
### 事件系统
+ Virtual DOM在内存中是以对象的形式存在的，如果想要在这些对象上添加事件，就会非常简单。
1. 合成事件的绑定方式
	 <br>` <p onClick={this.handleClick.bind(this)}>点击</p>`
2. 合成事件的实现机制
	1) 事件委派
		+ react并不会把事件处理函数直接绑定到真实的节点上，而是把所有事件绑定到结构的最外层，使用一个统一的事件监听器，这个事件监听器上维持了一个映射来保存所有组件内部的事件监听和处理函数。
		+ 当组件挂载或卸载时，只是在这个统一的事件监听器上插入或删除一些对象；当事件发生时，首先被这个统一的事件监听器处理，然后在映射里找到真正的事件处理函数并调用。这样做简化了事件处理和回收机制，效率也有很大提升。
	2) 自动绑定
		+ bind方法
			<br>  `<div onClick={this.handleClick.bind(this, id)}/>`
		+ 双冒号 ::
			<br>只绑定，`不传参 `
			<br> ` <div onClick={::this.handleClick}/>`
		+ 构造器内声明
			<br>这种绑定方式的好处在于仅需要进行一次绑定，而不需要每次调用事件监听器时去执行绑定操作
			```javascript
			class Test extends component{
			  constructor(proos){
			    super(props);
			    this.state = {};
			    this.handleClick = this.handleClick.bind(this)
			  }
			  render(
			    <div onClick={this.handleClick} />
			  )
			}
			```
		+ 箭头函数 自动绑定此函数作用域的this
			<br>`<div onClick={(e) => this.handleClick(id, e)} />`：这种方式若是作为回调props向子组件传递，可能会导致不必要的重渲染。同时与bind的方法相比，需要显示的传递event对象
		+ 在class上绑定this的方式：`不能传递参数`
			```javascript
			class Test extends component{
			  handleClick = () =>{}
			  render(
			    <div onClick={this.handleClick} />
			  )
			}
			```
3. 在React中使用原生事件
	+ 在React中使用DOM原生事件时，一定要在组件卸载时手动移除，否则很可能出现内存泄漏的问题。而使用合成事件系统时则不需要，因为React内部已经帮你妥善地处理了
	+ React合成事件 VS 原生事件
		1) React的合成事件则并没有实现事件捕获，仅仅支持了事件冒泡机制
		2) 在React合成事件中，没有兼容性问题。(阻止事件默认用preventDefault(),获取事件对象event)
4. 内存泄漏：
	<br>不再用到的内存，没有及时释放，就叫做内存泄漏(memory leak)
	<br>Chrome浏览器查看内存占用：
	1) 打开开发者工具，选择Timeline面板
	2) 在顶部的Capture字段里面勾选Memory
	3) 点击左上角的录制按钮。
	4) 在页面上进行各种操作，模拟用户的使用情况。
	5) 一段时间后，点击对话框的stop按钮，面板上就会显示这段时间的内存占用情况。
		+ 平稳  -> 没有泄漏
		+ 不平稳 -> 内存泄漏
### 表单
1. 受控组件
	+ 每当表单的状态发生变化时，都会被写入到组件的state中，这种组件在React中被称为受控组件(controlled component)。在受控组件中，组件渲染出的状态与它的value或checked prop相对应。React通过这种方式消除了组件的局部状态，使得应用的整个状态更加可控。
	+ React受控组件更新state的流程：
		1) 可以通过在初始state中设置表单的默认值。
		2) 每当表单的值发生变化时，调用onChange事件处理器。
		3) 事件处理器通过合成事件对象e拿到改变后的状态，并更新应用的state。
		4) setState触发视图的重新渲染，完成表单组件值的更新。
	+ 在React中，数据是单向流动的。表单的数据源于组件的state，并通过props传入，这也称为单向数据绑定。然后，我们又通过onChange事件处理器将新的表单数据写回到组件的state，完成了双向数据绑定。
2. 非受控组件
	+ 简单地说，如果一个表单组件没有value props(单选按钮和复选框对应的是 checked prop)时，就可以称为非受控组件。相应地，你可以使用defaultValue和defaultChecked prop来表示组件的默认状态。
	+ 在React中，非受控组件是一种反模式，它的值不受组件自身的state或props控制。通常，需要通过为其添加ref prop来访问渲染后的底层DOM元素。
### 组件间通信
1. 父组件向子组件通信
	<br>`props`
2. 子组件向父组件通信
	1) 回调函数
		<br>父组件向子组件传递回调函数
			<br>`	<Sub onChange={()=>this.handleChange()}/>`
		<br>子组件
			<br>`	this.props.onChange(value)`
	2) 利用自定义事件机制
3. 跨级组件通信
	+ 当需要让子组件跨级访问信息时，我们可以像之前说的方法那样向更高级别的组件层层传递props，但此时的代码显得不那么优雅，甚至有些冗余。在React中，我们还可以使用context来实现跨级父子组件间的通信。
		```javascript
		// 父组件添加
		static childContextTypes = {
		  color: PropTypes.string,   // 传递给子组件
		}
		getChildContext() {
		  return {
		    color: 'red',
		  };
		}
		//子组件(可跨级)
		static contextTypes = {
		  color: PropTypes.string,
		}
		//获取：
		this.context.color
		```
4. 没有嵌套关系的组件通信
	+ 回调函数(找到共同的父组件)
	+ Pub/Sub模式
### 组件间抽象
1. mixin
	1) ES6 Classes与decorator
		+ decorator是在ES7中定义的新特性，是运用在运行时的方法
	2) mixin的问题
		+ 破坏了原有组件的封装
		+ 命名冲突
		+ 增加复杂性
2. 高阶组件
	+ 高阶组件(higher-order component)，类似于高阶函数，它接受React组件作为输入，输出一个新的React组件。
	+ 实现高阶组件的方法有如下两种:
		1) 属性代理(props proxy)。高阶组件通过被包裹的React组件来操作props
			```javascript
			import React, { Component } from 'React';
			const MyContainer = (WrappedComponent) =>
			  class extends Component {
			    constructor(props) {
			      super(props);
			      this.state = {
			        name: '',
			      };
			      this.onNameChange = this.onNameChange.bind(this);
			    }
			    proc(wrappedComponentInstance){
			      wrappedComponentInstance.method()
			    }
			    onNameChange(event) {
			      this.setState({
			        name: event.target.value,
			      })
			    }
			    render() {
			      const newProps = {
			        name: {
			          value: this.state.name,
			          onChange: this.onNameChange,
			        },
			      }
			      const props = Ojbect.assign({}, this.props, {ref: this.proc.bind(this)})
			      return (
			        <div>
			          <WrappedComponent { ...props } { ...newProps }/>
			        </div>
			      )
			    }
			  }
			  /*
			  a) 控制props  b) 通过refs使用引用
			  c) 抽象state  d) 使用其他元素包裹 WrappedComponent
			  */
			// 调用
			import React, { Component } from 'React';
			@MyContainer
			class MyComponent extends Component {
			  render() {
			    return <input name="name" {...this.props.name} />;
			  }
			}
			```
			+ 当使用属性代理构建高阶组件时，上述执行生命周期的过程类似于堆栈调用
				<br>`didmount→HOC didmount→(HOCs didmount)→(HOCs will unmount)→HOC will unmount→unmount`
		2) 反向继承(inheritance inversion)。高阶组件继承于被包裹的React组件。
			```javascript
			const MyContainer = (WrappedComponent) =>
			  class extends WrappedComponent {
			    render() {
			      return super.render();
			    }
			  }
			```
			+ 这种方法与属性代理不太一样。它通过继承WrappedComponent来实现，方法可以通过super来顺序调用。因为依赖于继承的机制，HOC的调用顺序和队列是一样的
				<br>`didmount→HOC didmount→(HOCs didmount)→will unmount→HOC will unmount→(HOCs will unmount)`
### 组件性能优化
+ 影响网页性能最大的因素是浏览器的`重绘(reflow)`和`重排(repaint)`。React背后的Virtual DOM就是尽可能地减少浏览器的重绘与重排。
+ 从React的渲染过程来看，如何防止不避要的渲染可能是最需要去解决的问题。然而，针对这个问题，React官方提供了一个便捷的方法来解决，那就是PureRender。
1. 纯函数
	+ 纯函数由三大原则构成：
		1) **给定相同的输入，它总是返回相同的输出；**
			+ 当然，还有很多情况是在不同的输入下会有相同的输出，但从概念上说，这个方法也还是纯函数。
		2) **过程没有副作用(side effect);**
			+ 就是说在纯函数中我们不能改变外部状态。而在JavaScript中改变外部状态的情况比比皆是，就比如方法的参数是对象或数组，那么它本身就有可能被方法执行的过程改变。
				```javascript
				const addToCart = (cart, item, quantity) => {
				  cart.items.push({
				    item,
				    quantity,
				  });
				  return cart;
				};
				/*
				在执行addToCart方法的时候，改变了originalCart对象。尽管我们返回了新对象，但因为在JavaScript中对象是引用，因此原来的对象也改变了。这就产生了副作用。
				*/
				```
			+ 我们提出了Immutable的概念，让参数中的引用重新复制。这里我们借用了lodash的cloneDeep方法来作深拷贝。
				```javascript
				import '_' from 'lodash';
				const addToCart = (cart, item, quantity) => {
				  const newCart = _.cloneDeep(cart);
				  newCart.items.push({
				    item,
				    quantity,
				  });
				  return newCart;
				};
				/*也可以用 const newCart = JSON.parse(JSON.stringify(cart))*/
				```
		3) **没有额外的状态依赖。**
			+ 就是指方法内的状态都只在方法的生命周期内存活，这意味着我们不能在方法内使用共享变量，因为这会给方法带来不可知因素。
	+ 纯函数也是函数式编程的基础，它完全独立于外部状态，这样就避免了因为共享外部状态而导致的bug。
	+ React在设计时带有函数式编程的基因，因为React组件本身就是纯函数。React的`createElement`方法保证了组件是纯净的，即传入指定props得到一定的Virtual DOM，整个过程都是可预测的。
	+ 我们可以通过拆分组件为子组件，进而对组件做更细粒度的控制。这也是函数式编程的魅力之一，保持纯净状态，可以让方法或组件更加专注(focused)，体积更小(small)，更独立(independent)，更具有复用性(reusability)和可测试性(testability)。
2. PureRender
	+ PureRender中的Pure指的就是组件满足纯函数的条件，即组件的渲染是被相同的props和state渲染进而得到相同的结果。这个概念与上述给定相同的输入，它总是返回相同的输出一致。
	+ 其原理为重新实现了shouldComponentUpdate 生命周期方法，让当前传入的props和state与之前的作浅比较，如果返回false，那么组件就不会执行render方法。
	+ 这里讲到了用shouldComponentUpdate来作性能优化的方法。在理想情况下，不考虑props和state的类型，那么要作到充分比较，只能通过深比较，但是它实在是太昂贵了。
		```javascript
		shouldComponentUpdate(nextProps, nextState) {
		  return isDeepEqual(this.props, nextProps) && isDeepEqual(this.state, nextState);  // 太昂贵了
		}
		```
	+ PureRender对object只作了引用比较，并没有作值比较。对于实现来说，这是一个取舍问题。PureRender源代码中只对新旧props作了`浅比较`。以下是shallowEqual的示例代码：
		```javascript
		function shallowEqual(obj, newObj) {
		  if (obj === newObj) {
		    return true;
		  }
		  const objKeys = Object.keys(obj);
		  const newObjKeys = Object.keys(newObj);
		  if (objKeys.length !== newObjKeys.length) {
		    return false;
		  }
		  // 关键代码，只需关注 props 中每一个是否相等，无需深入判断
		  return objKeys.every(key => {
		    return newObj[key] === obj[key];
		  });
		}
		```
	+ 优化PureRender
		在使用React写组件的过程中，PureRender可能是最重要也是最常见的性能优化方法。试想在数据可变的情况下，深比较的成本是相当昂贵的。但事实上，浅比较可以覆盖的场景并不是那么多。如果说props或state中有以下几种类型的情况，那么无论如何，它都会触发PureRender为true。
		1) 直接为props设置对象或数组
			<br>每次调用React组件其实都会重新创建组件。就算传入的数组或对象的值没有改变，它们引用的地址也会发生改变。
			```javascript
			 <Account style={{ color: 'black' }} />
			 //或：
			 <Account style={this.props.style || {}} />
			/*
			这样设置prop，则每次渲染时style都是新对象。对于这样的赋值操作，我们只需要提前赋值成常量，不直接使用字面量
			*/
			const defaultStyle = {};
			<Account style={this.props.style || defaultStyle} />
			```
			<br>同样，像在 props 中为对象或数据计算新值会使 PureRender 无效：
			```javascript
			<Item items={this.props.items.filter(item => item.val > 30)} />
			/*
			我们可以马上想到始终让对象或数组保持在内存中就可以增加命中率。但保持对象引用不符合函数式编程的原则，这为函数带来了副作用
			*/
			```
		2) 设置 props 方法并通过事件绑定在元素上
			<br>我们不用每次都绑定事件，因此把绑定移到构造器内。如果绑定方法需要传递参数，那么可以考虑通过抽象子组件或改变现有数据结构解决。
		3) 设置子组件
			<br>对于设置了子组件的React组件，在调用shouldComponentUpdate时，均返回 true。为什么呢？下面以NameItem组件为例来介绍：
			```javascript
			import React, { Component } from 'react';
			class NameItem extends Component {
			  render() {
			    return (
			      <Item>
			        <span> Arcthur </span>
			      <Item />
			    )
			  }
			}
			//上面的子组件JSX部分翻译过来，其实是：
			<Item children={React.createElement('span', {}, 'Arcthur')} />
			/*
			  显然，Item组件不论什么情况下都会重新渲染。那么，怎么避免Item组件的重复渲染呢？很简单，我们给NameItem设置PureRender，也就是说提到父级来判断：
			*/
			```
			```javascript
			import React, { Component } from 'react';
			import PureRenderMixin from 'react-addons-pure-render-mixin';
			class NameItem extends Component {
			  constructor(props) {
			  super(props);
			  this.shouldComponentUpdate = PureRenderMixin.shouldComponentUpdate.bind(this);
			 }
			 render() {
			  return (
			    <Item>
			      <span>Arcthur</span>
			    </Item>
			    );
			  }
			}
			```
3. React.PureComponent
	<br>PureComponent重新实现了shouldComponentUpdate()方法，新旧的props、state进行浅比较。
	<br>若state/props的数据类型为不可变数据，则能正确渲染结果。若是为可变数据如：数据、对象等，则可能会出错，因为引用类型的新旧state/props都指向都一个内存地址。
	1) 基本类型的PureComponent
	```javascript
	class CounterButton extends React.PureComponent {
	  constructor(props) {
	    super(props);
	    this.state = {count: 1};
	  }

	  render() {
	    return (
	      <button
	        color={this.props.color}
	        onClick={() => this.setState(state => ({count: state.count + 1}))}>
	        Count: {this.state.count}
	      </button>
	    );
	  }
	}
	```
	2) 对象或数组类型的PureComponent, 解决的方法是变化的state和props返回新的数据类型，和旧的数据不指向同一内存地址。（缺点是浅拷贝，或是数组或对象中的成员，也是引用类型的数据，则PureComponent也会无效。终极方法：深拷贝、Immutable）
	```javascript
	handleClick() {
	  this.setState(state => ({
	    words: [...state.words, 'marklar'],
	    //words: state.words.concat(['marklar']),
	    list: {...state.list, { name: 'haha', age: '23' } },
	    //list: Object.assign({},state.list,{name:'haha',age:'23'}),
	  }));
	  //使用concat()的原因是，concat()方法不修改原数组，push()会修改原数组
	};
	```
4. Immutable
	+ 在传递数据时，可以直接使Immutable Data来进一步提升组件的渲染性能。JavaScript中的对象一般是可变的(mutable)，因为使用了引用赋值，新的对象简单地引用了原始对象，改变新的对象将影响到原始对象。
	+ Immutable Data就是一旦创建，就不能再更改的数据。对Immutable对象进行修改、添加或删除操作，都会返回一个新的Immutable对象。Immutable实现的原理是持久化的数据结构(persistent datastructure)，也就是使用旧数据创建新数据时，要保证旧数据同时可用且不变。同时为了避免深拷贝把所有节点都复制一遍带来的性能损耗，Immutable使用了结构共享(structural sharing)，即如果对象树中一个节点发生变化，只修改这个节点和受它影响的父节点，其他节点则进行共享。
	+ 创建：` immutable.fromJS({uid:'123'})`
	+  Immutable的优点
		1) 降低了“可变”带来的复杂度
		2) 节省内存
		3) 撤销/重做，复制/粘贴，甚至时间旅行这些功能做起来都是小菜一碟
		4) 并发安全
		5) 拥抱函数式编程。
			<br>Immutable本身就是函数式编程中的概念。只要输入一致，输出必然一致，这样开发的组件更易于调试和组装。
	+ 使用Immutable的缺点：容易与原生对象混淆是使用Immutable的过程中遇到的最大的问题。
	+ 两个Immutable对象可以使用 === 来比较，这样是直接比较内存地址，其性能最好。但是即使两个对象的值是一样的，也会返回false;
		```javascript
		let map1 = Immutable.Map({a:1, b:1, c:1});
		let map2 = Immutable.Map({a:1, b:1, c:1});
		map1 === map2; // => false
		```
	+ 为了直接比较对象的值，Immutable提供了Immutable.is 来作“值比较”：
		```javascript
		Immutable.is(map1, map2); // => true
		/*
		Immutable.is比较的是两个对象的hashCode或valueOf(对于JavaScript对象)。由于Immutable内部使用了trie数据结构来存储，只要两个对象的hashCode相等，值就是一样的。这样的算法避免了深度遍历比较，因此性能非常好。
		*/
		```
	+ Immutable与cursor
		<br>由于Immutable数据一般嵌套非常深，所以为了便于访问深层数据，cursor提供了可以直接访问这个深层数据的引用。
		```javascript
		import Immutable from 'immutable';
		import Cursor from 'immutable/contrib/cursor';
		let data = Immutable.fromJS({ a: { b: { c: 1 } } });
		  // 让 cursor 指向 { c: 1 }
		let cursor = Cursor.from(data, ['a', 'b'], newData => {
		  // 当 cursor 或其子 cursor 执行更新时调用
		  console.log(newData);
		});
		cursor.get('c'); // 1
		cursor = cursor.update('c', x => x + 1);
		cursor.get('c'); // 2
		```
	+ Immutable与PureRender
		+ React做性能优化时最常用的就是shouldComponentUpdate方法，但它默认返回true，即始终会执行render方法，然后做Virtual DOM比较，并得出是否需要做真实DOM的更新，这里往往会带来很多没必要的渲染。
		+ 在shouldComponentUpdate中的应用Immutable.js则提供了简洁、高效的判断数据是否变化的方法，只需===和is比较就能知道是否需要执行render，而这个操作几乎零成本，所以可以极大提高性能。
			```javascript
			import React, { Component } from 'react';
			import { is } from 'immutable';
			class App extends Component {
			  shouldComponentUpdate(nextProps, nextState) {
			    const thisProps = this.props || {};
			    const thisState = this.state || {};
			    if (Object.keys(thisProps).length !== Object.keys(nextProps).length || Object.keys(thisState).length !== Object.keys(nextState).length) {
			      return true;
			    }
			    for (const key in nextProps) {
			      if (nextProps.hasOwnProperty(key) && !is(thisProps[key], nextProps[key])) {
			        return true;
			      }
			    }
			    for (const key in nextState) {
			      if (nextState.hasOwnProperty(key) && !is(thisState[key], nextState[key])) {
			        return true;
			      }
			    }
			    return false;
			  }
			}
			```
	+ Immutable与setState
		<br>React建议把this.state当作不可变的，因此修改前需要做一个深拷贝
		```javascript
		import React, { Component } from 'react';
		class App extends Component {
		  constructor(props) {
		    super(props);
		    this.state = {
		      data: Map({ times: 0 }),
		    }
		  }
		  handleAdd() {
		    this.setState(({ data }) => ({
		      data: data.update('times', v => v + 1),
		    }));
		    // 这时的 times 并不会改变
		    console.log(this.state.data.get('times'));
		  }
		}
		```
5. key
	+ 它是用来标识当前项的唯一性的props，对key有一个原则，那就是独一无二，且能不用遍历或随机值就不用，除非列表内容也并不是唯一的表示，且没有可以相匹配的属性。
	+ 关于key，我们还需要知道的一种情况是，有两个子组件需要渲染的时候，我们没法给它们设key。这时需要用到React插件createFragment来解决
		```javascript
		import React from 'react';
		import createFragment from 'react-addons-create-fragment';
		const Rank = ({ first, second }) => {
		  const children = createFragment({
		    first: first,
		    second: second,
		  });
		  return (
		    <ul> { children } </ul>
		  );
		}
		/*first和second两个prop的key就是我们设置对象的 key。*/
		```
6. react-addons-pref
	+ react-addons-perf是官方提供的插件。通过Perf.start()和Perf.stop()两个API设置开始和结束的状态来作分析
### 动画
1. CSS动画与JavaScript动画
	+ 总的来说，使用CSS动画，能够得到更好的性能和更快的开发效率。尽管运用CSS 更加方便，但必然有其作为DSL(domain-specific language, 领域编程语言)的局限性。当碰到CSS的局限性，导致用CSS无法实现或者实现起来十分烦琐时，就是使用JavaScript动画的时候了。
	+ CSS 动画的局限性
		1) CSS只支持cubic-bezier的缓动，如果你的动画对缓动函数有要求，就必须使用JavaScript动画。
		2) CSS动画只能针对一些特有的CSS属性。仍然有一些属性是CSS动画不支持的，例如SVG中path的d属性。
		3) CSS把translate、rotate、skew等都归结为一个属性——transform。因此，这些属性能共用同一个缓动函数。
	+ CSS animation
		1) 使用多步动画(多关键帧动画)
		2) 弥补CSS transition在控制上的不足，设置动画的反转、暂停、次数(可以设置为永久)等。
2. 玩转React Transition
### 自动化测试
+ 测试可以让项目保持健壮，在后期维护和扩展的过程中，减少犯错的几率。当项目发布时，代码能通过所有测试也代表所覆盖到的场景全部通过。自动化测试就是把整个流程自动化，代替复杂的人工点击。同时通过配置回调钩子，可以让测试定期执行或在每次发布前执行。
1. Jest
	+ Jest是由Facebook开源的React单元测试框架，内部DOM操作基于JSDOM，语法和断言基于Jasmine框架。它有以下4个特点
		1) 自动找到测试；
		2) 自动mock模拟依赖包，达到单元测试的目的
		3) 并不需要真实DOM环境执行，而是JSDOM模拟的DOM；
		4) 多进程并行执行测试。
	+ 当使用Jest来测试React组件时，还要引入react-dom/test-utils插件，用于模拟浏览器事件和对DOM进行校验。它提供的常用方法如下
		1) Simulate.{eventName} (DOMElement element, [object eventData])：模拟触发事件。
		2) renderIntoDocument(ReactElement instance)：渲染React组件到文档中，这里的文档节点由JSDOM提供。
		3) findRenderedDOMComponentWithClass(ReactComponent tree, string className)：从渲染的DOM树中查找含有class的节点。
		4) findRenderedDOMComponentWithTag(ReactComponent tree, function componentClass)：从渲染的 DOM 树中找到指定组件的节点。
	+ 使用Jest测试组件非常容易。它既可以模拟渲染DOM节点，也可以模拟触发DOM事件。在大部分情况下，它已经很好用。
		```javascript
		jest.unmock('../tab.js');
		import React from 'react';
		import ReactDOM from 'react-dom';
		import TestUtils from 'react-dom/test-utils';
		import Tab from '../Tab';
		describe('Tab', () => {
		  it('render the tab content', () => {
		  // 根据 data 渲染出 Tab 内容
		  const tab = TestUtils.renderIntoDocument(
		    <Tabs classPrefix={'tabs'} defaultActiveIndex={0} className="ui-tabs">
		      <TabPane order="0" tab={'Tab 1'}>第一个 Tab 里的内容</TabPane>
		      <TabPane order="1" tab={'Tab 2'}>第二个 Tab 里的内容</TabPane>
		      <TabPane order="2" tab={'Tab 3'}>第三个 Tab 里的内容</TabPane>
		    </Tabs>
		  );
		  const tabNode = ReactDOM.findDOMNode(tab);
		  // 验证渲染出 3 个 Tab
		  expect(tab.querySelectorAll('.tabs-tab').length).toEqual(3);
		  // 验证默认选中第一个 Tab，即索引为 0 的子元素含有 active 的 class
		  expect(tab.querySelectorAll('.tabs-tab')[0].classList.contains('tabs-active')).toBe(true);
		  });
		});
		//验证了渲染后，还需要验证点击后能切换到新的 Tab：
		describe('Tab', () => {
		  it('changes active tab after click', () => {
		    const tab = TestUtils.renderIntoDocument(
		      <Tabs classPrefix={'tabs'} defaultActiveIndex={0} className="ui-tabs">
		      <TabPane order="0" tab={'Tab 1'}>第一个 Tab 里的内容</TabPane>
		      <TabPane order="1" tab={'Tab 2'}>第二个 Tab 里的内容</TabPane>
		      <TabPane order="2" tab={'Tab 3'}>第三个 Tab 里的内容</TabPane>
		     </Tabs>
		    );
		    // 模拟点击第三个标签
		    TestUtils.Simulate.click(
		      tab.querySelectorAll('.tabs-tab')[2]
		    );
		    // 第一个标签取消选中，第三个标签被选中
		    expect(tab.querySelectorAll('.tabs-tab ')[0].classList.contains('tabs-active')).toBe(false);
		    expect(tab.querySelectorAll('.tabs-tab')[2].classList.contains('tabs-active')).toBe(true);
		  });
		});
		```
2. 浅渲染机制
	+ 浅渲染（shallow rendering）很有趣，意思就是只渲染组件中的第一层，这样测试执行器就不需要关心DOM和执行环境了。
	+ 在实际开发中，组件的层级非常深，所以测试顶层组件时，如果需要把所有子组件全部渲染出来，成本变得非常高。因为 React 组件良好的封装性，测试组件时，大部分测试只需要关注组件本身，它的子组件测试应该在子组件对应的测试代码里做。这样测试执行得很快。
	+ 假如一个组件内部有个非常复杂的子组件 ComplexComponent：
		```javascript
		function MyComponent() {
		  return(
		    <div>
		      <span className="heading">Title</span>
		      <ComplexComponent foo="bar" />
		    </div>
		  )
		}
		```
	+ 做浅渲染测试是这样的：
		```javascript
		import ShallowRenderer from 'react-test-renderer/shallow';
		const renderer = new ShallowRenderer();
		renderer.render(<MyComponent />);
		const result = renderer.getRenderOutput();
		expect(result.type).toBe('div');
		expect(result.props.children).toEqual([
		  <span className="heading">Title</span>,
		  <ComplexComponent foo="bar" />,
		]);
		```
3. 全渲染机制
	+	全渲染（full rendering）就是完整渲染出当前组件及其所有的子组件，就像在真实浏览器中渲染那样。当组件内部直接改变了DOM时，就需要使用全渲染来测试。全渲染需要真实地模拟DOM环境，流行的做法有以下几种。
		1) 使用JSDOM：使用JavaScript模拟DOM环境，能满足90%的使用场景。这是Jest内部所使用的全渲染框架。
		2) 使用Cheerio：类似JSDOM，更轻的实现，类似jQuery的语法。这是Enzyme内部使用的全渲染框架。
		3) 使用Karma：在真实的浏览器中执行测试，也支持在多个浏览器中依次执行测试，使用的是真实DOM环境，但速度稍慢。
4. 自动化测试
	+ 现在是时候把整个流程自动化起来了，你需要一个持续集成服务器(CI)来把整个流程自动化。如果使用GitHub或Gitlab来管理代码，你可以使用Travis CI或Circle CI。
_________
# 解读React源码
### 初探React源码
1. 在React源码中，每个文件的命名风格属于字面与含义可相互解释，整体的代码结构按照addons、isomorphic、renderers、shared、core、test进行组织。
2. renderers：是React代码的核心部分，它包含了大部分功能实现，此处对其进行单独分析。renderers分为dom和shared目录。shared：包含event和reconciler。
3. reconciler
	+ reconciler：称为协调器，它是最为核心的部分，包含React中自定义组件的实现(ReactCompositeComponent)、组件生命周期机制、setState机制(ReactUpdates、ReactUpdateQueue)、DOM diff算法(ReactMultiChild)等重要的特性方法。
	+ Virtual DOM是React的核心与精髓所在，而reconciler就是实现Virtual DOM的主要源码。Virtual DOM实际上是在浏览器端用JavaScript实现的一套DOM API，它之于React就好似一个虚拟空间，包括一整套Virtual DOM模型、生命周期的维护和管理、性能高效的diff算法和将Virtual DOM展示为原生DOM的Patch方法等。
	+ 基于React进行开发时，所有的DOM树都是通过Virtual DOM构造的。React在Virtual DOM上实现了DOM diff算法，当数据更新时，会通过diff寻找到需要变更的DOM节点，并只对变化的部分进行实际的浏览器的DOM更新，而不是重新渲染整个DOM树。
	+ React也能够实现Virtual DOM的批处理更新，当操作Virtual DOM时, 不会马上生成真实的DOM，而是会将一个事件循环(event loop)内的两次数据更新进行合并，这样就使得React能够在事件循环的结束之前完全不用操作真实的DOM。例如，多次进行节点内容 A→B，B→A的变化，React会将多次数据更新合并为A→B→A，即A→A，认为数据并没有更新，因此UI也不会发生任何变化。
### 生命周期的管理艺术
<br>&ensp;&ensp;&ensp;&ensp;React的主要思想是通过构建可复用组件来构建用户界面。所谓组件，其实就是有限状态机(FSM)，通过状态渲染对应的界面，且每个组件都有自己的生命周期，它规定了组件的状态和方法需要在哪个阶段改变和执行。
1. 初探React生命周期
	1) 当`首次挂载`组件时，按顺序执行，getDefaultProps、getInitialState、componentWillMount、render和componentDidMount。
	2) 当`卸载`组件时，执行 componentWillUnmount。
	3) 当`重新挂载`组件时，此时按顺序执行getInitialState、componentWillMount、render和componentDidMount，但并不执行getDefaultProps。
	4) 当`再次渲染`组件时，组件接受到更新状态，此时按顺序执行componentWillReceiveProps、shouldComponentUpdate、componentWillUpdate、render和componentDidUpdate。
2. 详解React生命周期
	+ 自定义组件(ReactCompositeComponent)的生命周期主要通过3个阶段进行管理——MOUNTING、RECEIVE_PROPS和UNMOUNTING。
	+ 这3个阶段对应3种方法，分别为：mountComponent、updateComponent和unmountComponent，每个方法都提供了几种处理方法，其中带will前缀的方法在进入状态之前调用，带did前缀的方法在进入状态之后调用。3个阶段共包括5种处理方法，还有两种特殊状态的处理方法。
		```javascript
		// 5种处理方法
		componentWillMount()
		componentDidMount()
		componentWillUpdate(nextProps, nextState)
		componentDidUpdate(prevProps, prevState)
		componentWillUnmount()
		// 2种特殊状态的处理方法
		componentWillReceiveProps(nextProps)
		shouldComponentUpdate(nextProps, nextState)
		```
	1) 阶段一：MOUNTING
		+ mountComponent负责管理生命周期中的getInitialState、componentWillMount、render和componentDidMount。
		+ 由于getDefaultProps是通过构造函数进行管理的，所以也是整个生命周期中最先开始执行的。而mountComponent只能望洋兴叹，无法调用到getDefaultProps。这就解释了为何getDefaultProps只执行一次。
		+ 若存在`componentWillMount`，则执行。如果此时在componentWillMount中调用setState方法，是不会触发re-render的，而是会进行state合并，且inst.state = this.\_processPendingState(inst.props, inst.context)是在componentWillMount之后执行的，因此componentWillMount中的this.state 并不是最新的，在render中才可以获取更新后的this.state。
		+ 因此，React是利用更新队列`this._pendingStateQueue`以及更新状态`this._pendingReplaceState`和`this._pendingForceUpdate`来实现setState的异步更新机制
		+ mountComponent本质上是通过递归渲染内容的，由于递归的特性，父组件的componentWillMount在其子组件的componentWillMount之前调用，而父组件的componentDidMount在其子组件的componentDidMount之后调用
			<br>`P componentWillMount -> P render -> C componentWillMount ->C render -> C componentDidMount -> P componentDidMount`
	2) 阶段二：RECEIVE_PROPS
		+ updateComponent负责管理生命周期中的componentWillReceiveProps、shouldComponentUpdate、componentWillUpdate、render和componentDidUpdate。
		+ 若存在`componentWillReceiveProps`，则执行。如果此时在componentWillReceiveProps中调用setState，是不会触发re-render的，而是会进行state合并。
		+ 禁止在`shouldComponentUpdate`和`componentWillUpdate`中调用setState，这会造成循环调用，直至耗光浏览器内存后崩溃。
		+ updateComponent本质上也是通过递归渲染内容的，由于递归的特性，父组件的componentWillUpdate是在其子组件的componentWillUpdate之前调用的，而父组件的 componentDidUpdate也是在其子组件的componentDidUpdate之后调用的。
			<br>`P shouldComponentUpdate -> P componentWillUpdate -> P render ->  C shouldComponentUpdate -> C componentWillUpdate ->C render -> C componentDidUpdate -> P componentDidUpdate`
	3) 阶段三：UNMOUNTING
		+ unmountComponent负责管理生命周期中的componentWillUnmount。
		+ 如果存在componentWillUnmount，则执行并重置所有相关参数、更新队列以及更新状态，如果此时在componentWillUnmount中调用setState，是不会触发re-render的，这是因为所有更新队列和更新状态都被重置为null，并清除了公共类，完成了组件卸载操作。
3. 无状态组件
	+ 无状态组件只是一个render方法，并没有组件类的实例化过程，也没有实例返回
	+ 无状态组件没有状态，没有生命周期，只是简单地接受props渲染生成DOM结构，是一个纯粹为渲染而生的组件。由于无状态组件有简单、便捷、高效等诸多优点，所以 如果可能的话，请尽量使用无状态组件。
### 解密setState 机制
1. setState异步更新
	+ setState通过一个`队列机制`实现state更新。当执行setState时，会将需要更新的state合并后放入状态队列，而不会立刻更新this.state，队列机制可以高效地批量更新state。
2. setState循环调用风险
	+ 当调用setState时，实际上会执行`enqueueSetState`方法，并对partialState以及_pendingStateQueue更新队列进行合并操作，最终通过`enqueueUpdate`执行state更新。
	+ 而performUpdateIfNecessary方法会获取`_pendingElement`、`_pendingStateQueue`、`_pendingForceUpdate`，并调用receiveComponent和updateComponent方法进行组件更新。
	+ 如果在shouldComponentUpdate或componentWillUpdate方法中调用setState ，此时this.\_pendingStateQueue != null，则performUpdateIfNecessary方法就会调用updateComponent方法进行组件更新，但updateComponent方法又会调用shouldComponentUpdate和componentWillUpdate方法，因此造成循环调用，使得浏览器内存占满后崩溃。
	+ setState的源码
		```javascript
		// 更新 state
		ReactComponent.prototype.setState = function(partialState, callback){
		  this.updater.enqueueSetState(this, partialState);
		  if (callback) {
		    this.updater.enqueueCallback(this, callback, 'setState');
		  }
		};
		enqueueSetState: function(publicInstance, partialState) {
		  var internalInstance = getInternalInstanceReadyForUpdate(publicInstance,'setState');
		  if (!internalInstance) {
		    return;
		  }
		  // 更新队列合并操作
		  var queue = internalInstance._pendingStateQueue || (internalInstance._pendingStateQueue = []);
		  queue.push(partialState);
		  enqueueUpdate(internalInstance);
		},
		// 如果存在_pendingElement、_pendingStateQueue和_pendingForceUpdate，则更新组件
		performUpdateIfNecessary: function(transaction) {
		  if (this._pendingElement != null) {
		    ReactReconciler.receiveComponent(this, this._pendingElement, transaction, this._context);
		  }
		  if (this._pendingStateQueue !== null || this._pendingForceUpdate) {
		    this.updateComponent(transaction, this._currentElement, this._currentElement, this._context, this._context);
		  }
		}
		/*
		setState()源码的两个方法 enqueueSetState() 和 performUpdateIfNecessary()
		*/
		```
3. setState调用栈
	+ setState最终是通过enqueueUpdate执行state更新，enqueueUpdate的代码如下
		```javascript
		function enqueueUpdate(component) {
		  ensureInjected();
		  // 如果不处于批量更新模式
		  if (!batchingStrategy.isBatchingUpdates) {
		    batchingStrategy.batchedUpdates(enqueueUpdate, component);
		    return;
		  }
		  // 如果处于批量更新模式，则将该组件保存在 dirtyComponents 中
		  dirtyComponents.push(component);
		}
		```
4. 解密setState
	```javascript
	import React, { Component } from 'react';
	class Example extends Component {
	  constructor() {
	    super();
	    this.state = {
	      val: 0
	    };
	  }
	  componentDidMount() {
	    this.setState({val: this.state.val + 1});
	    console.log(this.state.val);   // 0
	    this.setState({val: this.state.val + 1});
	    console.log(this.state.val);   // 0   // state 不是更新后的值
	    setTimeout(() => {
	      this.setState({val: this.state.val + 1});
	      console.log(this.state.val);   // 2 // state是更新后的值
	      this.setState({val: this.state.val + 1});
	      console.log(this.state.val);  // 3  // state是更新后的值
	    }, 0);
	    /*
	    this.setState((prevState, props) => ({
	      val: prevState.val + 1
	    }));
	    console.log(this.state.val);
	    */
	  }
	  render() {
	    return null;
	  }
	}
	/*
	a) componentDidMount中调用setState时，batchingStrategy的isBatchingUpdates已经被设为true，所以两次setState的结果并没有立即生效，而是被放进了dirtyComponents中。这也解释了两次打印this.state.val都是0的原因，因为新的 state还没有被应用到组件中。
	b) setTimeout中的两次setState，因为没有前置的batchedUpdate调用，所以batchingStrategy的isBatchingUpdates标志位是false，也就导致了新的state马上生效，没有走到dirtyComponents分支。也就是说，setTimeout中第一次执行setState时，this.state.val为1而setState完成后打印时this.state.val变成了2。第二次的setState同理。
	*/
	```
### diff 算法
&ensp;&ensp;&ensp;&ensp;diff会帮助我们计算出Virtual DOM中真正变化的部分，并只针对该部分进行原生DOM操作，而非重新渲染整个页面，从而保证了每次操作更新后页面的高效渲染。
1. 传统diff算法
	<br>&ensp;&ensp;&ensp;&ensp;计算一棵树形结构转换成另一棵树形结构的最少操作，是一个复杂且值得研究的问题。传统diff算法通过循环递归对节点进行依次对比，效率低下，算法复杂度达到O(n3)，其中n是树中节点的总数。
2. 详解diff
	+ React将Virtual DOM树转换成actual DOM树的最少操作的过程称为调和(reconciliation)。diff算法便是调和的具体实现。
	+ React通过制定大胆的diff策略，将O(n3)复杂度的问题转换成O(n)复杂度的问题。
	+ Virtual DOM概况来讲，就是在数据和真实DOM之间建立了一层缓冲。对于开发者而言，数据变化了就调用React的渲染方法，而React并不是直接得到新的DOM进行替换，而是先生成Virtual DOM，与上一次渲染得到的Virtual DOM进行比对，在渲染得到的Virtual DOM上发现变化，然后将变化的地方更新到真实DOM上。
3. diff 三个策略：
	+ 策略一：Web UI中DOM节点跨层级的移动操作特别少，可以忽略不计。
	+ 策略二：拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构。
	+ 策略三：对于同一层级的一组子节点，它们可以通过唯一id进行区分。

	<br>**基于以上策略，React分别对tree diff、component diff 以及element diff进行算法优化。**
	1) tree diff
		+ 基于策略一，React对树的算法进行了简洁明了的优化，即对树进行分层比较，两棵树只会对同一层次的节点进行比较。
		+ React只会简单的考虑同层节点的位置变换，对于不同层的节点，只有简单的创建和删除。
		+ 在开发组件时，保持稳定的DOM结构会有助于性能的提升。例如，可以通过 CSS 隐藏或显示节点，而不是真正地移除或添加DOM节点。
	2) component diff
		<br>React是基于组件构建应用的，对于组件间的比较所采取的策略也是非常简洁、高效的。
		- 如果是同一类型的组件，按照原策略继续比较Virtual DOM树即可。
		- 如果不是，则将该组件判断为dirty component，从而替换整个组件下的所有子节点。
		- 对于同一类型的组件，有可能其Virtual DOM没有任何变化，如果能够确切知道这点，那么就可以节省大量的diff 运算时间。因此，React允许用户通过shouldComponentUpdate()来判断该组件是否需要进行diff算法分析。
	3) element diff
		<br>当节点处于同一层级时，diff提供了3种节点操作，分别为INSERT_MARKUP(插入)、MOVE_EXISTING(移动)和 REMOVE_NODE(删除)。
### React Patch方法
1. Patch，简而言之就是将tree diff计算出来的DOM差异队列更新到真实的DOM节点上，最终让浏览器能够渲染出更新的数据。
2. 主要是通过遍历差异队列实现的。遍历差异队列时，通过更新类型进行相应的操作，包括：新节点的插入、已有节点的移动和移除等。
3. 这里为什么可以直接依次插入节点呢？原因就是在diff阶段添加差异节点到差异队列时，本身就是有序添加。也就是说，新增节点(包括move和insert)在队列里的顺序就是最终真实DOM的顺序，因此可以直接依次根据index去插入节点。而且，React并不是计算出一个差异就去执行一次Patch，而是计算出全部差异并放入差异队列后，再一次性地去执行Patch方法完成真实DOM的更新。
_________
# 深入Redux应用架构
### Redux简介
1. Redux三大原则
	1) 单一数据源
		+ 使用单一数据源的好处在于整个应用状态都保存在一个对象中，这样我们随时可以提取出整个应用的状态进行持久化(比如实现一个针对整个应用的即时保存功
		能)。此外，这样的设计也为服务端渲染提供了可能。
	2) 状态是只读的
		+ 在Redux中，我们并不会自己用代码来定义一个store。取而代之的是，我们定义一个reducer，它的功能是根据当前触发的action对当前应用的状态(state)进行迭代，这里我们并没有直接修改应用的状态，而是返回了一份全新的状态。
		+ Redux提供的createStore方法会根据reducer生成store。最后，我们可以利用 store.dispatch方法来达到修改状态的目的。
	3) 状态修改均由纯函数完成
		+ 这样设计的好处在于reducer里对状态的修改变得简单、纯粹、可测试。
2. Redux核心API
	+ Redux的核心是一个store，这个store由Redux提供createStore(reducers[, initialState])方法生成。从函数签名看出，要想生成store，必须要传入reducers，同时也可以传入第二个可选参数初始化状态(initialState)。
	+ 在Redux里，负责响应action并修改数据的角色就是reducer。reducer本质上是一个函数，其函数签名为reducer(previousState, action) => newState。可以看出，reducer在处理action的同时，还需要接受一个previousState参数。所以，reducer的职责就是根据previousState和action计算出新的newState。
	+ Redux中最核心的API——createStore
		```javascript
		import { createStore } from 'redux';
		const store = createStore(reducers);
		```
	+ 通过createStore方法创建的store是一个对象，它本身又包含4个方法
		1) getState()：获取 store 中当前的状态。
		2) dispatch(action)：分发一个action，并返回这个action，这是唯一能改变store中数据的方式。
		3) subscribe(listener)：注册一个监听者，它在store发生变化时被调用。
		4) replaceReducer(nextReducer)：更新当前store里的reducer，一般只会在开发模式中调用该方法。
3. 与React绑定
	+ Redux的核心只有一个createStore()方法，但是仅仅使用这个方法还不足以让Redux在React应用中发挥作用。我们还需要react-redux库——Redux官方提供的React绑定。
	+ react-redux提供了一个组件和一个API帮助Redux和React进行绑定，一个是React组件`<Provider/>` ，一个是connect()。`<Provider/>`接受一个store作为props，它是整个Redux应用的顶层组件，而connect()提供了在整个React应用的任意组件中获取store中数据的功能。
  `  connect([mapStateToProps], [mapDispatchToProps], [mergeProps],[options])`
### Redux middleware
1. 理解middleware机制
	+ Redux提供了applyMiddleware方法来加载middleware，该方法的源码如下：
		```javascript
		import compose from './compose';
		export default function applyMiddleware(...middlewares) {
		  return (next) => (reducer, initialState) => {
		    let store = next(reducer, initialState);
		    let dispatch = store.dispatch;
		    let chain = [];
		    var middlewareAPI = {
		      getState: store.getState,
		      dispatch: (action) => dispatch(action),
		    };
		    chain = middlewares.map(middleware => middleware(middlewareAPI));
		    dispatch = compose(...chain)(store.dispatch);
		    return {
		      ...store,
		      dispatch,
		    };
		  }
		}
		```
	+ logger middleware的实现：
		```javascript
		export default store => next => action => {
		  console.log('dispatch:', action);
		  next(action);
		  console.log('finish:', action);
		}
		```
2. 函数式编程思想设计
	+ middleware的设计有点特殊，是一个层层包裹的匿名函数，这其实是函数式编程中的currying，它是一种使用匿名单参数函数来实现多参数函数的方法。
	+ currying的middleware结构的好处主要有以下两点。
		1) 易串联：currying函数具有延迟执行的特性，通过不断currying形成的middleware可以累积参数，再配合组合(compose)的方式，很容易形成pipeline来处理数据流。
		2) 共享store：在applyMiddleware执行的过程中，store还是旧的，但是因为闭包的存在，applyMiddleware完成后，所有的middleware内部拿到的store是最新且相同的。
	+ 另外，我们会发现applyMiddleware的结构也是一个多currying的函数。借助compose，applyMiddleware可以用来和其他插件加强createStore函数：
		```javascript
		import { createStore, applyMiddleware, compose } from 'Redux';
		import rootReducer from '../reducers';
		import DevTools from '../containers/DevTools';
		const finalCreateStore = compose(
		  // 在开发环境中使用的 middleware
		  applyMiddleware(d1, d2, d3),
		  // 它会启动 Redux DevTools
		  DevTools.instrument()
		)(createStore);
		```
3. 组合串联middleware
	<br>这一层只有一行代码，却是applyMiddleware精华之所在：
	```javascript
	dispatch = compose(...chain)(store.dispatch);
	/*
	其中compose是函数式编程中的组合，它将chain中的所有匿名函数[f1, f2, ... , fx, ..., fn]组装成一个新的函数，即新的dispatch。当新dispatch 执行时，[f1, f2, ... , fx, ..., fn]，从右到左依次执行。
	Redux中compose的实现是下面这样的，当然实现方式并不唯一：
	*/
	function compose(...funcs) {
	  return arg => funcs.reduceRight((composed, f) => f(composed), arg);
	}
	/*
	compose(...funcs) 返回的是一个匿名函数，其中funcs就是chain数组。当调用reduceRight时，依次从funcs数组的右端取一个函数fx拿来执行，fx的参数composed就是前一次fx+1执行的结果，而第一次执行的fn(n 代表 chain 的长度)的参数 arg就是store.dispatch。所以，当compose执行完后，我们得到的dispatch 是这样的，假设n = 3：
	*/
	dispatch = f1(f2(f3(store.dispatch))));
	```
4. 在middleware中调用dispatch会发生什么
	+ 在middleware中调用store.dispatch()和在其他任何地方调用的效果一样，而是middleware中调用next()，效果是进入下一个middleware。如果在middleware一直调用store.dipatch(action)，就会形成无限循环了。
	+ Redux Thunk
		```javascript
		const thunk = store => next => action =>
		  typeof action === 'function' ? action(store.dispatch, store.getState) : next(action)
		```
### Redux异步流
1. 使用middleware简化异步请求
	+ Thunk函数实现上就是针对多参数的currying以实现对函数的惰性求值。任何函数，只要参数有回调函数，就能写成Thunk函数的形式
	+ redux-thunk的源代码
		```javascript
		function createThunkMiddleware(extraArgument) {
		  return ({ dispatch, getState }) => next => action => {
		    if (typeof action === 'function') {
		      return action(dispatch, getState, extraArgument);
		    }
		    return next(action);
		  };
		}
		/*
		当action为函数的时候，我们并没有调用next或dispatch方法，而是返回action的调用。这里的action即为一个Thunk函数，
		以达到将dispatch和getState参数传递到函数内的作用。
		*/
		```
	+ redux-composable-fetch
		```javascript
		const fetchMiddleware = store => next => action => {
		  if (!action.url || !Array.isArray(action.types)) {
		    return next(action);
		  }
		  const [LOADING, SUCCESS, ERROR] = action.types;
		  next({
		    type: LOADING,
		    loading: true,
		    ...action,
		  });
		  fetch(action.url, { params: action.params })
		    .then(result => {
		      next({
		        type: SUCCESS,
		        loading: false,
		        payload: result,
		      });
		    })
		    .catch(err => {
		      next({
		        type: ERROR,
		        loading: false,
		        error: err,
		      });
		    });
		}
		```
2. 使用middleware处理复杂异步流
	在实际场景中，我们不但有短连接请求，还有轮询请求、多异步串联请求，或是在异步中加入同步处理的逻辑。这时候，使用redux-composable-fetch就显得力不从心了。
	+ **轮询**
		<br>&ensp;&ensp;&ensp;&ensp; 轮询是长连接的一种实现方式，它能够在一定时间内重新启动自身，然后再次发起请求。基于这个特性，我们可以在redux-composable-fetch的基础上再写一个 middleware，这里命名为redux-polling
		```javascript
		import setRafTimeout, { clearRafTimeout } from 'setRafTimeout';
		export default ({ dispatch, getState }) => next => action => {
		  const { pollingUrl, params, types } = action;
		  const isPollingAction = pollingUrl && params && types;
		  if (!isPollingAction) {
		    return next(action);
		  }
		  let timeoutId = null;
		  const startPolling = (timeout = 0) => {
		    timeoutId = setRafTimeout(() => {
		      const { pollingUrl, ...others } = action;
		      const pollingAction = {
		        ...others,
		        url: pollingUrl,
		        timeoutId,
		      };
		      dispatch(pollingAction).then(data => {
		        if (data && data.interval && typeof data.interval === 'number') {
		          startPolling(data.interval * 1000);
		        } else {
		          console.error('pollingAction should fetch data contain interval');
		        }
		      });
		    }, timeout);
		  };
		  startPolling();
		}
		export const clearPollingTimeout = (timeoutId) => {
		  if (timeoutId) {
		    clearRafTimeout(timeoutId);
		  }
		};
		// 最后，调用 action 来发起轮询：
		{
		  pollingUrl: '/api/weather.json',
		  params: {
		    city: encodeURI(city),
		  },
		  types: [null, 'GET_WEATHER_SUCESS', null],
		}
		```
### Redux与路由
1. 路由的基本原理
	<br>&ensp;&ensp;&ensp;&ensp; 简单地说，路由的基本原理即是保证View和URL同步，而View可以看成是资源的一种表现。当用户在Web界面中进行操作时，应用会在若干个交互状态中切换，路由则会记录下某些重要的状态，这些变化同样会被记录在浏览器的历史中，用户可以通过浏览器的“前进”、“后退”按钮切换状态。简单地说，用户可以通过手动输入或者与页面进行交互来改变URL，然后通过同步或者异步的方式向服务端发送请求获取资源，重新绘制UI。
2. React Router Redux
	1) 将React Router与Redux store绑定
		<br>&ensp;&ensp;&ensp;&ensp; React Router Redux提供了简单直白的API——syncHistoryWithStore来完成与 Redux store的绑定工作。我们只需要传入React Router中的history(前面提到的 browserHistory 或hashHistory，甚至是自己创建的history)，以及Redux中的store，就可以获得一个增强后的history 对象。
		```javascript
		import { browserHistory } from 'react-router'
		import { syncHistoryWithStore } from 'react-router-redux'
		import reducers from '<project-path>/reducers'
		const store = createStore(reducers);
		const history = syncHistoryWithStore(browserHistory, store)
		```
	2) 用Redux的方式改变路由
		```javascript
		import { browserHistory } from 'react-router';
		import { routerMiddleware } from 'react-router-redux';
		const middleware = routerMiddleware(browserHistory);
		const store = createStore(
		  reducers,
		  applyMiddleware(middleware)
		);
		//最后，就可以用 store.dispatch 来分发一个路由变动的 action 了：
		import { push } from 'react-router-redux';
		// 切换路由到 /home
		store.dispatch(push('/home'));
		```
5 Redux与组件
1) 容器型组件
	+ 容器型组件，意为组件是怎么工作的，更具体一些就是数据是怎么更新的。它不会包含任何Virtual DOM的修改或组合，也不会包含组件的样式。如果映射到Redux上，那么容器型组件就是使用connect的组件。
2) 展示型组件
	+ 展示型组件，意为组件是怎么渲染的。它包含了Virtual DOM的修改或组合，也可能包含组件的样式。同时，它不依赖任何形式的store。一般可以写成无状态函数，但实际上展示型组件并不一定都是无状态的组件，因为很多展示型组件里依然存在生命周期方法。
	+ 这样做区分的目的是为了可以使用相同的展示型组件来配合不同的数据源作渲染，可以做到更好的可复用性。

# React 16 新增
### render的返回类型
1. fragments
<br>React中一个常见模式是为一个组件返回多个元素。 片段(fragments) 可以让你将子元素列表添加到一个分组中，并且不会在DOM中增加额外节点。常用在table、ul、dl等中
	+  用法一： `[]`
		```javascript
		// v16.0 数组的方式， 需要手动添加key。
		function ListItem({ item }) {
		  return [
		    <dt key="list_1">{item.term}</dt>,
		    <dd key="list_2">{item.description}</dd>>
		    ]
		}
		```
	+ 用法二：`<Fragment></Fragment>`
		```javascript
		// v16.2 引入<Fragment>标签
		import React, { Fragment } from 'react';
		function Glossary(props) {
		  return (
		    <dl>
		      {props.items.map(item => (
		        // key 是唯一可以传递给 Fragment 的属性
		        <Fragment key={item.id}>
		          <dt>{item.term}</dt>
		          <dd>{item.description}</dd>
		        </Fragment>
		      ))}
		    </dl>
		  );
		}
		```
	+ 用法三： `<></>`  `babel 7 `
		```javascript
		function ListItem({ item }) {
		  return (
		    <>
		      <dt>{item.term}</dt>
		      <dd>{item.description}</dd>>
		    </>
		  );
		}
		```
2. 字符串类型
	<br>直接生成dom节点的text内容
	```javascript
	render() {
	  return 'Look ma, no spans!';
	}
	```

### 错误处理
1. 简介
+ 错误边界(Error Boundaries)是React组件，它可以在子组件树的任何位置捕获JavaScript错误，记录这些错误，并显示一个备用UI，而不是使整个组件树崩溃。 错误边界在渲染，生命周期方法以及整个组件树下的构造函数中捕获错误。
+ componentDidCatch(error, info): 新生命周期方法
+ 错误边界(Error Boundaries)仅可以捕获其子组件的错误。错误边界无法捕获其自身的错误。如果一个错误边界无法渲染错误信息，则错误会向上冒泡至最接近的错误边界。这也类似于JavaScript中catch{} 的工作机制。
+ 错误边界无法捕获如下错误:
	1) 事件处理
	2) 异步代码 （例如 setTimeout 或 requestAnimationFrame 回调函数）
	3) 服务端渲染
	4) 错误边界自身抛出来的错误 （而不是其子组件）
2. 组件栈追踪
	+ React 16会将渲染期间所有在开发环境下的发生的错误打印到控制台，即使应用程序意外的将其掩盖。除了错误信息和JavaScript栈外，其还提供了组件栈追踪。现在你可以准确地查看发生在组件树内的错误信息，也可以在组件堆栈中查看文件名和行数
	 <br> bable 配置插件： babel-plugin-transform-react-jsx-source
	+ 显示在堆栈跟踪中的组件名称取决于Function.name属性。
3. 为何不使用 try/catch
	+ try/catch仅能在命令式代码（imperative code）下可用。然而，React组件是声明式的并且具体指出声明什么需要被渲染：
### 自定义DOM属性
+ 支持非标准的自定义DOM属性，在之前的版本中，React会忽略无法识别的HTML和SVG属性，自定义属性只能通过data-\*形式添加，现在它会把这些属性直接传递给DOM，这个改动让React可以去掉属性白名单，从而减少了文件大小。但当DOM传递的自定义属性是函数类型或event handler类型时，也会被React忽略掉。
### 更好的服务器端渲染
+ 提升服务端渲染性能，React 16的SSR被完全重写，新的实现非常快，接近3倍性能于React 15，现在提供一种流模式streaming，可以更快地把渲染的字节发送到客户端。
### 更小的体积
+ 减少了约 32% 的大小
### refs  // v16.3
<br> 在JSX中ref是不会传递给其子组件的，如果把传递ref属性，使用React.forwardRef()
<br> React新的创建ref的方式：`React.createRef()`
1. React 16以前的ref(详情见前面refs部分)
	1) ref的使用
		+ 回调的方式
		+ String
	2) 缺点：
2. React.createRef
	<br>React新的API创建ref的方式，如`this.myRef = React.createRef()`, myRef将作为实例属性使用。ref将指向DOM节点，获取DOM节点的方式：`this.myRef.current`，就可以操作原生DOM方法。
	<br>不能给函数式组件添加ref属性，因为函数式组件没有实例，但函数式组件里面的HTML元素和类组件使用ref属性是可以的
	<br>子组件暴露ref给父组件：当父组件需要触发子组件的focus等事件、或者需要获取子组件的节点数或位置时
3. React.forwardRef
	1) 转发refs给DOM组件
		+ Ref转发是一种选择性加入的功能，可让某些组件接收他们收到的ref，并将其向下传递（换句话说，“转发”）给子组件。
		```javascript
		const FancyButton = React.forwardRef((props, ref) => (
		  <button ref={ref} className="FancyButton">
		    {props.children}
		  </button>
		));
		const ref = React.createRef();
		<FancyButton ref={ref}>Click me!</FancyButton>;
		//调用
		ref.current.focus();
		```
	2) 在高阶组件中转发refs
		+ 在高阶组件中传递ref, ref指向的是最外层的组件，而不是被包裹的组件
		```javascript
		const logProps = (WrappedComponent) =>
		  class LogProps extends React.Component {
		    componentDidUpdate(prevProps) {
		      console.log('old props:', prevProps);
		      console.log('new props:', this.props);
		    }
		    render() {
		      return <WrappedComponent {...this.props} />;
		    }
		  }
		@logProps
		export default class FancyButton extends React.Component {
		  focus() {}
		}
		import FancyButton from './FancyButton';
		const ref = React.createRef();
		<FancyButton label="Click Me" handleClick={handleClick} ref={ref} />;
		/*
		ref指向的是LogPorps组件，而不是FancyButton组件
		*/
		```
		<br>方法二
		```javascript
		function logProps(Component) {
		  class LogProps extends React.Component {
		    componentDidUpdate(prevProps) {
		      console.log('old props:', prevProps);
		      console.log('new props:', this.props);
		    }
		    render() {
		      const {forwardedRef, ...rest} = this.props;
		      return <Component ref={forwardedRef} {...rest} />;
		    }
		  }
		  return React.forwardRef((props, ref) => {
		    return <LogProps {...props} forwardedRef={ref} />;
		  });
		}// ref指向FancyButton组件
		```
### 上下文(Context)   // v16.3
1. React.createContext
	<br>`const {Provider, Consumer} = React.createContext(defaultValue);`
	+ 创建一个{ Provider, Consumer }对，当React渲染context Consumer时，它将从组件树中匹配最接近的Provider中读取当前的context值。
	+ defaultValue参数 仅当 Consumer(使用者)在树中没有匹配的Provider(提供则) 时使用它。这有助于在不封装它们的情况下对组件进行测试。注意：将undefined作为Provider(提供者)值传递不会导致Consumer(使用者)使用defaultValue 。
	+ 通过使用与Object.is比较新值和旧值来确定value属性变化。
	+ context常用场景：设置语言环境、地区偏好、UI主题、数据缓存等
2. 使用多个context
<br>为了保持context的快速重新渲染，React需要使每个context Consumer成为树中的一个独立节点。
	```javascript
	//祖 (一)
	const ThemeContext = React.createContext('light');
	const UserContext = React.createContext({
	  name: 'Guest',
	});
	class App extends React.Component {
	  render() {
	    const {signedInUser, theme} = this.props;
	    return (
	      <ThemeContext.Provider value={theme}>
	        <UserContext.Provider value={signedInUser}>
	          <Layout />
	        </UserContext.Provider>
	      </ThemeContext.Provider>
	    );
	  }
	}
	// 父 ( 二 )
	function Layout() {
	  return (
	    <div>
	      <Sidebar />
	      <Content />
	    </div>
	  );
	}
	// 子使用 ( 三 )
	function Content() {
	  return (
	    <ThemeContext.Consumer>
	      {theme => (
	        <UserContext.Consumer>
	          {user => (
	            <ProfilePage user={user} theme={theme} />
	          )}
	        </UserContext.Consumer>
	      )}
	    </ThemeContext.Consumer>
	  );
	}
	```
3. 在生命周期方法中访问Context
	<br>在生命周期方法中访问context值是一种相对常见的用例。不是将context添加到每个生命周期方法中，你只需将它作为props，然后像使用props一样使用它即可。
4. 高阶组件中的Context
	<br>某些类型的context被许多组件（例如theme或localization）使用。使用 `<Context.Consumer>`元素显示地封装每个依赖项是冗余的
	```javascript
	const {ThemeContext} = React.createContext('light');
	const withTheme = (Component)=> (props)=> (
	  <ThemeContext.Consumer>
	    {theme => <Component {...props} theme={theme} />}
	  </ThemeContext.Consumer>
	);
	// 调用
	const Button = ({theme, ...rest}) =>
	  <button className={theme} {...rest}/>;
	const ThemedButton = withTheme(Button);
	```
5. 转发Refs给context Consumer(使用者)
	<br>渲染prop(属性)API的一个问题是refs不会自动传递给封装元素。为了解决这个问题，使用React.forwardRef：
	```javascript
	//fancy-button.js
	class FancyButton extends React.Component {
	  focus() {
	  }
	}
	export default React.forwardRef((props, ref) => (
	  <ThemeContext.Consumer>
	    {theme => (
	      <FancyButton {...props} theme={theme} ref={ref} />
	    )}
	  </ThemeContext.Consumer>
	));
	//app.js
	import FancyButton from './fancy-button';
	const ref = React.createRef();
	<FancyButton ref={ref} onClick={handleClick}>
	  Click me!
	</FancyButton>;
	```
### 异步渲染
+ 要理解react异步渲染的机制，我们首先要说一说react之前是如何进行渲染。在react16之前，组件的渲染都是同步进行的，也就是说从constructor开始到componentDidUpdate结束，react的运行都是没有中断的，生命周期开始之后就会运行到其结束为止，这样带来的一个缺点就是，如果组件嵌套很深， 渲染时间增长了之后，一些重要的，高优先级的操作就会被阻塞，例如用户的输入等，这样就会造成体验上的不友好。
+ 在之后即将到来的异步渲染机制中， 会允许首先解决高优先级的运行，同时会暂停当前的渲染进程，当高优先级的进程结束之后，再返回继续运行当前进程，这样会大大的提高react的流畅度，给用户带来更好的体验
+ getDerivedStateFromProps：保证与即将到来的异步渲染模式的兼容
+ Facebook在react16增加fiber结构，其实并不是为了减少组件的渲染时间，事实上也并不会减少，最重要的是现在可以使得一些更高优先级的任务，如用户的操作能够优先执行，提高用户的体验，至少用户不会感觉到卡顿~
### Pointer Events   // v16.4

### React.Component
1. 组件生命周期
	+ 新增加三个生命周期方法：componentDidCatch(error,info) getDerivedStateFromProps(props,state)、getSnapshotBeforeUpdate(prevProps, prevState)
	+ 三个标记为unsafe的方法，在React 17版本中需加前缀"UNSAFE_": componentWillMount()、componentWillUpdate(nextProps, nextState)、componentWillReceiveProps(nextProps)。共同点：这三个方法在运行中可能会调用多次。
	1) Mounting
		+ constructor()
	  + static `getDerivedStateFromProps()`
		+ render()
		+ componentDidMount()：组件挂载后调用
			<br>获取真实的DOM节点、获取远程数据、添加事件监听器、定时器等
			<br>若此时调用setState()方法，将会调用两次render()，所以state的数据可放在constructor中。但是若需要渲染tooltips /modal之类依赖DOM节点来决定它的大小和位置的，可以使用setState()
	2) Updating
		+ static getDerivedStateFromProps(props, state)
		+ shouldComponentUpdate(nextProps, nextState)
		+ render()
		+ getSnapshotBeforeUpdate(prevProps, prevState)
			<br>读取DOM变化，如屏幕滚动导致scroll_height的变化，浏览器窗口的调整等。
		+ componentDidUpdate(prevProps, prevState, snapshot): 组件更新时调用
			<br>获取真实的DOM节点、获取远程数据。当使用getSnapshotBeforeUpdate()时，接收其返回结果作为第三个参数。
			<br>此时调用setState(), state是立即更新的
	3) Unmonting
		+ componentWillUnmount()
2. render()
	1) React elements：返回单一节点元素，如<div /> and <MyComponent />
	2) Array和Fragment: 返回多个元素
	3) String和number: 渲染为DOM的文本节点
	4) Portals: 把子组件挂载到不同的DOM树中
	5) Boolean和null：不渲染
3. class properties
	1) defaultProps: 为class中的实例属性props设置默认值，只调用一次
	2) displayName：用于调试信息，默认为类名或函数名，在高阶组件中需要显式的指定
### React.DOM
1. render
	<br>`ReactDOM.hydrate(element, container[, callback])`
2. hydrate `react 17`
	<br>`ReactDOM.hydrate(element, container[, callback])`
	+ 与render()相同，但用于混合容器，该容器的HTML内容是由ReactDOMServer渲染的。React将尝试将事件监听器附加到现有的标记。
3. findDOMNode()
	<br>`ReactDOM.findDOMNode(component)`
	+ 在组件挂载后可访问DOM节点，但是不能用于render()等未加载周期中
	+ 若render()返回多个元素如Fragment, findDOMNode()将返回第一个非空的子节点
4. createPortal(): 将子节点渲染到父组件DOM层次结构之外的DOM节点
	<br> `ReactDOM.createPortal(child, container)`
	+ 对于portal的一个典型用例是当父组件有overflow: hidden或z-index样式，但你需要子组件能够在视觉上"跳出(break out)"其容器。例如，对话框、hovercards以及提示框。
		```javascript
		render() {
		  return ReactDOM.createPortal(
		    this.props.children,
		    domNode,
		  );
		}
		```
5. unmountComponentAtNode()
	<br>`ReactDOM.unmountComponentAtNode(container)`
_____

# 优化技巧
1. 条件渲染时
	<br>可以用变量来存储元素。这可以帮助您有条件地渲染组件的一部分，而输出的其余部分不会更改。
2. 列表渲染
	<br>加入key， 唯一
3. 事件绑定
	+ 若这个回调被作为 prop(属性) 传递给下级组件，这些组件可能需要额外的重复渲染。我们通常建议在构造函数中进行绑定，以避免这类性能问题。
	+ arrow functions vs  Function.prototype.bind 绑定this
		```javascript
		<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
		<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
		/*
		参数e作为React事件对象将会被作为第二个参数进行传递。通过箭头函数的方式，事件对象必须显式的进行传递，
		但是通过bind的方式，事件对象以及更多的参数将会被隐式的进行传递。
		*/
		```
4. 性能优化
	1) 使用 Chrome性能分析工具 分析组件性能
		+ 在开发模式中，你可以在支持相关功能的浏览器中使用性能工具来可视化组件 装载(mount)，更新(update)和卸载(unmount)的各个过程
		+ 在开发模式在Chrome中操作如下
			+ 通过添加 ?react_perf 查询字段加载你的应用(例如：`http://localhost:3000/?react_perf`)。
			+ 打开 Chrome DevTools Performance并点击Record
			+ 执行你想要分析的操作，不要超过20秒，否则 Chrome 可能会挂起。
			+ 停止记录。
			+ 在User Timing标签下，React事件将会分组列出。
	2)  React DevTools
		+ 在开发者工具的控制台中，选择React选项卡中的Highlight Updates (高亮显示更新) 选项
		+ 当页面进行交互，会看到所有重新渲染的组件周围都会出现高亮显示的边框。 反过来，这可以让你知道没有必要重新渲染的组件。
5. 代码拆分
	1) React Loadable：包装一个很好的、React友好动态导入API，用于在给定组件中，将代码拆分引入到你的应用程序中。(高阶组件，基于组件分割)
		```javascript
		import Loadable from 'react-loadable';
		const LoadableOtherComponent = Loadable({
		  loader: () => import('./OtherComponent'),
		  loading: () => <div>Loading...</div>,
		});
		const MyComponent = () => (
		  <LoadableOtherComponent/>
		);
		```
