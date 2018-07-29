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
- [信息提示框](#alert)
- [按钮](#button)
	- [独立按钮](#individual-button)
	- [按钮组](#group-button)
- [进度条](#process-bar)
- [分页](#pagination)
- [卡片](#card)
- [下拉菜单](#dropdown)
- [折叠](#collapse)
- [导航](#navigation)


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

<a name="alert"><h2>信息提示框  [<sup>目录</sup>](#content)</h2></a>

提示框可以使用 .alert 类, 后面加上 .alert-success, .alert-info, .alert-warning, .alert-danger, .alert-primary, .alert-secondary, .alert-light 或 .alert-dark 类来实现:

```
<div class="alert alert-success">
  <strong>成功!</strong> 指定操作成功提示信息。
</div>
```

**关闭提示框**

我们可以在提示框中的 div 中添加 `.alert-dismissable` 类，然后在关闭按钮的链接上添加 `class="close"` 和 `data-dismiss="alert"` 类来设置提示框的关闭操作。

```
<div class="alert alert-success alert-dismissable">
  <button type="button" class="close" data-dismiss="alert">&times;</button>
  <strong>成功!</strong> 指定操作成功提示信息。
</div>
```

**提示**: `&times;` (×) 是 HTML 实体字符，来表示关闭操作，而不是字母 "x"。

<a name="button"><h2>按钮  [<sup>目录</sup>](#content)</h2></a>

<a name="individual-button"><h3>独立按钮  [<sup>目录</sup>](#content)</h3></a>

**1\. 按钮样式** `btn-*`

```
<button type="button" class="btn">基本按钮</button>
<button type="button" class="btn btn-primary">主要按钮</button>
<button type="button" class="btn btn-secondary">次要按钮</button>
<button type="button" class="btn btn-success">成功</button>
<button type="button" class="btn btn-info">信息</button>
<button type="button" class="btn btn-warning">警告</button>
<button type="button" class="btn btn-danger">危险</button>
<button type="button" class="btn btn-dark">黑色</button>
<button type="button" class="btn btn-light">浅色</button>
<button type="button" class="btn btn-link">链接</button>
```

**2\. 按钮边框** `btn-outline-*`

```
<button type="button" class="btn btn-outline-primary">主要按钮</button>
<button type="button" class="btn btn-outline-secondary">次要按钮</button>
<button type="button" class="btn btn-outline-success">成功</button>
<button type="button" class="btn btn-outline-info">信息</button>
<button type="button" class="btn btn-outline-warning">警告</button>
<button type="button" class="btn btn-outline-danger">危险</button>
<button type="button" class="btn btn-outline-dark">黑色</button>
<button type="button" class="btn btn-outline-light text-dark">浅色</button>
```


**3\. 按钮大小** `btn-[sm|lg]`

```
<button type="button" class="btn btn-primary btn-lg">大号按钮</button>
<button type="button" class="btn btn-primary">默认按钮</button>
<button type="button" class="btn btn-primary btn-sm">小号按钮</button>
```

**4\. 块级按钮** `.btn-block `

**5\. 激活和禁用的按钮**

按钮可设置为激活或者禁止点击的状态。

.active 类可以设置按钮是可用的， disabled 属性可以设置按钮是不可点击的

```
<button type="button" class="btn btn-primary active">点击后的按钮</button>
<button type="button" class="btn btn-primary" disabled>禁止点击的按钮</button>
<a href="#" class="btn btn-primary disabled">禁止点击的链接</a>
```

<a name="group-button"><h3>按钮组  [<sup>目录</sup>](#content)</h3></a>

将按钮放在同一行上，组成按钮组

**1\. 创建按钮组**

可以在 `<div>` 元素上添加 .btn-group 类来创建按钮组。

```
<div class="btn-group">
  <button type="button" class="btn btn-primary">Apple</button>
  <button type="button" class="btn btn-primary">Samsung</button>
  <button type="button" class="btn btn-primary">Sony</button>
</div>
```

**2\. 按钮组大小** `.btn-group-[lg|sm|xs]`

```
<div class="btn-group btn-group-lg">
  <button type="button" class="btn btn-primary">Apple</button>
  <button type="button" class="btn btn-primary">Samsung</button>
  <button type="button" class="btn btn-primary">Sony</button>
</div>
```

**3\. 垂直按钮组** `.btn-group-vertical`

```
<div class="btn-group-vertical">
  <button type="button" class="btn btn-primary">Apple</button>
  <button type="button" class="btn btn-primary">Samsung</button>
  <button type="button" class="btn btn-primary">Sony</button>
</div>
```

**4\. 内嵌按钮组及下拉菜单**

```
<div class="btn-group">
  <button type="button" class="btn btn-primary">Apple</button>
  <button type="button" class="btn btn-primary">Samsung</button>
  <div class="btn-group">
    <button type="button" class="btn btn-primary dropdown-toggle" data-toggle="dropdown">
       Sony
    </button>
    <div class="dropdown-menu">
      <a class="dropdown-item" href="#">Tablet</a>
      <a class="dropdown-item" href="#">Smartphone</a>
    </div>
  </div>
</div>
```

<p align="center"><img src=./picture/Beginning-Bootstrap-groupButton-inbuilt-dropdown.png width=400/></p>

<a name="process-bar"><h2>进度条  [<sup>目录</sup>](#content)</h2></a>

<p align="center"><img src=./picture/Beginning-Bootstrap-process-bar.jpg width=900 /></p>

创建一个基本的进度条的步骤如下：

> - 添加一个带有 .progress 类的 `<div>`。
> - 接着，在上面的 `<div>` 内，添加一个带有 class .progress-bar 的空的 `<div>`。
> - 添加一个带有百分比表示的宽度的 style 属性，例如 style="width:70%" 表示进度条在 70% 的位置。

```
<div class="progress">
  <div class="progress-bar" style="width:70%"></div>
</div>
```

**1\. 进度条标签**

可以在进度条内添加文本，如进度的百分比：

```
<div class="progress">
  <div class="progress-bar" style="width:70%">70%</div>
</div>
```

**2\. 进度条颜色** `.bg-[sucess|warning...]`

**3\. 条纹的进度条** `.progress-bar-striped`

```
<div class="progress">
  <div class="progress-bar progress-bar-striped" style="width:40%"></div>
</div>
```

**4\. 动画进度条** `.progress-bar-animated`

```
<div class="progress-bar progress-bar-striped progress-bar-animated" style="width: 40%"></div>
```


**5\. 混合色彩进度条**

```
<div class="progress">
  <div class="progress-bar bg-success" style="width:40%">
    Free Space
  </div>
  <div class="progress-bar bg-warning" style="width:10%">
    Warning
  </div>
  <div class="progress-bar bg-danger" style="width:20%">
    Danger
  </div>
</div>
```

<a name="pagination"><h2>分页  [<sup>目录</sup>](#content)</h2></a>

要创建一个基本的分页可以在 `<ul>` 元素上添加 .pagination 类。然后在 `<li>` 元素上添加 .page-item 类：

```
<ul class="pagination">
  <li class="page-item"><a class="page-link" href="#">Previous</a></li>
  <li class="page-item"><a class="page-link" href="#">1</a></li>
  <li class="page-item"><a class="page-link" href="#">2</a></li>
  <li class="page-item"><a class="page-link" href="#">3</a></li>
  <li class="page-item"><a class="page-link" href="#">Next</a></li>
</ul>
```

**1\. 当前页页码状态** `.active`

```
<ul class="pagination">
  ...
  <li class="page-item active"><a class="page-link" href="#">2</a></li>
  ...
</ul>
```

**2\. 不可点击的分页链接** `.disabled`

```
<ul class="pagination">
  <li class="page-item disabled"><a class="page-link" href="#">Previous</a></li>
  ...
</ul>
```

**3\. 分页显示大小**

.pagination-lg 类设置大字体的分页条目，.pagination-sm 类设置小字体的分页条目:

```
<ul class="pagination pagination-lg">
  ...
</ul>
 
<ul class="pagination pagination-sm">
  ...
</ul>
```

**4\. 面包屑导航**

.breadcrumb 和 .breadcrumb-item 类用于设置面包屑导航：

```
<ul class="breadcrumb">
  <li class="breadcrumb-item"><a href="#">Photos</a></li>
  <li class="breadcrumb-item"><a href="#">Summer 2017</a></li>
  <li class="breadcrumb-item"><a href="#">Italy</a></li>
  <li class="breadcrumb-item active">Rome</li>
</ul>
```

<a name="card"><h2>卡片  [<sup>目录</sup>](#content)</h2></a>

**1\. 创建卡片**

```
<div class="card">
  <div class="card-body">简单的卡片</div>
</div>
```

**2\. 头部和底部**

.card-header类用于创建卡片的头部样式， .card-footer 类用于创建卡片的底部样式：

```
<div class="card">
  <div class="card-header">头部</div>
  <div class="card-body">内容</div> 
  <div class="card-footer">底部</div>
</div>
```

<p align="center"><img src=./picture/Beginning-Bootstrap-Card-header-footer.png width=600 /></p>

**3\. 标题、文本和链接**

在头部元素上使用 .card-title 类来设置卡片的标题 。 .card-text 类用于设置卡片正文的内容。 .card-link 类用于给链接设置颜色。 

```
<div class="card">
  <div class="card-body">
    <h4 class="card-title">Card title</h4>
    <p class="card-text">Some example text. Some example text.</p>
    <a href="#" class="card-link">Card link</a>
    <a href="#" class="card-link">Another link</a>
  </div>
</div>
```

**4\. 图片卡片**

给 <img> 添加 .card-img-top（图片在文字上方） 或 .card-img-bottom（图片在文字下方) 来设置图片卡片：

```
<div class="card" style="width:400px">
  <img class="card-img-top" src="img_avatar1.png" alt="Card image">
  <div class="card-body">
    <h4 class="card-title">John Doe</h4>
    <p class="card-text">Some example text.</p>
    <a href="#" class="btn btn-primary">See Profile</a>
  </div>
</div>
```

如果图片要设置为背景，可以使用 .card-img-overlay 类:

```
<div class="card" style="width:500px">
  <img class="card-img-top" src="img_avatar1.png" alt="Card image">
  <div class="card-img-overlay">
    <h4 class="card-title">John Doe</h4>
    <p class="card-text">Some example text.</p>
    <a href="#" class="btn btn-primary">See Profile</a>
  </div>
</div>
```

<a name="dropdown"><h2>下拉菜单  [<sup>目录</sup>](#content)</h2></a>

**1\. 创建下拉菜单**

```
<div class="dropdown">
  <button type="button" class="btn btn-primary dropdown-toggle" data-toggle="dropdown">
    Dropdown button
  </button>
  <div class="dropdown-menu">
    <a class="dropdown-item" href="#">Link 1</a>
    <a class="dropdown-item" href="#">Link 2</a>
    <a class="dropdown-item" href="#">Link 3</a>
  </div>
</div>
```

.dropdown 类用来指定一个下拉菜单。

我们可以使用一个按钮或链接来打开下拉菜单， 按钮或链接需要添加 .dropdown-toggle 和 data-toggle="dropdown" 属性。

class to a `<div>` 元素上添加 .dropdown-menu 类来设置实际下拉菜单，然后在下拉菜单的选项中添加 .dropdown-item 类。

**2\. 下拉菜单中的分割线**

<table>
<tr>
	<td>
		<img src=./picture/Beginning-Bootstrap-dropdown-divider-1.png width=400 />
	</td>
	<td>
		<img src=./picture/Beginning-Bootstrap-dropdown-divider-2.png width=400 />
	</td>
</tr>
</table>

**3\. 下拉菜单中的标题**

<table>
<tr>
	<td>
		<img src=./picture/Beginning-Bootstrap-dropdown-header-1.png width=400 />
	</td>
	<td>
		<img src=./picture/Beginning-Bootstrap-dropdown-header-2.png width=400 />
	</td>
</tr>
</table>

**4\. 下拉菜单的定位**

如果我们想让下拉菜单右对齐，可以在元素上的 .dropdown-menu 类后添加 .dropdown-menu-right 类。 

```
<div class="dropdown-menu dropdown-menu-right">
```

<table>
<thead>
	<th>左对齐</th>
	<th>右对齐</th>
</thead>
<tbody>
<tr>
	<td>
		<img src=./picture/Beginning-Bootstrap-dropdown-align-left.png width=400 />
	</td>
	<td>
		<img src=./picture/Beginning-Bootstrap-dropdown-align-right.png width=400 />
	</td>
</tr>
<tbody>
</table>

**5\. 向上弹出下拉菜单**

```
<div class="dropup">
```

<img src=./picture/Beginning-Bootstrap-dropdown-up.png width=400 />

<a name="collapse"><h2>折叠  [<sup>目录</sup>](#content)</h2></a>

```
<button data-toggle="collapse" data-target="#demo">折叠</button>
 
<div id="demo" class="collapse">
Lorem ipsum dolor text....
</div>
```

> .collapse 类用于指定一个折叠元素 (实例中的 `<div>`); 点击按钮后会在隐藏与显示之间切换。
> 
> 控制内容的隐藏与显示，需要在 `<a>` 或 `<button>` 元素上添加 data-toggle="collapse" 属性。 data-target="#id" 属性是对应折叠的内容 (`<div id="demo">`)。

默认情况下折叠的内容是隐藏的，你可以添加 .show 类让内容默认显示:

```
<div id="demo" class="collapse show">
Lorem ipsum dolor text....
</div>
```

**扩展卡片组件来显示简单的手风琴**

<table>
<thead>
	<th>展开</th>
	<th>折叠</th>
</thead>
<tbody>
<tr>
	<td>
		<img src=./picture/Beginning-Bootstrap-collapse-card-group-1.png width=400 />
	</td>
	<td>
		<img src=./picture/Beginning-Bootstrap-collapse-card-group-2.png width=400 />
	</td>
</tr>
<tbody>
</table>

使用 data-parent 属性来确保所有的折叠元素在指定的父元素下，这样就能实现在一个折叠选项显示时其他选项就隐藏

```
<div id="accordion">
	<div class="card">
	  <div class="card-header">
	    <a class="card-link" data-toggle="collapse" href="#collapseOne">
	      选项一
	    </a>
	  </div>
	  <div id="collapseOne" class="collapse show" data-parent="#accordion">
	    <div class="card-body">
	      #1 内容：菜鸟教程 -- 学的不仅是技术，更是梦想！！！
	    </div>
	  </div>
	</div>
	.
	.
	.
</div>
```

<a name="navigation"><h2>导航  [<sup>目录</sup>](#content)</h2></a>

**1\. 创建导航栏**

在 `<ul>` 元素上添加 .nav类，在每个 `<li>` 选项上添加 .nav-item 类，在每个链接上添加 .nav-link 类:

```
<ul class="nav">
  <li class="nav-item">
    <a class="nav-link" href="#">Link</a>
  </li>
  <li class="nav-item">
    <a class="nav-link" href="#">Link</a>
  </li>
  <li class="nav-item">
    <a class="nav-link" href="#">Link</a>
  </li>
  <li class="nav-item">
    <a class="nav-link disabled" href="#">Disabled</a>
  </li>
</ul>
```

**2\. 导航对齐方式**

`.justify-content-center` 类设置导航居中显示， `.justify-content-end` 类设置导航右对齐

```
<!-- 导航居中 -->
<ul class="nav justify-content-center">
 
<!-- 导航右对齐 -->
<ul class="nav justify-content-end">
</div>
```

**3\. 垂直导航** `.flex-column`

```
<ul class="nav flex-column">
```

**4\. 选项卡**

使用 `.nav-tabs` 类可以将导航转化为选项卡。然后对于选中的选项使用 `.active` 类来标记

```
<ul class="nav nav-tabs">
  <li class="nav-item">
    <a class="nav-link active" href="#">Active</a>
  </li>
  <li class="nav-item">
    <a class="nav-link" href="#">Link</a>
  </li>
  <li class="nav-item">
    <a class="nav-link" href="#">Link</a>
  </li>
  <li class="nav-item">
    <a class="nav-link disabled" href="#">Disabled</a>
  </li>
</ul>
```

<p align="center"><img src=./picture/Beginning-Bootstrap-navigation-tab.png width=400 /></p>

**5\. 动态选项卡**

如果你要设置选项卡是动态可切换的，可以在每个链接上添加 `data-toggle="tab"` 属性。 然后在每个选项对应的内容的上添加 `.tab-pane` 类。

如果你希望有淡入效果可以在 `.tab-pane` 后添加 `.fade` 类:

```
<!-- Nav tabs -->
<ul class="nav nav-tabs">
  <li class="nav-item">
    <a class="nav-link active" data-toggle="tab" href="#home">Home</a>
  </li>
  <li class="nav-item">
    <a class="nav-link" data-toggle="tab" href="#menu1">Menu 1</a>
  </li>
  <li class="nav-item">
    <a class="nav-link" data-toggle="tab" href="#menu2">Menu 2</a>
  </li>
</ul>
 
<!-- Tab panes -->
<div class="tab-content">
  <div class="tab-pane active container" id="home">...</div>
  <div class="tab-pane container" id="menu1">...</div>
  <div class="tab-pane container" id="menu2">...</div>
</div>
```

<table>
<tbody>
<tr>
	<td>
		<img src=./picture/Beginning-Bootstrap-navigation-tab-switch-1.png width=300 />
	</td>
	<td>
		<img src=./picture/Beginning-Bootstrap-navigation-tab-switch-2.png width=300 />
	</td>
	<td>
		<img src=./picture/Beginning-Bootstrap-navigation-tab-switch-3.png width=300 />
	</td>
</tr>
<tbody>
</table>



**6\. 胶囊导航**

`.nav-pills` 类可以将导航项设置成胶囊形状

**7\. 导航等宽**

`.nav-justified` 类可以设置导航项齐行等宽显示。





参考资料：

(1) [XAMPP简介、安转、使用](https://www.cnblogs.com/qyfeng009/p/5055192.html)

(2) [菜鸟教程：Bootstrap](http://www.runoob.com/bootstrap/bootstrap-tutorial.html)
