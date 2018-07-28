<a name="content">目录</a>

[jQuery入门笔记](#title)
- [jQuery 安装](#install-jquery)
- [jQuery 语法](#syntax)
	- [基本语法](#basic-syntax)
	- [文档就绪事件](#document-ready)
	- [jQuery选择器](#selectors)
	- [jQuery事件](#action)
- [jQuery 效果](#effect)
	- [隐藏和显示](#effect-hide-and-show)
		- [hide( ) 和 show( )](#action-hide-show)
		- [toggle( )](#action-toggle)














<h1 name="title">jQuery入门笔记</h1>

<p align="center"><img src=./picture/Beginning-jQuery-logo.jpg width=600 /></p>

jQuery 是一个 JavaScript 库。

jQuery 极大地简化了 JavaScript 编程。

<a name="install-jquery"><h2>jQuery 安装 [<sup>目录</sup>](#content)</h2></a>

jQuery 库是一个 JavaScript 文件，您可以使用 HTML 的 `<script>` 标签引用它：

```
<head>
<script src="/Path/Tojquery-1.10.2.min.js"></script>
</head>
```

- 可以将 jQuery 下载到本地，进行引用

	有两个版本的 jQuery 可供下载：
	
	> - Production version - 用于实际的网站中，已被精简和压缩。
	> - Development version - 用于测试和开发（未压缩，是可读的代码）
	
	以上两个版本都可以从 `jquery.com` 中下载。

- 通过 CDN（内容分发网络） 引用 jQuery

	BootCDN、百度、又拍云、新浪、谷歌和微软的服务器都存有 jQuery 。如果你的站点用户是国内的，建议使用百度、又拍云、新浪等国内CDN地址，如果你站点用户是国外的可以使用谷歌和微软。

	它们的引用地址如下：
	
	```
	BootCDN	https://cdn.bootcss.com/jquery/1.10.2/jquery.min.js
	
	百度CDN	https://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js
	
	又拍云CDN	http://upcdn.b0.upaiyun.com/libs/jquery/jquery-2.0.2.min.js
	
	新浪CDN	http://lib.sinaapp.com/js/jquery/2.0.2/jquery-2.0.2.min.js
	
	GoogleCDN	http://ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js
	```

<a name="syntax"><h2>jQuery 语法 [<sup>目录</sup>](#content)</h2></a>

通过 jQuery，您可以选取（查询，query） HTML 元素，并对它们执行"操作"（actions）。

<a name="basic-syntax"><h3>基本语法 [<sup>目录</sup>](#content)</h3></a>

基础语法： $(selector).action()

> - 美元符号定义 jQuery
> - 选择符（selector）"查询"和"查找" HTML 元素
> - jQuery 的 action() 执行对元素的操作

实例:

> - $(this).hide() - 隐藏当前元素
> 
> - $("p").hide() - 隐藏所有 <p元素
> 
> - $("p.test").hide() - 隐藏所有 class="test" 的 <p元素
> 
> - $("#test").hide() - 隐藏所有 id="test" 的元素

<a name="document-ready"><h3>文档就绪事件 [<sup>目录</sup>](#content)</h3></a>

```
$(document).ready(function(){
 
   // 开始写 jQuery 代码...
 
});
```

这是为了防止文档在完全加载（就绪）之前运行 jQuery 代码，即在 DOM 加载完成后才可以对 DOM 进行操作。

如果在文档没有完全加载之前就运行函数，操作可能失败。下面是两个具体的例子：

> - 试图隐藏一个不存在的元素
> - 获得未完全加载的图像的大小

简洁写法（与以上写法效果相同）

```
$(function(){
 
   // 开始写 jQuery 代码...
 
});
```

<a name="selectors"><h3>jQuery选择器 [<sup>目录</sup>](#content)</h3></a>

jQuery 选择器基于元素的 id、类、类型、属性、属性值等"查找"（或选择）HTML 元素

- 元素选择器

	jQuery 元素选择器基于元素名选取元素。
	
	在页面中选取所有 `<p>` 元素:
	
	```
	$("p")
	```
	
	示例：
	
	用户点击按钮后，所有 `<p>` 元素都隐藏
	
	```
	$(document).ready(function(){
	  $("button").click(function(){
	    $("p").hide();
	  });
	});
	```

- \#id 选择器

	jQuery #id 选择器通过 HTML 元素的 id 属性选取指定的元素。
	
	页面中元素的 id 应该是唯一的，所以您要在页面中选取唯一的元素需要通过 #id 选择器。
	
	通过 id 选取元素语法如下：
	
	```
	$("#test")
	```
	
	示例：
	
	```
	$(document).ready(function(){
	  $("button").click(function(){
	    $("#test").hide();
	  });
	});
	```

- .class 选择器

	jQuery 类选择器可以通过指定的 class 查找元素。
	
	语法如下：
	
	```
	$(".test")
	```

<a name="action"><h3>jQuery事件 [<sup>目录</sup>](#content)</h3></a>

|鼠标事件|键盘事件|表单事件|文档/窗口事件|
|:---:|:---:|:---:|:---:|
|click|keypress|submit|load|
|dblclick|keydown|change|resize|
|mouseenter|keyup|focus|scroll|
|mouseleave| ` ` |blur|unload|

常见的jQuery事件：

- **$(document).ready()**：$(document).ready() 方法允许我们在文档完全加载完后执行函数，这个之前提到过

- **click()**：click() 方法是当按钮点击事件被触发时会调用一个函数

- **dblclick()**：当双击元素时，会发生 dblclick 事件

- **mouseenter()**：当鼠标指针穿过元素时，会发生 mouseenter 事件

- **mouseleave()**：当鼠标指针离开元素时，会发生 mouseleave 事件

- **mousedown()**：当鼠标指针移动到元素上方，并按下鼠标按键时，会发生 mousedown 事件

- **mouseup()**：当在元素上松开鼠标按钮时，会发生 mouseup 事件

- **hover()**：hover()方法用于模拟光标悬停事件

	当鼠标移动到元素上时，会触发指定的第一个函数(mouseenter);当鼠标移出这个元素时，会触发指定的第二个函数(mouseleave)

	```
	$(document).ready(function(){
	    $("#p1").hover(
			function(){
				$("#p2").hide()
			},
			function(){
				$("#p3").hide()
			}
	    )
	});
	```

- **focus()**：当元素获得焦点时，发生 focus 事件

	当通过鼠标点击选中元素或通过 tab 键定位到元素时，该元素就会获得焦点。
	
	```
	$("input").focus(function(){
	  $(this).css("background-color","#cccccc");
	});
	```

- **blur()**：当元素失去焦点时，发生 blur 事件

<a name="effect"><h2>jQuery 效果 [<sup>目录</sup>](#content)</h2></a>

<a name="effect-hide-and-show"><h3>隐藏和显示 [<sup>目录</sup>](#content)</h3></a>

<a name="action-hide-show"><h4>hide( ) 和 show( ) [<sup>目录</sup>](#content)</h4></a>

使用 hide( ) 和 show( ) 方法来隐藏和显示 HTML 元素

```
$(selector).hide(speed,callback);

$(selector).show(speed,callback); 
```

> - 可选的 speed 参数规定隐藏/显示的速度，可以取以下值："slow"、"fast" 或毫秒。
> 
> - 可选的 callback 参数是隐藏或显示完成后所执行的函数名称。

<a name="action-toggle"><h4>toggle( ) [<sup>目录</sup>](#content)</h4></a>

通过 jQuery，您可以使用 toggle() 方法来切换 hide() 和 show() 方法。

显示被隐藏的元素，并隐藏已显示的元素：

```
$("button").click(function(){
  $("p").toggle();
});
```

参考资料：

[菜鸟教程：jQuery](https://www.runoob.com/jquery/jquery-tutorial.html)



