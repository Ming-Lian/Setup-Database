<a name="content">目录</a>

[Bootstrap 4 实战](#title)
- [引入 Bootstrap 框架相关文件](#load-bootstrap-framework)
- [创建首页](#home-page)









<h1 name="title">Bootstrap 4 实战</h1>

<p align="center"><img src=./picture/InAction-Bootstrap-demo.jpg width=600 /></p>

<a name="load-bootstrap-framework"><h2>引入 Bootstrap 框架相关文件 [<sup>目录</sup>](#content)</h2></a>

远程 CDN 引入

```
<link rel="stylesheet" href="//cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css">
<link href="../assets/style.css" rel="stylesheet">
<script src="//cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>
<script src="//cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
```

注意这几个文件的前后顺序，如果不对，会导致页面无法正常运行。

- 先引入 bootstrap.min.css （Bootstrap的样式表文件）
- 然后引入自己写的 css 文件（style.css）
- 然后引入 jQuery（javascript 库）
- 最后引入 bootstrap.min.js 程序文件

<a name="home-page"><h2>创建首页 [<sup>目录</sup>](#content)</h2></a>

首页的结构布局：

<p align="center"><img src=./picture/InAction-Bootstrap-home-frame.png width=600 /></p>

- **导航条组件**

```
<!-- 小屏幕上水平导航栏会切换为垂直的 -->
<nav class="navbar navbar-expand-sm bg-dark navbar-dark">

	<!-- logo -->
	<a class="navbar-brand" href="#">Logo</a>
  
	<!-- 折叠导航栏 -->
	<button type="button" class="navbar-toggler" data-toggle="collapse" data-target="#collapsibleNavbar">
		<span class="navbar-toggler-icon"></span>
	</button>

	<!-- Navbar Links -->
	<div class="collapse navbar-collapse" id="collapsibleNavbar">
	    <ul class="navbar-nav">
			<li class="nav-item">
				<a class="nav-link" href="#">首页</a>
			</li>
			<li class="nav-item">
				<a class="nav-link" href="#">关于</a>
			</li>
			<li class="nav-item">
				<a class="nav-link" href="#">登录</a>
			</li>
		</ul>
	</div>
</nav>
```

<table>
<tr>
	<td>
		<img src=./picture/InAction-Bootstrap-home-navbar-1.png width=400 />
	</td>
	<td>
		<img src=./picture/InAction-Bootstrap-home-navbar-2.png width=400 />
	</td>
</tr>
</table>

- **轮播图效果**

```
<div id="demo" class="carousel slide" data-ride="carousel">
 
  <!-- 指示符 -->
  <ul class="carousel-indicators">
    <li data-target="#demo" data-slide-to="0" class="active"></li>
    <li data-target="#demo" data-slide-to="1"></li>
    <li data-target="#demo" data-slide-to="2"></li>
  </ul>
 
  <!-- 轮播图片 -->
  <div class="carousel-inner">
    <div class="carousel-item active">
      <img class="img-fluid" src="./picture/slide-1.jpg">
    </div>
    <div class="carousel-item">
      <img class="img-fluid" src="./picture/slide-2.jpg">
    </div>
    <div class="carousel-item">
      <img class="img-fluid" src="./picture/slide-3.jpg">
    </div>
  </div>
 
  <!-- 左右切换按钮 -->
  <a class="carousel-control-prev" href="#demo" data-slide="prev">
    <span class="carousel-control-prev-icon"></span>
  </a>
  <a class="carousel-control-next" href="#demo" data-slide="next">
    <span class="carousel-control-next-icon"></span>
  </a>
 
</div>
```

为了使图片对窗口进行响应，需要在 `<head>` 中增加以下css style：

```
  <style>
  /* Make the image fully responsive */
  .carousel-inner img {
      width: 100%;
      height: 100%;
  }
  </style>
```

<p align="center"><img src=./picture/InAction-Bootstrap-home-navbar-and-slides.png width=900 />




参考资料：

(1) [最好懂的 Bootstrap 实战案例教程](https://blog.csdn.net/qq_14991385/article/details/60755620)

(2) [GitHub笔记：Bootstrap入门笔记](https://github.com/Ming-Lian/Setup-Database/blob/master/Bootstrap%E5%85%A5%E9%97%A8%E7%AC%94%E8%AE%B0.md#picture)
