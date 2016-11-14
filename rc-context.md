# Using context in reactjs

## 前言

最近在做react项目的过程中，需要使用context的相关功能。由于对context的理解不是特别深刻，所以上网查阅了react中context的介绍的一些相关资料。发现所找到的资料中，还是[Facebook官网对于context的介绍](https://facebook.github.io/react/docs/context.html)简单明了，所以抱着半翻译半理解的心态写下这篇文章，方便自己以后查阅以及方便其他的reactjs开发者。

## Context的介绍

首先context，翻译成中文是上下文。顾名思义，context拥有承上启下的意思。具体承上启下体现在以下场景：

--------------
> 假设我们拥有组件A，它所处于的组件树种拥有很多后代组件。假设组件B是其孙子组件，我 们要从祖先组件A组件传递信息给其后代组件B组件。其中一种方法就是手动通过传递props
> 的方式传递。这种方式肯定能达到我们传递信息的目的。但是试想一下，如果B组件与A组件
> 之间相隔很多代，这样的传递方式就会显得极其麻烦、不优雅，以及对于代码维护也会产生
> 较高的成本。

使用context API，可以使我们**直接**传递信息，省去繁琐的手动传递props的过程，使我们可以更加简单的利用react中单向的数据流。

## 为什么不推荐使用context

react官方文档关于context的介绍中写道：context目前还是一个处于试验阶段的API，有可能在后期react发布的版本中对其进行修改。如果要使用context，我们可以在项目中一小块隔离的区域中使用，这样便于对context后期的维护和升级。

官方不推荐使用的原因很明确，就只有一个。就是有可能会修改context的API，造成对已有项目进行维护增加成本的问题。但是针对这个问题，网上也有相应的解决办法，就是HOC（Higher Order Component）。**HOC简单来说就是使用组件包裹器的方式，即创建一个接收原有组件为参数的函数，返回一个新的组件的方式，从而达到将组件与context解耦的目的，便于后期维护**。读者如果对此有兴趣，可以自行查阅相应的资料。

## react中context的用法

上述讲解了context的一些背景，现在正式进入主题，介绍react中context的用法。

先来看一下官方提供的代码示例：

```javascript
class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.props.color}}>
        {this.props.children}
      </button>
    );
  }
}

class Message extends React.Component {
  render() {
    return (
      <div>
        {this.props.text} <Button color={this.props.color}>Delete</Button>
      </div>
    );
  }
}

class MessageList extends React.Component {
  render() {
    const color = "purple";
    const children = this.props.messages.map((message) =>
      <Message text={message.text} color={color} />
    );
    return <div>{children}</div>;
  }
}
```

上述代码是我对于context的介绍中关于手动传递props场景的还原。三级组件之间我们需要手动传递两次props，多级的话就会显得更加麻烦。使用context对其改造之后的代码如下：

```javascript
class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.context.color}}>
        {this.props.children}
      </button>
    );
  }
}

Button.contextTypes = {
  color: React.PropTypes.string
};

class Message extends React.Component {
  render() {
    return (
      <div>
        {this.props.text} <Button>Delete</Button>
      </div>
    );
  }
}

class MessageList extends React.Component {
  getChildContext() {
    return {color: "purple"};
  }

  render() {
    const children = this.props.messages.map((message) =>
      <Message text={message.text} />
    );
    return <div>{children}</div>;
  }
}

MessageList.childContextTypes = {
  color: React.PropTypes.string
};
```

可以看到使用context改造之后的代码**更加优雅以及更加直接**。上述代码中，祖先代码使用了childContextTypes和getChildContext两个API。我们可以将context简单理解成一个对象。关于context的用法简而言之就是祖先组件中通过childContextTypes定义context对象中各个字段的类型，通过getChildContext定义context对象中各个字段的值。这样我们在祖先组件中就生成了一个可以在组件树中传递的context对象，**即祖先组件是context对象的提供者**。后代组件如果要使用context对象的时候，只需要在其组件定义的代码中显式声明contextTypes，并且在contextTypes中**显式的指定你所需要用到的字段**即可。

具体有关context的讲解也可以参考[rayswim博客中有关context的介绍](https://github.com/rayswim/blog/blob/master/src/React%20Components%E4%B9%8B%E9%97%B4%E5%A6%82%E4%BD%95%E8%BF%9B%E8%A1%8C%E9%80%9A%E4%BF%A1.md)。

## context对于组件生命周期函数的影响

当我们在组件中明确定义了contextType的时候，我们可以在以下生命周期函数中传递context参数来使用context。

* constructor(props, context)
* componentWillReceiveProps(nextProps, nextContext)
* shouldComponentUpdate(nextProps, nextState, nextContext)
* componentWillUpdate(nextProps, nextState, nextContext)
* componentDidUpdate(prevProps, prevState, prevContext)

## context在无状态函数性组件中的使用

我们在定义组件的时候可以通过函数声明的方式来定义，并非只有通过es6中class的方式以及es5的方式。我们在使用函数声明的方式来定义组件的时候，如果显式声明contextTypes，那么在其定义的函数参数中，可以传递context，从而在组件中使用context。具体还是来看下官方提供的Button组件：

```javascript
const Button = ({children}, context) =>
  <button style={{background: context.color}}>
    {children}
  </button>;

Button.contextTypes = {color: React.PropTypes.string};
```

## 更新context对象

当祖先组件（**其中定义了getChildContext**）中的state或者props改变的时候，如果getChildContext中定义的值是state或者props中的内容，那么getChildContext函数将会被调用从而重新设置context对象。所以我们可以通过结合state等来更新context对象，并且这种改变或被后代组件接收到。

继续看一下官方提供的代码片段：

```javascript
class MediaQuery extends React.Component {
  constructor(props) {
    super(props);
    this.state = {type:'desktop'};
  }

  getChildContext() {
    return {type: this.state.type};
  }

  componentDidMount() {
    const checkMediaQuery = () => {
      const type = window.matchMedia("(min-width: 1025px)").matches ? 'desktop' : 'mobile';
      if (type !== this.state.type) {
        this.setState({type});
      }
    };

    window.addEventListener('resize', checkMediaQuery);
    checkMediaQuery();
  }

  render() {
    return this.props.children;
  }
}

MediaQuery.childContextTypes = {
  type: React.PropTypes.string
};
```
上述代码就是结合state来更新context对象。但是*此时有一个问题*：

```
我们在组件树种传递更新之后的context对象的时候，如果某个中间级的组件组件在shouldComponentUpdate函数中返回false，那么后代组件不会更新context中的值，即后代组件接受不到context的更新。这种情况下，祖先以及后代组件传递消息的过程就会变得不可控。
```

所以我们在使用context的时候需要谨慎使用。具体可以参考[How to safely use React context](https://medium.com/@mweststrate/how-to-safely-use-react-context-b7e343eff076#.hocg2crnm)

## context.router

我们在使用react-router的时候，为了方便控制路由跳转。可以在contextTypes中定义router字段，从而可以使用goBack、push等函数。具体可以参考如下代码：

```javascript
static contextTypes = {
	router: React.PropTypes.any
};

onTap = () => {
	const router = this.context.router;
	router.goBack();
}
```

## 结论

使用context确实给我们项目带来很多便利，虽然针对context API可能会变以及更新context对象的时候可能会存在问题已经有比较不错的解决方案，但是我们还需要谨慎使用context API

## 参考资料

* [context官方文档](https://facebook.github.io/react/docs/context.html)
* [How to safely use React context](https://medium.com/@mweststrate/how-to-safely-use-react-context-b7e343eff076#.hocg2crnm)
* [Introduction to Contexts in React.js](https://www.tildedave.com/2014/11/15/introduction-to-contexts-in-react-js.html)
* [How to handle React context in a reliable way](https://medium.com/react-ecosystem/how-to-handle-react-context-a7592dfdcbc#.7prp784s1)
* [Higher Order Components: A React Application Design Pattern](https://www.sitepoint.com/react-higher-order-components/)
