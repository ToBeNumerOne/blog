# react中this关键字的用法

## 前言

在写react组件的时候，经常会遇到使用this关键字的情况。在使用React.createClass语法创建组件的时候，由于此形式会autobinding关键字this的上下文，所以以往写react组件的时候不必过多的关注组件中this的用法。

随着es6的火热以及自己参与的项目的架构设计，我们采用es6的形式来开发react的应用。不得不说，使用es6确实给我们带来许多便利。同时，react的[官方声明](https://facebook.github.io/react/docs/reusable-components.html#no-autobinding)中也推荐我们使用es6的class语法创建组件:

```
When you don't need local state or lifecycle hooks in a component, we recommend 
declaring it with a function. Otherwise, we recommend to use the ES6 class
syntax.
```


但是，所有事物都具有两面性，es6的形式给我们带来许多便利的同时也给我们带来一些疑惑。**本文主要针对作者在用es6写react的组件的时候，遇到的this指向问题，做一些深入研究。**

## es6类创建组件
es6的语法与之前相比较，一个最明显的区别就是组件类的创建。我们使用继承于React.Component的形式而不是React.createClass的形式。

```javascript
// es6的形式
class Demo extends React.Component{
	render(){
		return (
			<div>
				...
			</div>
		)
	}
}
```

```javascript
// es5的形式
var Demo = React.createClass({
	render: function(){
		<div>
			...
		</div>
	}
})
```

## this指向问题再现

先看下面一段代码：

```javascript
class Demo extends Component{

  constructor(props){
    super(props);
    this.state = {
      children:[
          <p key={Math.random()*1000} >{Math.floor(Math.random()*1000)}</p>
      ],
      items:[
        <p key={Math.random()*1000} >{Math.floor(Math.random()*1000)}</p>,
        <p key={Math.random()*1000} >{Math.floor(Math.random()*1000)}</p>,
        <p key={Math.random()*1000} >{Math.floor(Math.random()*1000)}</p>,
        <p key={Math.random()*1000} >{Math.floor(Math.random()*1000)}</p>,
        <p key={Math.random()*1000} >{Math.floor(Math.random()*1000)}</p>
      ]
    };
  }

  onClickHandler(){
    let children = this.state.children;
    children.push(<p key={Math.random()*1000} >{Math.floor(Math.random()*1000)}</p>);
    this.setState({
      children:children
    });
  }

  onHandler(index){
    console.log('clicked');
  }

  render(){

    let children = this.state.children;
    let items = this.state.items;

    return (
        <div>
          <button onClick={this.onClickHandler} >add</button>
          <div>
            {children}
            <hr/>
            {items.map(function(item){
              return <a href="javascript:void(0)" key={Math.random()} onClick={this.onHandler}>{item}</a>
            }, this)}
          </div>
        </div>
    )
  }
}
```

当我们执行上述代码的时候，会报如下图所示的错误：

![error](https://github.com/ToBeNumerOne/blog/blob/master/this-error.png)

## this指向问题的解决方法

### 1、bind(this)的方式
该方式是在函数运行时显示注入this的方式。js中所有的函数都有bind方法，通过bind方法会返回一个函数，(**注意**bind()方法返回的是函数，不是代表立即执行，具体参考bind的用法)。通过bind(this)的形式，明确指定正确的上下文this。

上述问题代码的修改：

```javascript
render(){
    let children = this.state.children;
    return (
        <div>
          <button onClick={this.onClickHandler.bind(this)} >add</button>
          <div>
            {children}
          </div>
        </div>
    )
  }
```

### 2、箭头函数(es6)的方式
es6中引入了箭头函数。箭头函数实质上是一个函数表达式，它会绑定this到正确的上下文。不论函数嵌套多深，箭头函数都会有正确的上下文this。但是这种方式使得我们无法命名函数，使得我们调试以及使用的时候会造成一定的不方便。

```javascript
render(){
    let children = this.state.children;
    return (
        <div>
          <button onClick={() => {let children = this.state.children; children.push(<p key={Math.random()*1000} >{Math.floor(Math.random()*1000)}</p>); this.setState({children:children});}} >add</button>
          <div>
            {children}
          </div>
        </div>
    )
  }
```

### 3、es7的方式
根据es7绑定语法提案，以及现有的babel技术，使得我们在编写代码解决this指向问题的时候可以选择es7的形式。在es7(es2016)中，引入了::操作符。此操作符左侧是一个value，右侧是一个function。此操作符使用左侧的值来绑定右侧函数的this。关于bind操作符的代码具体见一下示例：

```javascript
render(){
    let children = this.state.children;
    return (
        <div>
          <button onClick={this::()=>{let children = this.state.children; children.push(<p key={Math.random()*1000} >{Math.floor(Math.random()*1000)}</p>); this.setState({children:children});}} >add</button>
          <div>
            {children}
          </div>
        </div>
    )
  }
```

### 4、函数指定this的方式
在javascript中，很多函数都可以指定this，如数组的map函数，我们可以在map函数中显示的传递this值。

```javascript
render(){
    let children = this.state.children;
    let items = this.state.items;
    return (
        <div>
          <button onClick={this.onClickHandler} >add</button>
          <div>
            {children}
            <hr/>
            {items.map(function(item){
              return <a key={Math.random()} onClick={this.onHandler}>{item}</a>
            },this)}
          </div>
        </div>
    )
  }
```










