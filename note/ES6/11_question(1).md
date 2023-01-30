# 考核问题解决(1) | 集创第十一篇考核

​	由于进入了考核制作的阶段，没有再进一步进行系统的学习，更多的只是 **遇到问题 -> 解决问题** 的过程，考核过程用大量运用了css和JavaScript，所以从第十一篇开始，着重梳理一下遇到的一些问题，以下分为三个模块(html,css,javascript)。

---

## html

1.当我们使用a标签的时候，往往会给他添加上点击带来的特效（弹出窗口，跳转其他页面）等。大多数时候我们并不希望页面会跳转，所以可以有以下几种做法：

```javascript
<a href="http://www.baidu.com" onclick="return false">点我不跳转</a>

//onclick方法负责执行js函数，void(0)返回undefined，地址不发生跳转。
//或者在js_method 中最后一行加入 return false
<a href="javascript:void(0);" onclick="js_method"</a>

<a href="javascript:;" onclick="js_method"></a>

//跳转到当前页面的顶部，不推荐使用
<a href="#" class="demo" onclick="del()">删除</a>
```

---

## CSS

1.当我们给一个元素:hover 添加一个边框的时候，元素常常会因为边框的加入，重新计算样式布局，容易造成元素内内容抖动，非常的影响美观。

```css

/*
	我们可以先给box元素添加一个与背景色一样的边框，然后在hover中进行更	改，这样就不会出现令人不安的抖动现象了。
*/
#box {
	width:200px;
	height:200px;
	background-color:red;
	padding:15px;
	border:10px solid red;
}

#box:hover {
	border-color:white;
}

```

2.当我们在使用一个div等其他元素包含另一含字元素时，经常会出现字体向左上方靠拢，这时候我们只需要把行高调整为和父元素一致就可以了。

```css
#father {
	width:50px;
	height:50px;
	background-color:green;
}

#father a {
	line-height:50px;
}
```

3.当我们制作导航栏的时候，往往会出现ul元素左侧出现一小段间距的问题，我们可以使用以下方法轻松解决。我在查询的时候发现可以将 ul 替换成 lh 元素，也可以达到同样的效果，但是却没查到 lh 是哪种标签。

```css
ul {
	margin:0;
	padding:0;
}
```

4.有时候我们已经定好宽高了，但是子元素使用了padding导致了内容的溢出，超出了父元素的范围，我们仅需向该元素中添加box-sizing：border-box;的属性，来限制一下盒子的尺寸就行了。

