<a name="content">目录</a>

[Bootstrap入门笔记](#title)
- [本地(Windows)开发环境的准备：XAMPP](#setup-local-webserver)
- [Bootstrap安装](#install-bootstrap)


<h1 name="title">Bootstrap入门笔记</h1>

<a name="setup-local-webserver"><h2>本地(Windows)开发环境的准备：XAMPP [<sup>目录</sup>](#content)</h2></a>

XAMPP是一款开源、免费的网络服务器软件，经过简单安装后，就可以在个人电脑上搭建服务器环境

XAMPP (X-系统（**适用于多系统**），A-Apache，M-Mysql，P-php，P-Phpmyadmin/Perl)，这个缩写名称说明了XAMPP安装包所包含 的文件：

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



参考资料：

(1) [XAMPP简介、安转、使用](https://www.cnblogs.com/qyfeng009/p/5055192.html)

(2) [菜鸟教程：Bootstrap](http://www.runoob.com/bootstrap/bootstrap-tutorial.html)
