# 如何做出真正的1px边框(super细的线)
## 问题陈述
这段时间在做移动端的一个项目.发现在写CSS的过程中,border在设置成1px的情况下,如```border: solid 1px #f00```,在安卓等机型可以正常显示,但是在iphone这类的retina屏幕上,则会显示的粗一些.经过查询发现,这个问题是一个比较普遍的问题,并且网上针对此问题也有一些比较完善的解决方案的总结.所谓前人种树,后人乘凉.现在借鉴前人针对此问题的总结,在此重新整理下解决方案,方便笔者以及其他读者在之后的开发中快速定位以及解决该问题.
## 解决方案
### 1, viewport + rem实现
在我们做移动端项目的时候,会用到```<meta name="viewport" content="width=device-width,initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">```.该中方法就是通过js检测devicePixelRatio来动态设置viewport以及font-size.具体代码如下:

```javascript
var viewport = document.querySelector("meta[name=viewport]");  
//下面是根据设备像素设置viewport  
if (window.devicePixelRatio == 1) {  
	// 设置viewport
}  
if (window.devicePixelRatio == 2) {  
	// 设置viewport
}  
if (window.devicePixelRatio == 3) {  
	// 设置viewport  
}  
// 设置font-size,方便使用rem
var docEl = document.documentElement;  
var fontsize = 10 * (docEl.clientWidth / 320) + 'px';  
docEl.style.fontSize = fontsize;  
```

该方法适合重启项目或者进行技术选型时使用,如果是升级老的项目则会造成改动成本较大的问题.
### 2, .5px实现
众所周知,retina的devicePixelRatio是2,即我们在设置1px的时候,设备实际显示的是2px像素.如此,我们可以设置border为.5px来实现1px的线.同理,如果devicePixelRatio是3的时候,可以设置为0.3333333333333来实现.具体代码如下:

```css
.border { border: 1px solid #f00 }
@media screen and (-webkit-min-device-pixel-ratio: 2) {
    .border { border: 0.5px solid #f00 }
}
@media screen and (-webkit-min-device-pixel-ratio: 3) {
    .border { border: 0.3333333333333px solid #f00 }
}
```

### 3, :before/:after + transform实现
该方法原理是原来的元素不设置border,通过伪元素选择器:after或者:before和css的transform来实现.此方法中,原来元素设置position:relative,伪元素:after或者:before设置为position:absolute,如此来调节border的位置,同时transform来缩小border到.5px.具体代码如下:

```css
.1px{
position: relative;
border:none;
}
.1px:after{
content: '';
position: absolute;
bottom: 0;
background: #f00;
width: 100%;
height: 1px;
-webkit-transform: scaleY(0.5);
transform: scaleY(0.5);
-webkit-transform-origin: 0 0;
transform-origin: 0 0;
}
```

### 4, border-image实现
该方法更多的是依赖于边框图片,如准备一个2px高,上面1px透明,下面为实际边框颜色(设置底部边框的情况).该方法的前提是在retina屏的情况下,所以在使用该方法的时候还需要做一个适配.代码如下:

```
.1px {
border-bottom: 1px solid #f00;
}
@media only screen and (-webkit-min-device-pixel-ratio: 2) {
.1px {
border-bottom: none;
border-width: 0 0 1px 0;
-webkit-border-image: url(XXXXXXXX) 0 0 2 0 stretch;
border-image: url(XXXXXXXX) 0 0 2 0 stretch;
}
}
```

### 5, gradient实现
该方法采用css3渐变的原理来实现,设置50%为实际边框颜色,50%透明,与设置border-image的方式类似,具体代码如下:

```
.1px{
background: -webkit-gradient(linear, left top, left bottom, color-stop(.5, transparent), color-stop(.5, #c8c7cc), to(#c8c7cc)) left bottom repeat-x;
background-size: 100% 1px;
}
```


## 结语
上述为实现真正1px线的方案,当然还有其他的解决方案,笔者在此没有一一罗列.如果有需要读者可以自寻查阅以及查阅相关方案的优劣性,一次来决定在项目中使用哪种方案.

References:

* [7种方法解决移动端Retina屏幕1px边框问题](http://www.jianshu.com/p/7e63f5a32636)
* [移动端1px细线解决方案总结](http://www.cnblogs.com/lunarorbitx/p/5287309.html)
* [ali-flexible](https://github.com/amfe/lib-flexible)
* [再谈mobile web retina 下 1px 边框解决方案](http://blog.csdn.net/huang100qi/article/details/47355277)
