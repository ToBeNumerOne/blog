# ES8 新特性一览
-

本文是一篇译文，搬运国外的优秀博文。主要讲解ES8(ES2017)新增的功能、特性。文章首发于[掘金](https://juejin.im/post/596713b75188250da20604cb)。

<img class="progressiveMedia-noscript js-progressiveMedia-inner" src="https://cdn-images-1.medium.com/max/2000/1*g3nPXrupuJ3koTjRNr6daw.png">

ES8或者说是ES2017已经在今年6月底的时候被TC39正式发布。似乎我们在最近的一年里就已经谈论了很多有关ECMA的事情。现在的ES标准每年发布一次。我们都知道ES6是在2015年发布的，ES7是在2016年发布的，但是估计会有很少数人知道ES5是在何时发布的。答案是2009年，是在JavaScript逐渐变的流行之前发布的。

JavaScript，作为一门处于高速发展期的开发语言，正在变的越来越完善、稳定。我们必须拥抱这些变化，并且我们需要把ES8加入到我们的技术栈中。

如果您想对ES8做一个深入、彻底的了解，您可以查阅[Web资源](https://www.ecma-international.org/ecma-262/8.0/index.html)或者[PDF资源](https://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf)。其他的读者，您可以直接查阅本文，因为本文将涵盖ES8主要的新特性，并且会附上代码示例。

## 字符串填充
在String对象中，ES8增加了两个新的函数：padStart和padEnd。正如其名，这俩函数的作用就是在字符串的头部和尾部增加新的字符串，并且返回一个**具有指定长度的新的字符串**。你可以使用指定的字符、字符串或者使用函数提供的默认值－空格来填充源字符串。具体的函数申明如下：

```javascript
str.padStart(targetLength [, padString])

str.padEnd(targetLength [, padString])
```

正如你所看到的，这俩函数的第一个参数（必输）是`targetLength`，这个参数指的是设定这俩函数最后返回的字符串的长度。第二个参数`padString`是可选参数，代表你想要填充的内容，默认值是空格。具体代码示例如下：

```javascript
'es8'.padStart(2);          // 'es8'
'es8'.padStart(5);          // '  es8'
'es8'.padStart(6, 'woof');  // 'wooes8'
'es8'.padStart(14, 'wow');  // 'wowwowwowwoes8'
'es8'.padStart(7, '0');     // '0000es8'

'es8'.padEnd(2);          // 'es8'
'es8'.padEnd(5);          // 'es8  '
'es8'.padEnd(6, 'woof');  // 'es8woo'
'es8'.padEnd(14, 'wow');  // 'es8wowwowwowwo'
'es8'.padEnd(7, '6');     // 'es86666'
```

目前浏览器的支持情况如下（信息来自MDN）：

<img class="progressiveMedia-noscript js-progressiveMedia-inner" src="https://cdn-images-1.medium.com/max/800/1*gR7YnK8_2yw2l2YZQiJkSA.png">

## values和entries函数
在Object中，ES8也新增了两个新的函数，分别是`Object.values`函数和`Object.entries`函数。`Object.values`函数将会返回一个数组，该数组的内容是函数参数（一个对象）可遍历属性的属性值。数组中得到的属性值的顺序与你在对参数对象使用`for in `语句时获取到的属性值的顺序一致。函数声明如下：

```javascript
Object.values(obj)
```

参数`obj`就是源对象，它可以是一个对象或者一个数组（因为数组可以看作是数组下标为key，数组元素为value的特殊对象）。具体的代码示例如下：

```javascript
const obj = { x: 'xxx', y: 1 };
Object.values(obj); // ['xxx', 1]

const obj = ['e', 's', '8']; // same as { 0: 'e', 1: 's', 2: '8' };
Object.values(obj); // ['e', 's', '8']

// when we use numeric keys, the values returned in a numerical 
// order according to the keys
const obj = { 10: 'xxx', 1: 'yyy', 3: 'zzz' };
Object.values(obj); // ['yyy', 'zzz', 'xxx']
Object.values('es8'); // ['e', 's', '8']
```

目前浏览器对于`Object.values`函数的支持情况如下（信息来自MDN）：

<img class="progressiveMedia-noscript js-progressiveMedia-inner" src="https://cdn-images-1.medium.com/max/800/1*Q-K5Cjjb9qnIviRmbn_Ccg.png">

介绍完`Object.values`函数，接下来继续介绍`Object.entries`函数。`Object.entries`函数与`Object.values`函数类似，也是返回一个数组，只不过这个数组是一个以源对象（参数）的可枚举属性的键值对为数组`[key, value]`的n行2列的数组。它的返回顺序与`Object.values`函数类似。它的函数声明如下：

```javascript
Object.entries(obj)
```

示例代码如下：

```javascript
const obj = { x: 'xxx', y: 1 };
Object.entries(obj); // [['x', 'xxx'], ['y', 1]]

const obj = ['e', 's', '8'];
Object.entries(obj); // [['0', 'e'], ['1', 's'], ['2', '8']]

const obj = { 10: 'xxx', 1: 'yyy', 3: 'zzz' };
Object.entries(obj); // [['1', 'yyy'], ['3', 'zzz'], ['10': 'xxx']]
Object.entries('es8'); // [['0', 'e'], ['1', 's'], ['2', '8']]
```

目前浏览器对于`Object.entries`函数的支持情况如下（信息来自MDN）：

<img class="progressiveMedia-noscript js-progressiveMedia-inner" src="https://cdn-images-1.medium.com/max/800/1*QROuy9LbQuGS4Z_vUDztDA.png">

## getOwnPropertyDescriptors函数
Object中还有一个新成员，那就是`Object.getOwnPropertyDescriptors`函数。该函数返回指定对象（参数）的所有**自身属性描述符**。所谓自身属性描述符就是在对象自身内定义，不是通过原型链继承来的属性。函数声明如下：

```javascript
Object.getOwnPropertyDescriptors(obj)
```

`obj`参数即为源对象，该函数返回的每个描述符对象可能会有的key值分别是：`configurable`、`enumerable`、`writable`、`get`、`set`和`value`。示例代码如下：

```javascript
const obj = { 
  get es7() { return 777; },
  get es8() { return 888; }
};
Object.getOwnPropertyDescriptor(obj);
// {
//   es7: {
//     configurable: true,
//     enumerable: true,
//     get: function es7(){}, //the getter function
//     set: undefined
//   },
//   es8: {
//     configurable: true,
//     enumerable: true,
//     get: function es8(){}, //the getter function
//     set: undefined
//   }
// }
```

描述符数据非常重要，尤其是在装饰器上。该函数的浏览器支持情况如下（信息来自MDN）：

<img class="progressiveMedia-noscript js-progressiveMedia-inner" src="https://cdn-images-1.medium.com/max/800/1*S5kbcy_dAqPJXqHs-9ZTMw.png">

## 结尾逗号
此处结尾逗号指的是在函数参数列表中最后一个参数之后的逗号以及函数调用时最后一个参数之后的逗号。ES8允许在函数定义或者函数调用时，最后一个参数之后存在一个结尾逗号而不报`SyntaxError`的错误。示例代码如下： 

> 函数声明时

```javascript
function es8(var1, var2, var3,) {
  // ...
}
```

> 函数调用时

```javascript
es8(10, 20, 30,);
```

ES8的这项新特性受启发于对象或者数组中最后一项内容之后的逗号，如`[10, 20, 30,]`和`{ x: 1, }`。

> 译者注：
> 
> 个人暂时还没有想到这个新功能特别的用武之地，如果读者对于此功能有独到的见解，欢迎交流！

## 异步函数
由`async`关键字定义的函数声明定义了一个可以异步执行的函数，它返回一个`AsyncFunction`类型的对象。异步函数的内在运行机制和`Generator`函数非常类似，但是不能转化为`Generator`函数。
> ps: 不理解`Generator`函数的读者可以参考[阮一峰大师的ES6入门中关于Generator函数的讲解](http://es6.ruanyifeng.com/#docs/generator)

示例代码如下：

```javascript
function fetchTextByPromise() {
  return new Promise(resolve => { 
    setTimeout(() => { 
      resolve("es8");
    }, 2000);
  });
}
async function sayHello() { 
  const externalFetchedText = await fetchTextByPromise();
  console.log(`Hello, ${externalFetchedText}`); // Hello, es8
}
sayHello();
```

上述代码中，`sayHello`函数的调用将会导致在2秒之后打印`Hello, es8`。继续来看一段代码：

```javascript
console.log(1);
sayHello();
console.log(2);
```

输出将会变成：

```javascript
1 // immediately
2 // immediately
Hello, es8 // after 2 seconds
```

之所以会打印上述内容，那是因为异步函数不会阻塞程序的继续执行。
> 译者注：
> 
> 此处打个小广告，如果有读者对于JavaScript的异步机制还有不明白的地方，可以参考本人的一篇博客[javascript异步机制](https://github.com/ToBeNumerOne/blog/blob/master/js-async.md)，里面是本人关于异步机制的一点拙见,相信会对您有一点启发。欢迎指正与交流！

**需要注意的是异步函数会一直返回`promise`对象并且`await`可能只会在被`async`关键字定义的函数中使用。**异步函数的浏览器支持情况如下（信息来自MDN）：

<img class="progressiveMedia-noscript js-progressiveMedia-inner" src="https://cdn-images-1.medium.com/max/800/1*o9uz3ul-hxd4zDL6ADVCow.png">

## 共享内存与原子操作
当内存被共享时，多个线程可以并发读、写内存中相同的数据。原子操作可以确保那些被读、写的值都是可预期的，即新的事务是在旧的事务结束之后启动的，旧的事务在结束之前并不会被中断。这部分主要介绍了ES8中新的构造函数`SharedArrayBuffer`以及拥有许多静态方法的命名空间对象`Atomic`。

`Atomic`对象类似于`Math`对象，拥有许多静态方法，所以我们不能把它当做构造函数。`Atomic`对象有如下常用的静态方法：

* add /sub - 为某个指定的value值在某个特定的位置增加或者减去某个值
* and / or /xor - 进行位操作
* load - 获取特定位置的值

该部分的浏览器兼容情况如下（信息来自MDN）：

<img class="progressiveMedia-noscript js-progressiveMedia-inner" src="https://cdn-images-1.medium.com/max/800/1*YQ8a02yltTM1Vfphdik5_g.png">

## 取消模版字符串限制（ES9）
使用ES6中规定的模版字符串，我们可以做如下事情：

```javascript
const esth = 8;
helper`ES ${esth} is `;
function helper(strs, ...keys) {
  const str1 = strs[0]; // ES
  const str2 = strs[1]; // is
  let additionalPart = '';
  if (keys[0] == 8) { // 8
    additionalPart = 'awesome';
  }
  else {
    additionalPart = 'good';
  }
  
  return `${str1} ${keys[0]} ${str2} ${additionalPart}.`;
}
```

上述代码的返回值将会是`ES 8 is awesome`。如果esth是7的话，那么返回值将会是`ES 7 is good`。这样做完全没有问题，很酷！但是我们在使用模版字符串的时候，有一个限制，那就是不能使用类似于`\u 或者 \x`的子字符串，ES9正在处理这个问题。详情请查阅[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)或者[TC39文档](https://tc39.github.io/proposal-template-literal-revision/)。**模板字符串修正(非模板字符串)**的浏览器兼容情况如下（信息来自MDN）:

<img class="progressiveMedia-noscript js-progressiveMedia-inner" src="https://cdn-images-1.medium.com/max/800/1*uO1Rt_UtQWPaBCSnF9vA_g.png">

## 结论
JavaScript正处于高速发展中，时常会被更新。我们必须准备好接受、拥抱JavaScript的新特性。最后，上述这些特性被TC39委员会所确认以及被一些核心开发人员所实现。甚至许多新特性现在已经成为了TypeScript、浏览器以及一些语法糖的一部分，所以我们现在就可以尝试使用它们，积极拥抱新特性。

最后，你可以在[Medium](https://medium.com/@dormoshe)或者[Twitter](https://twitter.com/DorMoshe)上来关注我，进而查看更多有关 JavaScript 和 Angular 的文章。

> * 原文地址：[ES8 was Released and here are its Main New Features](https://hackernoon.com/es8-was-released-and-here-are-its-main-new-features-ee9c394adf66)
> * 原文作者：[Dor Moshe](https://hackernoon.com/@dormoshe)
> * 译文出自：[掘金翻译计划](https://github.com/xitu/gold-miner)
> * 译者： [Jason Cheng](https://github.com/ToBeNumerOne)