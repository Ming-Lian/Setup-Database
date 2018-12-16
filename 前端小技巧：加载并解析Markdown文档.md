<a name="content">目录</a>

[前端小技巧：加载并解析Markdown文档](#title)
- [问题描述与分析](#description)
- [用AJAX获取Markdown文档](#get-markdown-doc)
- [用表单指定Markdown文档](#specify-markdown-doc)
- [引用marked框架解析Markdown文档](#trans-markdown2html)
- [附：完整的实现脚本](#full-script)




<h1 name="title">前端小技巧：加载并解析Markdown文档</h1>

<a name="description"><h2>问题描述与分析 [<sup>目录</sup>](#content)</h2></a>

作为程序猿，应该多多少少都用过Markdown，或者至少读过别人用Markdown语法写的一些文档，比如在GitHub有一个你要用的开源程序，而你又是第一回用它，那么你一般会在这个仓库的Readme里读一读开发者提供的工具说明和使用的相关信息，这部分文档一般就是用Markdown的语法写的

<p align="center"><img src=./picture/MarkDown2HTML-markdown-example.png width=800 /></p>

<p align="center">此处以李恒大神的BWA的Readme为例</p>

简单来说，Markdown就是简化阉割过的HTML，优点是语法简单高效，缺点就是HTML中一些稍微高级复杂一点的效果，比如文本居中，Markdown就无法实现，所以Markdown一般被用来写对页面排版要求不高，以文字为主的笔记和文档

如果你一开始用Markdown写了文档，想要把它放到你的网页上去，并以解析后的形式呈现

<p align="center"><img src=./picture/MarkDown2HTML-transformation.png width=800 /></p>

<p align="center">左边是原始Markdown文档，右边是解析后的网页效果</p>

那么你有两种实现途径：

- 将Markdown文档以HTML形式导出，在网页上加载之前导出的HTML文件即可
- 原始网页获取Markdown文档，用js将Markdown文本解析成HTML文本，动态修改原始网页对应标签节点的内容，就实现了Markdown文本的解析和展示

第一种方法：

> 技术难度最小，也很容易理解，但是操作起来比较繁琐：你的Markdown文档一般是要经常修改更新的，那么每修改一次，你就需要重新导出，然后将之前导出的那一版HTML替换掉

第二种方法：

> - 技术难度上要高一些，但是目前已经有可以调用的JS框架来实现Markdown2HTML的解析
> - 最大的优点就是，每一次修改更新很方便，只需要对Markdown文档就可以了

下面我们对**第二种方法**的实现过程进行详细的说明

<a name="get-markdown-doc"><h2>用AJAX获取Markdown文档 [<sup>目录</sup>](#content)</h2></a>

上一部分已经提到，我们需要先让原始网页请求服务器中的Markdown文档

这一步需要使用AJAX（中文音译为阿甲克斯），这里先对AJAX作一个简单的科普：

> AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML），AJAX 是一种用于创建快速动态网页的技术
> 
> 首先要说清楚什么是动态网页。与动态网页相对的是静态网页，也就是大家都知道的用HTML写的网页，这种网页的一个鲜明的特点就是它的内容一旦确定就无法改变，在互联网发展的最早期的时候大家用的都是这种静态网页。动态网页要求页面的内容能进行更新。那么怎么让原先的静态网页动起来成为动态网页呢？也有两种方法：
> 
> - 重载整个网页面来更新页面内容
> - 上一个页面状态与下一个页面状态，它们之间有相同不变的部分，也有发生了改变的部分，那么我只需要把那些需要发生变化的部分进行修改就行了。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新
> 
> 第一种方法的不经济显而易见，不管各个页面节点前后是否发生了改变，统一都进行刷新
> 
> AJAX就是采用第二种页面刷新方法：通过在后台与服务器进行少量数据交换，向服务器请求数据，解析请求的数据，然后对原页面需要刷新的部分进行刷新

<p align="center"><img src=./picture/ajax-process.gif width=800 /></p>

<p align="center">AJAX工作原理</p>

```
// 获取表单变量
var f=form;
// 创建XMLHttpRequest对象
var xmlhttp;
if (window.XMLHttpRequest)
{
	// IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
	xmlhttp=new XMLHttpRequest();
}
else
{
	// IE6, IE5 浏览器执行代码
	xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}


// 得到服务器响应后，对得到的Markdown文档进行解析
xmlhttp.onreadystatechange=function()
{
	if (xmlhttp.readyState==4 && xmlhttp.status==200)
	{
		// 这里调用了marked框架中的marked函数实现Markdown2HTML的解析
		document.getElementById("content").innerHTML=marked(xmlhttp.responseText);
	}
}


// 向服务器发送请求，获取你需要的Markdown文档
xmlhttp.open("GET",f.q.value,true);
xmlhttp.send();
}
```

对上面的脚本继续简单的说明：

> 这个是AJAX的基本语法框架
> 
> 完成三步操作：
> 1. 创建http请求对象，告诉服务器我要哪个Markdown文档
> 2. 向服务器发起http请求
> 3. 收到服务器的反馈数据，得到请求的Markdown文档，进行数据的解析，实现Markdown2HTML

那么这里有几个问题需要解答：

**1\. 怎么指定我想要的Markdown文档呢？**

> 看上面的代码部分的最后一小部分，有这样一行命令：
> 
> ```
> xmlhttp.open("GET",f.q.value,true);
> ```
> 
> 这个函数的第二个参数`f.q.value`，指定了你要的Markdown文档的地址，所以如果想要指定Markdown文档就需要对这个变量进行设定
> 
> 怎么设定这个变量？
> 
> 用表单传参来实现，这里的f就是表单变量，表单部分怎么实现请点 [这里](#specify-markdown-doc)

<a name="specify-markdown-doc"><h2>用表单指定Markdown文档 [<sup>目录</sup>](#content)</h2></a>

```
<form name="form" action="" method="post">
	<select name="q">
		<option class="active" alue="">选择一个笔记:</option>
		<option value="A.md">第一个Markdown文档</option>
		<option value="B.md">第二个Markdown文档</option>
		<option value="C.md">第三个Markdown文档</option>
		<option value="Evalue-TranscriptomeData.md">第四个Markdown文档</option>
	</select>
	<input type="button" value="显示" onclick="showMarkdown()">
</form>
```
表单的显示效果如下：

<p align="center"><img src=./picture/MarkDown2HTML-form.png width=300 /></p>

点击表单中的“显示”按钮后会执行`showMarkdown( )`函数，即 [用AJAX获取Markdown文档](#get-markdown-doc) 部分的那个函数，并且将表单选择的信息通过`form`变量传递给了`showMarkdown( )`函数中的`f`变量

这样就通过表单设定了用户指定的Markdown文档

<a name="trans-markdown2html"><h2>引用marked框架解析Markdown文档 [<sup>目录</sup>](#content)</h2></a>

这里采用的是GitHub上的名为`marked`的JS框架，链接：`https://github.com/markedjs/marked`

要想使用这个框架，需要在html脚本的头信息中引用这个框架：

```
<html>
<head>
	...
	<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
	...
</head>
.
.
.
```

引用这个框架后就可以使用里面定义的`marked( )`函数进行Markdown文本解析了

<a name="full-script"><h2>附：完整的实现脚本 [<sup>目录</sup>](#content)</h2></a>

```
<!DOCTYPE html>
<html>
<head>
	.
	.
	.
	// 在这里对marked框架进行引用
	<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
	.
	.
	.
	// 在这里编写AJAX代码
	<script>
  function showMarkdown()
{
	// 获取表单变量
	var f=form;
	// 创建XMLHttpRequest对象
	var xmlhttp;
	if (window.XMLHttpRequest)
	{
		// IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
		xmlhttp=new XMLHttpRequest();
	}
	else
	{
		// IE6, IE5 浏览器执行代码
		xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
	}
	
	
	// 得到服务器响应后，对得到的Markdown文档进行解析
	xmlhttp.onreadystatechange=function()
	{
		if (xmlhttp.readyState==4 && xmlhttp.status==200)
		{
			// 这里调用了marked框架中的marked函数实现Markdown2HTML的解析
			document.getElementById("content").innerHTML=marked(xmlhttp.responseText);
		}
	}
	
	
	// 向服务器发送请求，获取你需要的Markdown文档
	xmlhttp.open("GET",f.q.value,true);
	xmlhttp.send();
	}
}
	</script>
	.
	.
	.
</head>
<body>
	.
	.
	.
	// 在这里编辑表单代码
	<div class="container" style="margin-top:30px">
		<form name="form" action="" method="post">
			<select name="q">
				<option class="active" alue="">选择一个笔记:</option>
				<option value="A.md">第一个Markdown文档</option>
				<option value="B.md">第二个Markdown文档</option>
				<option value="C.md">第三个Markdown文档</option>
				<option value="Evalue-TranscriptomeData.md">第四个Markdown文档</option>
			</select>
			<input type="button" value="显示" onclick="showMarkdown()">
		</form>
	.
	.
	.
	// 在这里编辑空的div节点，用于后面展示解析后的内容
	<div id="content"></div>
	</div>
	.
	.
	.
</body>
</html>
```

---

参考资料：

(1) [本人github笔记：AJAX学习笔记](https://github.com/Ming-Lian/Setup-Database/blob/master/AJAX%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#content)

(2) [marked说明文档](https://github.com/markedjs/marked)
