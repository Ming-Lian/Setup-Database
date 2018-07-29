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
<!--导航-->
<nav class="navbar navbar-default">
   <div class="container-fluid">
       <ul class="nav navbar-nav">
           <li class="active"><a href="">首页</a></li>
           <li><a href="">关于</a></li>
           <li><a href="">登录</a></li>
       </ul>
   </div>
</nav>
```












参考资料：

(1) [最好懂的 Bootstrap 实战案例教程](https://blog.csdn.net/qq_14991385/article/details/60755620)
