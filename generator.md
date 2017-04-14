# ES6 Generator

--------------
> es6中提供了generator函数,作为一种异步编程的解决方案.本文主要介绍Generator函数的基本概念及其在异步编程中的运用.


![image](https://github.com/ToBeNumerOne/blog/blob/master/https.jpg)

## 前言

最近,在学习基于redux-saga的dva框架,以便后续对apollo组件库进行改进.学习的过程中,发现其中使用到了generator函数.但是因为之前的项目中没有用到过此函数,所以对于该函数也不是特别了解.所以写下这篇学习笔记,来方便大家学习.

## 初识Generator函数

认识Generator函数之前,先来看一段代码.

```javascript
effects: {
    *query({ payload }, { select, call, put }) {
      yield put({ type: 'showLoading' });
      const { data } = yield call(query);
      if (data) {
        yield put({
          type: 'querySuccess',
          payload: {
            list: data.data,
            total: data.page.total,
            current: data.page.current
          }
        });
      }
    },
    *create(){},
    *'delete'(){},
    *update(){},
}

```

上述代码摘自dva提供的用户管理教程.因为dva底层封装了redux-saga,而redux-saga使用了Generator函数.Generator函数就是上述代码中的```*create(){}```.它等价于```create: function* (){}```.也就是说Generator函数就是```function* Foo(){}```的形式.细心地读者可能已经发现了他和js中普通函数的区别.那就是函数定义的时候多了一个*号,同时函数体中使用了yield语句.或许看到现在你可能有点迷糊,那么我们来看下下面这个简单的例子,各位读者可以打开浏览器控制台运行下.

```javascript
function* generate(){
	yield 'First';
	yield 'Second';
	yield 'Third';
	return 'Ending...';
}
let gen = generate();
gen.next();
```

运行上述代码,会使你对于Generator函数有一个直观的认识.首先说明下Generator函数的定义.es6没有明确的规定*号在什么位置,但是本文为了与普通函数统一,统一都把\*号直接写在function后面,而这也是大多数程序员的写法.

上述代码运行会输出一个对象```{value: "First", done: false}```,代表执行第一个next方法的时候,输出的值是First,同时done字段表示该函数还没有到达终止状态.既然提到了状态,你可以把Generator函数理解成一个状态机.其中与之配合的yield语句是一个个断点.同时上述```let gen = generate()```generate函数不会立即执行,而是返回一个generator迭代器,从而迭代generator函数这个状态机的每个状态.那么Generator函数是如何来执行呢?他是通过迭代器的next方法来执行的.例如上述代码```gen.next()```使得状态从初始状态达到下一个断点(yield),所以它会输出First,同时该函数暂未到达结束状态,所以done的值是false.接下来可以继续调用3次```gen.next()```,最终会输出```{value: "Ending...", done: true}```,此时代表Generator函数到达终止状态,同时输出return的值.如果继续调用```gen.next()```,会输出```{value: undefined, done: true}```,代表Generator函数目前已经处于终止状态,没有任何值可以输出.

## Generator函数基本概念

目前为止,相信大家已经对Generator函数有了一个直观的认识,接下来我们具体看下Generator函数中yield和next的用法.

### yield语句

首先声明的是Generator函数中可以不用yield语句.如下代码:

```javascript
function* generate(){
	console.log('无yield');
	return 'Ending...';
}
let gen = generate();
gen.next();
```

它通过next执行的时候会直接输出```无yield```,同时输出```{value: "Ending...", done: true}```.

**需要注意的是yield语句之后再Generator函数中生效,在其他函数中使用会报错!**

yield是暂停标志,相当于断点,将Generator分割成多个状态.他通常与next方法配合使用.配合的规则如下:

* 初次执行next方法是,函数开始执行,知道下一个yield(断点)或者函数结尾
* next方法执行时,遇到yield语句,会暂停函数的执行,同时将yield语句之后表达式的值作为返回对象的value值
* 再次调用next时候,会从目前的断点处开始执行,直到下一个yield或者函数结尾,也就是说Generator函数具备记忆功能
* 如果遇到return语句,将返回值作为返回对象value的值,同时状态设置为true,表示到达结束状态
* 如果没有return语句或者已经到达了结束状态,value值为undefined,同时保持结束状态,即done为true

yield语句和return语句的区别就是yield语句可以执行多个,return只能执行一个.return语句执行完即函数结束,yield语句执行完会继续执行之后的语句,直到函数结束.

yield语句如果存在于一个表达式中,**必须得用圆括号包住**,如下代码:

```javascript
function* generate(){
	console.log('表达式' + (yield));
	console.log('表达式' + (yield 'Test'));
	return 'Ending...';
}
let gen = generate();
gen.next();
```

yield语句如果作为函数实参或者赋值表达式右侧的内容,可以不用加圆括号.如下代码:

```javascript
function* generate(){
	func(yield 'request');
	let a = yield 'b';
	return 'Ending...';
}
let gen = generate();
gen.next();
```

### next方法

yield语句本身没有返回值,即在Generator函数中执行```let a = yield 'First'```的时候,a的值是undefined.如下代码:

```javascript
function* sum(a){
	let b = yield 3;
	return a+b;
}
let gen_sum = sum(1);
gen_sum.next();
gen_sum.next();
```

上述代码返回的是```{value: NaN, done: true}```.可见b并没有赋值成功.解决办法就是next方法携带参数作为上一个yield语句的返回值.所以对上述代码做一个改造就会得到我们想要的结果,改造如下:

```javascript
function* sum(a){
	let b = yield 3;
	return a+b;
}
let gen_sum = sum(1);
gen_sum.next();
gen_sum.next(3);
```

最终就会输出```{value: 4, done: true}```.

所以**通过next方法可以达到向Generator函数动态的注入参数的目的**

### for ... of 语句

Generator函数返回的是一个迭代器对象,如上一段代码中```let gen_sum = sum(1);```,gen_sum是一个迭代器对象,随意它调用next方法可以依次遍历Generator函数的状态.讲到依次遍历,大家可能会想到for...of循环,因为依次迭代与循环十分相似.那么接下来就讲讲for ... of 循环与Generator函数.还是先来看一段代码:

```javascript
function* show(){
	yield 'a';
	yield 'b';
	yield 'c';
	yield 'd';
	return 'e';
}

for ( let i of show() ){
	console.log(i);
}
```

上述代码会依次输出a,b,c,d.首先需要解释的是for ... of 循环结束的条件是done为true.所以最后一次是return语句,done为true,所以不会打印输出.其次是a,b,c,d相当于next执行之后的value值.

### yield* 语句

到此为止,相信大家对于Generator函数已经有了一定的了解.大家可能会文,Generator函数是否可以向普通函数一样互相调用,答案是肯定的.Generator函数中可以调用别的Generator函数,具体怎么调用,就需要用到yield* 语句.先来看一段代码:

```javascript
function* func1(){
	yield '2';
	yield '3';
	return '3.5';
}

function* func2(){
	yield '1';
	yield* func1();
	yield '4';
}

// 上述代码也等同于
function* func2(){
	yield '1';
	for ( let i of func1() ){
		yield i;
	}
	yield '4';
}

let gen_func = func2();
gen_func.next();
gen_func.next();
gen_func.next();
gen_func.next();
```

运行上述代码,将会依次输出1,2,3,4.证明调用成功!

**可以看到由于yield\*等价于for ... of 循环,所以最后return的值没有输出**

如果想要获取返回值,则需要对上述代码做一些改进.改进如下:

```javascript
function* func1(){
	yield '2';
	yield '3';
	return '3.5';
}

function* func2(){
	yield '1';
	let end = yield* func1();
	console.log(end);//此处就会输出3.5
	yield '4';
}

let gen_func = func2();
gen_func.next();
gen_func.next();
gen_func.next();
gen_func.next();
```

可以看到,我们可以通过let xxx = yield* func()的形式获取return的值.

**yield\* 后面亦可以紧跟有迭代器接口的数据结构,所以yield\*后面可以跟数组之类的**大家可以自己试一下.

## Generator函数的异步应用

所谓Generator函数的异步应用,就是利用Generator函数的"断点"效果,把异步的操作写在yield语句里面,这样就不要编写回调函数,从而有效的避免"回调地狱"的问题(至于回调地狱是啥,大家可以自行百度).

讲到回调函数多层嵌套(回调地狱),大家可能首先想到的是使用promise.而promise只是对于回调函数方式的改进,它使用的是then方法.通过使用一系列连续的then方法,使得异步操作可以通过同步的方式来编写代码.但是通过使用一堆then方法,也会使得源代码的语义变得有点模糊.

关于Generator函数对于异步任务的封装,大家可以先看下如下代码:

```javascript
function* request(){
  var url = 'https://xxx.com';
  var result = yield fetch(url);
  console.log(result);
}
var it = request();
var result = it.next();

result.value.then(function(data){
  return data.json();
}).then(function(data){
  g.next(data);
});
```

上述代码,我们使用fetch方法发起一个异步请求,利用yield断点的功能,将发起请求与异步处理的第二阶段分开.上述代码中,第一次调用next方法返回的是promise对象,所以接下来使用then方法来继续调用next方法,可能解释的有点绕口,大家可以自己看下上述代码.

总之无论Promise还是Generator,他们对于异步任务主要的用武之地就是同步化的代码表现.

## 结语

讲到这里,相信大家对于Generator函数有了一定了解.方便大家查阅有关基于Generator的技术,如redux-saga.同时也方便大家改进自己项目的异步任务.当然Generator函数还有其他的特性,如抛出异常机制等,这就需要读者自己去研究,本文不做解释.

本文写的不足之处,欢迎大家指正!






