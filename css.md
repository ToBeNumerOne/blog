# css那些事儿

----
> 本文重在讲述能提高前端工作效率的css技巧。


## 前言

css是前端开发工作中必不可少的一部分，同时也是最容易被忽略的一部分。现如今，各种优秀的前端框架面世，导致许多前端开发人员蜂拥学习前端框架，却忽视了css这块。包括笔者自己也是，认为css够用即可，必要时直接百度就行。其实这种心态是不正确的，因为有时候百度出来的css，其实不是最优的，或者不利于后期维护。所以我们应该正视css。本文主要讲解在实际开发过程中，能提高工作效率的几个css技巧。

## 选择器

合理的利用伪元素选择器，可以使我们的代码更加简洁，语义话更佳明显。

### :empty

> 不支持ie8。

empty，就是空的意思。顾名思义，我们可以使用`:empty`来选择空元素。空元素就是没有标签内容的元素，如：`<div class='name'></div>`。

例如，我们目前拥有如下元素：

```
<li class='name'>li</li>
<li class='name'></li>
```

我们可以使用`.name:empty`来设置第三个li标签的样式，具体代码如下：

```
.name:empty{
	width: 100px;
	height: 200px;
	background-color: red;
	...
}
```

### :not

> 不支持ie8。

可以理解为非，如对非空元素的选择：`:not(:empty)`。

上述代码中，对于非空元素的样式设置如下：

```
.name:not(:empty){
	width: 10px;
	height: 10px;
	background-color: green;
	...
}
```

### :nth-of-type、:first-of-type、:last-of-type、:only-of-type

上述选择器分别为某种类型的第n个、第一个、最后一个、唯一的。此类选择器前面可以跟标签选择器、类选择器、id选择器。

其中`:nth-of-type`还可以携带参数，如：

```
:nth-of-type(even) // 表示某种类型的偶数个
:nth-of-type(3) // 表示某种类型的第三个
```


> 使用demo
> 
> div:first-of-type // 选中第一个div
> 
> img:last-of-type // 选中最后一个img标签

### [attr ** val]方括号选择器

首先声明，方括号选择器是笔者本人为了方便记忆所起的名字。该选择器如上表示，中间的`**`部分可以是`=、|=、^=、*=、$=、~=`。他们依次表示为：

* `=` 是attr严格等于val，完全匹配。
* `|=` 是attr的值以val开头，val必须为一整个单词或者后跟－连字符。
* `^=` 是attr的值以val开头，与`|=`的区别是val可以是一部分字符串。
* `*=` 是attr的值包含val。
* `$=` 是attr的值以val结束。
* `~=` 是attr的值包含val，此处val为一个单独的单词。

> 如：`div[class^=h]`表示匹配class属性值以h开头，并且是div元素。稍微有点绕口，请大家仔细体会。

### :first-child、:last-child、:nth-child、nth-last-child

上述选择器依次表示：

* 父元素的第一个字元素。
* 父元素的最后一个子元素。
* 第n个子元素。
* 顺序从后往前，第n个子元素。

> 如设置p标签的字体大小为20px，并且p标签作为第一个子元素。
> 
```
p:first-child{
	font-size: 20px;
}
```

### :only-child

匹配唯一子元素。

> 如设置p标签的字体大小为20px，并且p标签作为唯一子元素。
> 
```html
<div><p>111</p></div>
<div><p>111</p><div></div></div>
```
> 使用`p:only－child`选择器会匹配到第一个div标签下的p标签，而不会匹配第二个div下的。

### :root

设置html标签。

> 如设置html的字体大小为20px。
> 
```
:root{
	font-size: 20px;
}
```

## 关键点



## 命名规范

## 常见示例



