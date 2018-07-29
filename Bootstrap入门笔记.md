<a name="content">目录</a>

[Bootstrap 4 入门笔记](#title)
- [本地(Windows)开发环境的准备：XAMPP](#setup-local-webserver)
- [Bootstrap安装](#install-bootstrap)
- [移动设备兼容](#compatible-with-mobile-device)
- [网格系统](#grid-system)
- [文字排版](#type-setting)
- [颜色](#color)
- [表格](#table)
- [图片](#picture)



<h1 name="title">Bootstrap 4 入门笔记</h1>

<a name="setup-local-webserver"><h2>本地(Windows)开发环境的准备：XAMPP [<sup>目录</sup>](#content)</h2></a>

XAMPP是一款开源、免费的网络服务器软件，经过简单安装后，就可以在个人电脑上搭建服务器环境

XAMPP (X-系统（**适用于多系统**），A-Apache，M-MariaDB，P-php，P-Phpmyadmin/Perl)，这个缩写名称说明了XAMPP安装包所包含 的文件：

> - Apache web 服务器
> - MySQL数据库
> - PHP
> - Perl
> - FTP 服务程序(FileZillaFTP)
> - phpMyAdmin

简单的说，XAMPP是一款集成了Apache+MYSQL+PHP的服务器系统开发套 件，同时还包含了管理MySQL的工具phpMyAdmin，即可对MySQL进行可视化操作。

- 安装XAMPP

	XAMPP官网：https://www.apachefriends.org/index.html ，选择Windows版本的下载，目前Windows版本只支持32bit的
	
	安装过程中可能会弹出报错信息，先不管，完成安装后，在XAMPP的安装目录下，运行`setup_xampp.bat`，这样能使配置文件中的路径信息得到更新，可能会解除安装存在的问题，至于错误原因目前还未知

- 启动WebServer-Apache

	在XAMPP的安装目录下找到`xampp-control.exe`双击启动xampp的控制台

	<p align="center"><img src=./picture/Beginning-Bootstrap-SetupXAMPP-1.png width=600 /></p>

	点击Apache模块的Start按钮，启动WebServer
	
	测试WebServer是否已经成功启动：在浏览器中地址栏输入`localhost`回车，打开XAMPP主界面

	<p align="center"><img src=./picture/Beginning-Bootstrap-SetupXAMPP-2.png width=600 /></p>

- 关于XAMPP中的一些默认配置以及相关配置文件：

	1\. XAMPP Mysql 的默认账号密码是(这也是phpMyAdmin登录所用的帐号和密码)：

	```
	User: root   Password:（空）  
	```

	2\. XAMPP FileZilla FTP的默认账号密码是：

	```
	User: newuser   Password: wampp
	User: anonymous   Password: some@mail.net
	```

	3\. XAMPP Mercury的默认账号密码是：

	```
	Postmaster: postmaster (postmaster@localhost)
	Administrator: Admin (admin@localhost)
	TestUser: newuser   Password: wampp
	```

	4\.  XAMPP WEBDAV的默认账号密码是：

	```
	User: wampp   Password: xampp
	```

	5\. XAMPP的中相关服务的配置文件路径是：

	```
	Apache 配置：\xampp\apache\conf\httpd.conf；
	PHP：\xampp\php\php.ini；
	MySQL：\xampp\mysql\bin\my.ini；
	phpMyAdmin：\xampp\phpMyAdmin\config.inc.php；
	FileZilla FTP 服务器：\xampp\FileZillaFTP\FileZilla Server.xml；
	```

	6\. XAMPP的网站根目录的默认路径是：`\xampp\htdocs`；

	```
	这里\xampp指的是XAMPP的安装目录
	```
	
	7\. XAMPP MYSQL数据库保存数据的默认路径是什么？`\xampp\mysql\data`；

<a name="install-bootstrap"><h2>Bootstrap安装 [<sup>目录</sup>](#content)</h2></a>

打开 [Bootstrap官网](http://getbootstrap.com/)

<p align="center"><img src=./picture/Beginning-Bootstrap-install-Bootstrap.png width=900 /></p>

点击**Download**

Bootstrap官网提供了**预编译的版本**和**未编译的源代码**

> - **Compiled CSS and JS**
> 
> 可以下载 Bootstrap CSS、JavaScript 和字体的预编译的压缩版本。不包含文档和最初的源代码文件。
> 
> <img src=./picture/Beginning-Bootstrap-install-Bootstrap-compiled-file-structure.jpg width=600 />
> 
> 如上图所示，可以看到已编译的 CSS 和 JS（bootstrap.\*），以及已编译压缩的 CSS 和 JS（bootstrap.min.\*）。同时也包含了 Glyphicons 的字体，这是一个可选的 Bootstrap 主题。
> 
> - **Source files**
> 
> 最新的 Bootstrap LESS 和 JavaScript 源代码。如果您使用的是未编译的源代码，您需要编译 LESS 文件来生成可重用的 CSS 文件。对于编译 LESS 文件，Bootstrap 官方只支持  [Recess](https://twitter.github.io/recess/)
> 
> <img src=./picture/Beginning-Bootstrap-install-Bootstrap-sourcecode-structure.jpg width=600 />
> 
> > less/、js/ 和 fonts/ 下的文件分别是 Bootstrap CSS、JS 和图标字体的源代码。
> 
> > dist/ 文件夹包含了上面预编译下载部分中所列的文件和文件夹。
> 
> > docs-assets/、examples/ 和所有的 \*.html 文件是 Bootstrap 文档。

<a name="compatible-with-mobile-device"><h2>移动设备兼容 [<sup>目录</sup>](#content)</h2></a>

为了让 Bootstrap 开发的网站对移动设备友好，确保适当的绘制和触屏缩放，需要在网页的 head 之中添加 viewport meta 标签，如下所示：

```
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

> **width 属性**：控制设备的宽度。假设您的网站将被带有不同屏幕分辨率的设备浏览，那么将它设置为 device-width 可以确保它能正确呈现在不同设备上。
> 
> **initial-scale**：网页呈现比例，initial-scale=1.0 确保网页加载时，以 1:1 的比例呈现，不会有任何的缩放。

在移动设备浏览器上，通过为 viewport meta 标签添加 user-scalable=no 可以禁用其缩放（zooming）功能。 

通常情况下，maximum-scale=1.0 与 user-scalable=no 一起使用。这样禁用缩放功能后，用户只能滚动屏幕，就能让您的网站看上去更像原生应用的感觉。

```
<meta name="viewport" content="width=device-width, 
                                     initial-scale=1.0, 
                                     maximum-scale=1.0, 
                                     user-scalable=no">
```

<a name="grid-system"><h2>网格系统 [<sup>目录</sup>](#content)</h2></a>

Bootstrap 4 的网格系统是响应式的，列会根据屏幕大小自动重新排列。

Bootstrap 提供了一套响应式、移动设备优先的流式网格系统，随着屏幕或视口（viewport）尺寸的增加，系统会自动分为最多12列。

|` `|超小设备手机（<768px）|小型设备平板电脑（≥768px）|中型设备台式电脑（≥992px）|大型设备台式电脑（≥1200px）|
|:---:|:---:|:---:|:---:|:---:|
|网格行为|一直是水平的|以折叠开始，断点以上是水平的|以折叠开始，断点以上是水平的|以折叠开始，断点以上是水平的|
|Class 前缀|.col-xs-|.col-sm-|.col-md-|.col-lg-|
|列数量和|12|12|12|12|
|可嵌套	|Yes|Yes|Yes|Yes|
|偏移量	|Yes|Yes|Yes|Yes|
|列排序	|Yes|Yes|Yes|Yes|

基本的网格结构

```
<!-- 第一个例子：控制列的宽度及在不同的设备上如何显示 -->
<div class="container">
   <div class="row">
      <div class="col-*-*"></div>
      <div class="col-*-*"></div>      
   </div>
   <div class="row">...</div>
</div>
<div class="container">....

<!-- 第二个例子：或让 Bootstrap 者自动处理布局 -->
<div class="row">
  <div class="col"></div>
  <div class="col"></div>
  <div class="col"></div>
</div>
```

第二个例子: 不在每个 col 上添加数字，让 bootstrap 自动处理布局，同一行的每个列宽度相等： 两个 "col" ，每个就为 50% 的宽度。三个 "col"每个就为 33.33% 的宽度，四个 "col"每个就为 25% 的宽度，以此类推。

Bootstrap 网格系统（Grid System）的工作原理

> 行必须放置在 .container class 内，以便获得适当的对齐（alignment）和内边距（padding）
> 
> 使用行来创建列的水平组（列数和不能超过12）
> 
> 内容应该放置在列内，且唯有列可以是行的直接子元素

设定在不同设备（本质上是浏览器窗口大小，测试时可以在电脑上缩放浏览器窗口来查看效果）的网格系统的样式，使得在设备改变时自动切换网格布局

```
<div class="col-sm-3 col-md-6 col-lg-4">....</div>
```

**偏移列**

为了在大屏幕显示器上使用偏移，请使用 .col-md-offset-* 类。这些类会把一个列的左外边距（margin）增加 * 列，其中 * 范围是从 1 到 11

```
<div class="container">
    <h1>Hello, world!</h1>
    <div class="row" >
        <div class="col-md-6 col-md-offset-3" 
        style="background-color: #dedef8;box-shadow: 
        inset 1px -1px 1px #444, inset -1px 1px 1px #444;">
            <p>Lorem ipsum dolor sit amet, consectetur adipisicing 
            elit.
            </p>
        </div>
    </div>
</div>
```

<p align="center"><img src=./picture/Beginning-Bootstrap-GridSystem-col-offset.jpg width=600 /></p>


**嵌套列**

为了在内容中嵌套默认的网格，请添加一个新的 .row，并在一个已有的 .col-md-\* 列内添加一组 .col-md-\* 列。被嵌套的行应包含一组列，这组列个数不能超过12（其实，没有要求你必须占满12列）。

<p align="center"><img src=./picture/Beginning-Bootstrap-GridSystem_nesting-columns-demo.jpg width=600 /></p>

<a name="type-setting"><h2>文字排版 [<sup>目录</sup>](#content)</h2></a>

- **标题**

	Bootstrap 中定义了所有的 HTML 标题（h1 到 h6）的样式

	Bootstrap 还提供了四个 Display 类来控制标题的样式: .display-1, .display-2, .display-3, .display-4。Display 标题可以输出更大更粗的字体样式
	
	**内联子标题**：如果需要向任何标题添加一个内联子标题，只需要简单地在元素两旁添加 `<small>`，或者添加 .small class，这样子您就能得到一个字号更小的颜色更浅的文本
	
	**引导主体副本**：为了给段落添加强调文本，则可以添加 class="lead"，这将得到更大更粗、行高更高的文本

- **强调**
 
	HTML 的默认强调标签 `<small>`（设置文本为父文本大小的 85%）、`<strong>`（设置文本为更粗的文本）、`<em>`（设置文本为斜体）。
	
	Bootstrap 提供了一些用于强调文本的类，如下面实例所示：
	
	```
	<p class="text-left">向左对齐文本</p>
	<p class="text-center">居中对齐文本</p>
	<p class="text-right">向右对齐文本</p>
	<p class="text-muted">本行内容是减弱的</p>
	<p class="text-primary">本行内容带有一个 primary class</p>
	<p class="text-success">本行内容带有一个 success class</p>
	<p class="text-info">本行内容带有一个 info class</p>
	<p class="text-warning">本行内容带有一个 warning class</p>
	<p class="text-danger">本行内容带有一个 danger class</p>
	```

- **缩写**

	Bootstrap 定义 `<abbr>` 元素的样式为显示在文本底部的一条虚线边框，当鼠标悬停在上面时会显示完整的文本（只要您为 `<abbr>` title 属性添加了文本）。为了得到一个更小字体的文本，请添加 .initialism 到 `<abbr>`。
	
	```
	<abbr title="World Wide Web">WWW</abbr><br>
	```

- **引用（Blockquote）**

	在 HTML 文本旁使用默认的 `<blockquote>`
	
	添加一个 `<small>` 标签来标识引用的来源，使用 class .pull-right 向右对齐引用。

- **代码**

	Bootstrap 允许您以两种方式显示代码：
	
	> - 第一种是 `<code>` 标签。如果您想要**内联**显示代码，那么您应该使用 `<code>` 标签。
	> - 第二种是 `<pre>` 标签。如果代码需要被显示为一个独立的**块元素**或者**代码有多行**，那么您应该使用 `<pre>` 标签。
	
	请确保当您使用 `<pre>` 和 `<code>` 标签时，开始和结束标签使用了 unicode 变体： `&lt;` 和 `&gt;`。

<a name="color"><h2>颜色 [<sup>目录</sup>](#content)</h2></a>

**文本颜色**

<table>
<tr>
<td>
<pre>
&lt;div class="container"&gt;
  &lt;h2&gt;代表指定意义的文本颜色&lt;/h2&gt;
  &lt;p class="text-muted"&gt;柔和的文本。&lt;/p&gt;
  &lt;p class="text-primary"&gt;重要的文本。&lt;/p&gt;
  &lt;p class="text-success"&gt;执行成功的文本。&lt;/p&gt;
  &lt;p class="text-info"&gt;代表一些提示信息的文本。&lt;/p&gt;
  &lt;p class="text-warning"&gt;警告文本。&lt;/p&gt;
  &lt;p class="text-danger"&gt;危险操作文本。&lt;/p&gt;
  &lt;p class="text-secondary"&gt;副标题。&lt;/p&gt;
  &lt;p class="text-dark"&gt;深灰色文字。&lt;/p&gt;
  &lt;p class="text-light"&gt;浅灰色文本（白色背景上看不清楚）。&lt;/p&gt;
  &lt;p class="text-white"&gt;白色文本（白色背景上看不清楚）。&lt;/p&gt;
&lt;/div&gt;
</pre>
</td>
<td>
<img src=./picture/Beginning-Bootstrap-txt-color.png width=400/>
</td>
</tr>
</table>





<a name="table"><h2>表格 [<sup>目录</sup>](#content)</h2></a>

**可选的表格类**

|	类	|	描述	|
|:---|:---|
|	.table	|	为任意 `<table>` 添加基本样式 (只有横向分隔线)	|
|	.table-striped	|	在 `<tbody>` 内添加斑马线形式的条纹 ( IE8 不支持)	|
|	.table-bordered	|	为所有表格的单元格添加边框	|
|	.table-hover	|	在 `<tbody>` 内的任一行启用鼠标悬停状态	|
|	.table-condensed	|	让表格更加紧凑	|

**响应式表格**

通过把任意的 .table 包在 .table-responsive class 内，您可以让表格水平滚动以适应小型设备（小于 768px）。当在大于 768px 宽的大型设备上查看时，您将看不到任何的差别。

<a name="picture"><h2>图片 [<sup>目录</sup>](#content)</h2></a>

|	类	|	描述	|
|:---|:---|
|	.img-rounded	|	为图片添加圆角 (IE8 不支持)	|
|	.img-circle	|	将图片变为圆形 (IE8 不支持)	|
|	.img-thumbnail	|	缩略图功能	|
|	.img-responsive	|	图片响应式 (将很好地扩展到父元素)	|

**响应式图片**

 通过在 `<img>` 标签添加 .img-responsive 类来让图片支持响应式设计。 图片将很好地扩展到父元素。

.img-responsive 类将 `max-width: 100%;` 和 `height: auto;` 样式应用在图片上：

```
 <img src="cinqueterre.jpg" class="img-responsive" alt="Cinque Terre"> 
```







参考资料：

(1) [XAMPP简介、安转、使用](https://www.cnblogs.com/qyfeng009/p/5055192.html)

(2) [菜鸟教程：Bootstrap](http://www.runoob.com/bootstrap/bootstrap-tutorial.html)
