<a name="content">目录</a>

[PHP+MySQL实战：小鼠与人类lincRNA注释信息数据库](#title)
- [思路](#idea)
- [准备需要写入数据库中的数据](#prepare-data)
	- [下载注释信息文件](#download-gtf)
	- [提取lincRNA部分记录](#extract-lincRNA)
- [将数据写入数据库中](#write-data-into-database)
	- [方法一：使用MySQLi](#use-mysqli)
	- [方法二：使用sql脚本](#use-sqlscript)
		- [准备sql脚本](#prepare-sql)
		- [写入数据](#write-data)
- [编辑php脚本](#php-code)
	- [注册](#register)
		- [创建注册页面](#register-page)
		- [注册信息处理脚本](#registeraction-php)
	- [登录](#login)
		- [登录页面](#login-page)
		- [登录脚本](#login-php)
			- [方法一](#login-php-1)
			- [方法二](#login-php-2)
		- [登录成功](#login-success)
		- [退出登录](#logout)
	- [检索数据库](#query)
		- [表单部分](#query-form)
		- [PHP脚本](#query-php)






<h1 name="title">PHP+MySQL实战：小鼠与人类lincRNA注释信息数据库</h1>

<a name="idea"><h2>思路 [<sup>目录</sup>](#content)</h2></a>

1. 从 **UCSC genome browser** 或 **Ensemble** 等数据库中下载人类（homo sapiens）和小鼠 （Mus musculus）的全基因组注释信息文件(GTF或者GFF3格式文件）

2. 提取出其中的lincRNA的注释信息记录，然后将其写入MySQL数据库系统

3. 写好PHP脚本与之前建好的MySQL数据库系统进行数据交互

<a name="prepare-data"><h2>准备需要写入数据库中的数据 [<sup>目录</sup>](#content)</h2></a>

<a name="download-gtf"><h3>下载注释信息文件 [<sup>目录</sup>](#content)</h3></a>

获取GTF文件的下载地址：

打开Ensemble数据库：http://asia.ensembl.org/index.html

<p align="center"><img src=./picture/InAction-PHP-MySQL-download-1.png width=800 /></p>

<p align="center"><img src=./picture/InAction-PHP-MySQL-arrow.jpg width=50 /></p>

<p align="center"><img src=./picture/InAction-PHP-MySQL-download-2.png width=400 /></p>

<p align="center"><img src=./picture/InAction-PHP-MySQL-arrow.jpg width=50 /></p>

<p align="center"><img src=./picture/InAction-PHP-MySQL-download-3.png width=800 /></p>

<p align="center"><img src=./picture/InAction-PHP-MySQL-arrow.jpg width=50 /></p>

以下列出了可供下载的基因组注释信息版本`release-XX`，目前的最新版本为`release-91`

<p align="center"><img src=./picture/InAction-PHP-MySQL-download-4.png width=800 /></p>


得到下载链接，开始下载（下载至Linux服务器中）

```
# 下载GRCh38.91（人类）注释文件
$ wget -c -P /Path/To/dir ftp://ftp.ensembl.org/pub/release-91/gtf/homo_sapiens/Homo_sapiens.GRCh38.91.gtf.gz

# 下载GRCm38.91（小鼠）注释文件
$ wget -c -P /Path/To/dir ftp://ftp.ensembl.org/pub/release-91/gtf/mus_musculus/Mus_musculus.GRCm38.91.gtf.gz
```

<a name="extract-lincRNA"><h3>提取lincRNA部分记录 [<sup>目录</sup>](#content)</h3></a>

首先解压前面下载的压缩文件

```
$ gunzip /Path/To/dir/Homo_sapiens.GRCh38.91.gtf.gz
$ gunzip /Path/To/dir/Mus_musculus.GRCm38.91.gtf.gz
```

GTF格式说明

<p align="center"><img src=./picture/InAction-PHP-MySQL-gtf-format.png width=800 /></p>

看一下我们的GTF文件的具体情况

```
Chrom	Resource	Feature	 Start	 End	Score	Strand	Frame 	Attributes
1       havana  	gene    3073253 3074322 .       +       .       gene_id "ENSMUSG00000102693"; gene_version "1"; gene_name "4933401J01Rik"; gene_source "havana"; gene_biotype "TEC"; havana_gene "OTTMUSG00000049935"; havana_gene_version "1";
1       havana  	transcript      3073253 3074322 .       +       .       gene_id "ENSMUSG00000102693"; gene_version "1"; transcript_id "ENSMUST00000193812"; transcript_version "1"; gene_name "4933401J01Rik"; gene_source "havana"; gene_biotype "TEC"; havana_gene "OTTMUSG00000049935"; havana_gene_version "1"; transcript_name "4933401J01Rik-201"; transcript_source "havana"; transcript_biotype "TEC"; havana_transcript "OTTMUST00000127109"; havana_transcript_version "1"; tag "basic"; transcript_support_level "NA";
1       havana  	exon    3073253 3074322 .       +       .       gene_id "ENSMUSG00000102693"; gene_version "1"; transcript_id "ENSMUST00000193812"; transcript_version "1"; exon_number "1"; gene_name "4933401J01Rik"; gene_source "havana"; gene_biotype "TEC"; havana_gene "OTTMUSG00000049935"; havana_gene_version "1"; transcript_name "4933401J01Rik-201"; transcript_source "havana"; transcript_biotype "TEC"; havana_transcript "OTTMUST00000127109"; havana_transcript_version "1"; exon_id "ENSMUSE00001343744"; exon_version "1"; tag "basic"; transcript_support_level "NA";
```

可以看到，最后一列的**gene_biotype**中保存的是基因类型，例如protein_coding，lincRNA，miRNA等，所以针对这一列来提取

用Perl单行命令提取lincRNA注释记录

```
$ perl -ne 'chomp;next if (/^\#/);@gtf=split /\t/;if($gtf[8] =~ /gene_biotype\s\"lincRNA\"/) {print "$_\n";}' /Path/To/dir/Mus_musculus.GRCm38.91.gtf >/Path/To/dir/lincRNA_GRCm38.91.gtf
```

想获取该数据库实战中的示例数据，请点 [**这里**](https://github.com/Ming-Lian/Setup-Database/tree/master/txt-supply)

提取出来的lincRNA注释记录为以下形式：

```
Chrom	Resource	Feature	 Start	 End	Score	Strand	Frame 	Attributes
1       havana  	gene    29554   31109   .       +       .       gene_id "ENSG00000243485"; gene_version "5"; gene_name "MIR1302-2HG"; gene_source "havana"; gene_biotype "lincRNA";
1       havana  	transcript      29554   31097   .       +       .       gene_id "ENSG00000243485"; gene_version "5"; transcript_id "ENST00000473358"; transcript_version "1"; gene_name "MIR1302-2HG"; gene_source "havana"; gene_biotype "lincRNA"; transcript_name "MIR1302-2HG-202"; transcript_source "havana"; transcript_biotype "lincRNA"; tag "basic"; transcript_support_level "5";
1       havana  	exon    29554   30039   .       +       .       gene_id "ENSG00000243485"; gene_version "5"; transcript_id "ENST00000473358"; transcript_version "1"; exon_number "1"; gene_name "MIR1302-2HG"; gene_source "havana"; gene_biotype "lincRNA"; transcript_name "MIR1302-2HG-202"; transcript_source "havana"; transcript_biotype "lincRNA"; exon_id "ENSE00001947070"; exon_version "1"; tag "basic"; transcript_support_level "5";
```

接下来，将以上格式的信息格式化成以下形式：

```
Chrom	Biotype	Feature	 	Start	 End	 GeneId		GeneName	TranscriptId		ExonNumber
 1	lincRNA	gene		29554	31109	ENSG00000243485	MIR1302-2HG	-			-
 1	lincRNA	transcript	29554	31097	ENSG00000243485	MIR1302-2HG	ENST00000473358		-
 1	lincRNA	exon		29554	30039	ENSG00000243485	MIR1302-2HG	ENST00000473358		1
```

利用Perl的正则匹配提取目标字符串进行文本格式化，想了解正则表达式，请点 [这里](http://www.runoob.com/perl/perl-regular-expressions.html)

>对于第三列Feature的不同，可以分别构造不同的正则表达式：
> - gene：
>
> 原格式：
>
> ```
> gene_id "ENSG00000243485"; gene_version "5"; gene_name "MIR1302-2HG"; gene_source "havana"; gene_biotype "lincRNA";
> ```
>
> 正则表达式：
>
> ```
> /gene_id\s\"(\w+)\";.{18,19}\sgene_name\s\"([\w-\.]+)\"/
> ```
> - transcript：
>
> 原格式：
>
> ```
> gene_id "ENSG00000243485"; gene_version "5"; transcript_id "ENST00000473358"; transcript_version "1"; gene_name "MIR1302-2HG"; gene_source "havana"; gene_biotype "lincRNA"; transcript_name "MIR1302-2HG-202"; transcript_source "havana"; transcript_biotype "lincRNA"; tag "basic"; transcript_support_level "5";
> ```
>
> 正则表达式：
> 
> ```
> /gene_id\s\"(\w+)\";.{18,19}\stranscript_id\s\"(\w+)\";.{24,25}\sgene_name\s\"([\w-\.]+)\"/
> ```
>
> - exon：
>
> 原格式：
> 
> ```
> gene_id "ENSG00000243485"; gene_version "5"; transcript_id "ENST00000473358"; transcript_version "1"; exon_number "1"; gene_name "MIR1302-2HG"; gene_source "havana"; gene_biotype "lincRNA"; transcript_name "MIR1302-2HG-202"; transcript_source "havana"; transcript_biotype "lincRNA"; exon_id "ENSE00001947070"; exon_version "1"; tag "basic"; transcript_support_level "5";
> ```
> 
> 正则表达式：
> 
> ```
> /gene_id\s\"(\w+)\";.{18,19}\stranscript_id\s\"(\w+)\";.{24,25}\sexon_number\s\"(\d+)\";\sgene_name\s\"([\w-\.]+)\"/
> ```


```
# 正则匹配的正则表达式可能有点复杂，这里为了方便阅读分为多行且加上缩进，在实际命令行中请写成一行

$ perl -ne 'chomp;next if (/^\#/);@linc=split /\t/;
if($linc[2] =~ /gene/){
	if ($linc[8] =~ /gene_id\s\"(\w+)\";.{18,19}\sgene_name\s\"([\w-\.]+)\"/){
		print "$linc[0]\tlincRNA\t$linc[2]\t$linc[3]\t$linc[4]\t$1\t$2\t-\t-\n";
	}
}elsif($linc[2] =~ /transcript/){
	if($linc[8] =~ /gene_id\s\"(\w+)\";.{18,19}\stranscript_id\s\"(\w+)\";.{24,25}\sgene_name\s\"([\w-\.]+)\"/)
	{
		print "$linc[0]\tlincRNA\t$linc[2]\t$linc[3]\t$linc[4]\t$1\t$3\t$2\t-\n";
	}
}else{
	if($linc[8] =~ /gene_id\s\"(\w+)\";.{18,19}\stranscript_id\s\"(\w+)\";.{24,25}\sexon_number\s\"(\d+)\";\sgene_name\s\"([\w-\.]+)\"/)
	{
		print "$linc[0]\tlincRNA\t$linc[2]\t$linc[3]\t$linc[4]\t$1\t$4\t$2\t$3\n";
	}
}'

/Path/To/dir/lincRNA_GRCm38.91.gtf >/Path/To/dir/lincRNA_GRCm38.91.gtf.format
```

<a name="write-data-into-database"><h2>将数据写入数据库中 [<sup>目录</sup>](#content)</h2></a>

以下以将`lincRNA_GRCh38.91.gtf.format`的数据导入`lincRNA_h`表中为例

<a name="use-mysqli"><h3>方法一：使用MySQLi [<sup>目录</sup>](#content)</h3></a>

MySQLi：PHP中用于与MySQL数据库系统进行数据库交互的扩展

使用MySQLi的预处理与参数绑定方法，进行数据的批量导入

预备知识：

> - 想了解 MySQLi 连接数据库的方法，请点 [这里](https://github.com/Ming-Lian/Memo/blob/master/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%EF%BC%9APHP%E4%B8%80%E5%91%A8%E9%80%9F%E6%88%90.md#connect-mysql-mysqli)
> - 想了解 MySQLi 的预处理方法，请点 [这里](https://github.com/Ming-Lian/Memo/blob/master/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%EF%BC%9APHP%E4%B8%80%E5%91%A8%E9%80%9F%E6%88%90.md#use-prepare-sentence)
> - 想了解 PHP 的文件处理方法，请点 [这里](https://github.com/Ming-Lian/Memo/blob/master/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%EF%BC%9APHP%E4%B8%80%E5%91%A8%E9%80%9F%E6%88%90.md#file-process)

1\. 首先，创建好表格

```
# 登录MySQL
$ mysql -u username -p

# 选定要操作的数据库，即打算将表格创建在哪个数据库底下，例如我们要操作的数据库为lincRNAdb
mysql> use lincRNAdb;

# 创建表格，表格名为lincRNA_h
mysql> CREATE TABLE lincRNA_h (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `chrom` varchar(255) NOT NULL DEFAULT '' COMMENT '染色体',
  `biotype` varchar(255) NOT NULL DEFAULT '' COMMENT '基因类型',
  `feature` varchar(255) NOT NULL DEFAULT '' COMMENT '结构单元',
  `start` int(11) NOT NULL DEFAULT '0' COMMENT '起始位置',
  `end` int(11) NOT NULL DEFAULT '0' COMMENT '终止位置',
  `geneid` varchar(255) NOT NULL DEFAULT '' COMMENT '基因ENsembleId',
  `genename` varchar(255) NOT NULL DEFAULT '' COMMENT '基因名',
  `transcriptid` varchar(255) NOT NULL DEFAULT '' COMMENT '转录本ENsembleId',
  `exon` int(11) NOT NULL DEFAULT '0' COMMENT '转录本exon序号',
  PRIMARY KEY (`id`) 
) ;
```

- 编辑php脚本`data_batch_import.php`

```
<?php
$host="localhost";

// 请设置好正确的用户名和密码，注意必须为具有数据库写权限的用户
$username="root";
$password="password";

$dbname="testdb";

// 连接数据库
$conn=new mysqli($host,$username,$password,$dbname);

// 检查连接
if($conn->connect_error){
	die("连接失败：".$conn->connect_error);
}else{
	echo "连接成功！\n";
	echo "正在批量导入数据，请耐心等待...\n";
}

// 预处理及绑定
$stmt=$conn->prepare("INSERT INTO lincRNA_m (chrom,biotype,feature,start,end,geneid,genename,transcriptid,exon) VALUES (?,?,?,?,?,?,?,?,?)") ;
$stmt->bind_param("sssiisssi",$chrom,$biotype,$feature,$start,$end,$geneid,$genename,$transcriptid,$exon);

// 打开文件
$file=fopen("lincRNA_GRCm38.91.gtf.format",'r') or exit("Unable to open file!");

// 逐行读取文件，并写入数据库
while(!feof($file)){
	$data=explode("\t",fgets($file)); // 以tab为间隔标识将字符串打散
	
	// 设置参数
	$chrom=$data[0];
	$biotype=$data[1];
	$feature=$data[2];
	$start=$data[3];
	$end=$data[4];
	$geneid=$data[5];
	$genename=$data[6];
	$transcriptid=$data[7];
	$exon=$data[8];
	// 执行
	$stmt->execute();
}

echo "新记录插入成功";

fcolse($file);
$stmt->close();
$conn->close();
?>
```

- 执行php脚本，完成数据的批量导入

```
$ php -f data_batch_import.php
```

想了解如何在 Linux 命令行中使用和执行 PHP 代码，请点 [这里](https://github.com/Ming-Lian/Memo/blob/master/Beginning-SQL.md#run-php-code-in-linux)

<a name="use-sqlscript"><h3>方法二：使用sql脚本 [<sup>目录</sup>](#content)</h3></a>

<a name="prepare-sql"><h4>准备sql脚本 [<sup>目录</sup>](#content)</h4></a>

要往MySQL数据库中写入数据，需要用**INSERT INTO 语句**

```
INSERT INTO table_name (column1,column2,column3,...)
VALUES (value1,value2,value3,...);
```

一般情况下我们往数据库中写入少数几条数据时，直接在MySQL的交互环境中执行INSERT INTO 语句即可，但是当要批量导入时，再采用在MySQL的交互环境中执行INSERT INTO 语句，明显是低效而不现实的，这个时候我们可以写一个sql脚本，来执行数据库的批量操作。

简单来说sql脚本就是把多个INSERT INTO 语句写到一个文件里

sql脚本的文件起始部分（为人类和小鼠的数据分别准备一个sql文件，创建的对应表名分别为 lincRNA_h 和 lincRNA_m ，以下以人类的为例）：

```
SET NAMES utf8;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
--  Table structure for `lincRNA_h`
-- ----------------------------

DROP TABLE IF EXISTS `lincRNA_h`;

CREATE TABLE lincRNA_h (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `chrom` varchar(255) NOT NULL DEFAULT '' COMMENT '染色体',
  `biotype` varchar(255) NOT NULL DEFAULT '' COMMENT '基因类型',
  `feature` varchar(255) NOT NULL DEFAULT '' COMMENT '结构单元',
  `start` int(11) NOT NULL DEFAULT '0' COMMENT '起始位置',
  `end` int(11) NOT NULL DEFAULT '0' COMMENT '终止位置',
  `geneid` varchar(255) NOT NULL DEFAULT '' COMMENT '基因ENsembleId',
  `genename` varchar(255) NOT NULL DEFAULT '' COMMENT '基因名',
  `transcriptid` varchar(255) NOT NULL DEFAULT '' COMMENT '转录本ENsembleId',
  `exon` int(11) NOT NULL DEFAULT '0' COMMENT '转录本exon序号',
  PRIMARY KEY (`id`) 
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Records of `lincRNA_h`
-- ----------------------------
```

然后，用perl单行命令实现以下转换：

```
1	lincRNA	gene	29554	31109	ENSG00000243485	MIR1302-2HG	-	-
```

<p align="center"><img src=./picture/InAction-PHP-MySQL-arrow.jpg width=20 /></p>

```
INSERT INTO lincRNA_h （chrom,biotype,feature,start,end,geneid,genename,transcriptid,exon) VALUES ("1","lincRNA","gene","29554","31109","ENSG00000243485","MIR1302-2HG","-","-");
```

并将转换后的结果追加到上一步已经写好起始部分的sql脚本的后面

```
$ perl -ane 'chomp;print "INSERT INTO lincRNA_h (chrom,biotype,feature,start,end,geneid,genename,transcriptid,exon) VALUES (\"$F[0]\",\"$F[1]\",\"$F[2]\",\"$F[3]\",\"$F[4]\",\"$F[5]\",\"$F[6]\",\"$F[7]\",\"$F[8]\");\n"' /Path/To/lincRNA_GRCh38.91.gtf.format >>/Path/To/lincRNA_h.sql
```

<a name="write-data"><h4>写入数据 [<sup>目录</sup>](#content)</h4></a>

登录，进入MySQL的交互环境，才能进行以下操作

```
# 输入密码，登录MySQL
$ mysql -u username -p

# 查看已有的数据库
mysql> show databases;

# 选择要进行操作的数据库，比如我们要操作的数据库为lincRNAdb
## 注意：请确认你具有该数据库的写入权限，否则无法写入
mysql> use lincRNAdb;

# 执行sql脚本，向数据库中批量导入数据
mysql> source /Path/To/lincRNA_h.sql;

# 查看当前使用的数据库中已有表格，确保数据已成功导入：是否有lincRNA_h表格？
mysql> show tables;

# 查看表格中的数据是否正确，为了避免将表中的数据全部打印出来，请使用where子句，只打印出前100条记录
mysql> select * from lincRNA_h where id<100;
```

<a name="php-code"><h2>编辑php脚本 [<sup>目录</sup>](#content)</h2></a>

<a name="register"><h3>注册 [<sup>目录</sup>](#content)</h3></a>

<a name="register-page"><h4>创建注册页面 [<sup>目录</sup>](#content)</h4></a>

<p align="center"><img src=./picture/InAction-PHP-MySQL-register.png width=300 /></p>

表单部分的代码（想了解表单的相关知识，请点 [这里](https://github.com/Ming-Lian/Memo/blob/master/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%EF%BC%9APHP%E4%B8%80%E5%91%A8%E9%80%9F%E6%88%90.md#form-and-input)）：

```
<form action="registeraction.php" method="post">
<table border="0">
<tr>
	<td>用户名：</td>
	<td><input type="text" id="id_name" name="username" required="required"></td>
</tr>
<tr>
	<td>密&nbsp;&nbsp;&nbsp;码：</td>
	<td><input type="password" id="password" name="password" required="required"></td>
</tr>
<tr>
	<td>重复密码：</td>
	<td><input type="password" id="re_password" name="re_password" required="required"></td>
</tr>
<tr>
	<td>性别：</td>
	<td>
		<input type="radio" id="sex" name="sex" value="男">男
		<input type="radio" id="sex" name="sex" value="女">女
	</td>
</tr>
<tr>
	<td>QQ：</td>
	<td><input type="text" id="qq" name="qq" required="required"></td>
</tr>
<tr>
	<td>Email：</td>
	<td><input type="email" id="email" name="email" required="required"></td>
</tr>
<tr>
	<td>电话：</td>
	<td><input type="text" id="phone" name="phone" required="required"></td>
</tr>
<tr>
	<td>地址：</td>
	<td><input type="text" id="address" name="address" required="required"></td>
</tr>
<tr>
	<td colspan="2" align="center" style="color:red;font-size:10px;">
<!--提示信息-->
<?php
$err=isset($_GET["err"])?$_GET["err"]:"";
switch($err) {
	case 1:
		echo "用户名已存在！";
 		break;
	case 2:
		echo "密码与重复密码不一致！";
		break;
	case 3:
		echo "注册成功！";
		break;
}
?>
</td>
</tr>
<tr>
	<td colspan="2" align="center">
		<input type="submit" id="register" name="register" value="注册">
		<input type="reset" id="reset" name="reset" value="重置">
	</td>
</tr>
<tr>
	<td colspan="2" align="center">如果已有账号，快去<a href="login.php">登录</a>吧！</td>
</tr>
</table>
</form>
```

表单信息提交的对象为同一目录下的`registeraction.php`脚本

<a name="registeraction-php"><h4>注册信息处理脚本 [<sup>目录</sup>](#content)</h4></a>

`registeraction.php`脚本

```
<?php
// 声明变量

$username = isset($_POST['username'])?$_POST['username']:"";
$password = isset($_POST['password'])?$_POST['password']:"";
$re_password = isset($_POST['re_password'])?$_POST['re_password']:"";
$sex = isset($_POST['sex'])?$_POST['sex']:"";
$qq = isset($_POST['qq'])?$_POST['qq']:"";
$email = isset($_POST['email'])?$_POST['email']:"";
$phone = isset($_POST['phone'])?$_POST['phone']:"";
$address = isset($_POST['address'])?$_POST['address']:"";

if($password == $re_password) {
	// 建立连接，需要以root的身份登录MySQL，因为只有root用户才具有数据库的写权限
	// 使用面向对象的MySQLi方法
	$conn = new mysqli('localhost','root','','php'); // 第三项需填入root用户的密码，若设置为免密登录，则可以为空
	// 准备SQL语句,查询用户名
	$sql_select="SELECT username FROM User WHERE username = '$username'";
	// 执行SQL语句
	$result = $conn->query($sql_select);
	$row = $result->fetch_array();
	// 判断用户名是否已存在
	if($username == $row['username']) {
		//用户名已存在，显示提示信息
		header("Location:register.php?err=1");
	} else {

		//用户名不存在，插入数据
		//准备SQL语句
		$sql_insert = "INSERT INTO User(username,password,sex,qq,email,phone,address) VALUES('$username','$password','$sex','$qq','$email','$phone','$address')";
		//执行SQL语句
		$conn->query($sql_insert);
		header("Location:register.php?err=3");
	}

	//关闭数据库
	$conn->close($conn);
} else {
	header("Location:register.php?err=2");
}

?>
```

<a name="login"><h3>登录 [<sup>目录</sup>](#content)</h3></a>

<a name="login-page"><h4>登录页面 [<sup>目录</sup>](#content)</h4></a>

<p align="center"><img src=./picture/InAction-PHP-MySQL-login.png width=300 /></p>

```
<form id="loginform" action="loginaction.php" method="post">
<table border="0">
<tr>
	<td>用户名：</td>
	<td><input type="text" id="name" name="username" required="required" value="<?php echo isset($_COOKIE["wang"])?$_COOKIE["wang"]:"";?>"></td>
</tr>
<tr>
	<td>密&nbsp;&nbsp;&nbsp;码：</td>
	<td><input type="password" id="password" name="password"></td>
</tr>
<tr>
	<td colspan="2"><input type="checkbox" name="remember"><small>记住我</td>
</tr>
<tr>
	<td colspan="2" align="center" style="color:red;font-size:10px;">
<!--提示信息-->
<?php
$err=isset($_GET["err"])?$_GET["err"]:"";
switch($err) {
case 1:
	echo "用户名或密码错误！";
	break;
case 2:
	echo "用户名或密码不能为空！";
	break;
}
?>
	</td>
</tr>
<tr>
	<td colspan="2" align="center">
		<input type="submit" id="login" name="login" value="登录">
		<input type="reset" id="reset" name="reset" value="重置">
	</td>
</tr>
<tr>
	<td colspan="2" align="center">还没有账号，快去<a href="register.php">注册</a>吧！</td>
</tr>
</table>
</form>
```

<a name="login-php"><h4>登录脚本 [<sup>目录</sup>](#content)</h4></a>

<a name="login-php-1">方法一：</a>

该方法为，用root用户权限从User表中提取相应的用户名和密码信息，与用户填写的用户名和密码进行比较，来判断用户用户是否拥有数据库的访问权限

该方法需要拥有root用户的账户密码，若没有，请使用第二种方法

```
<?php
// 声明变量
$username = isset($_POST['username'])?$_POST['username']:"";
$password = isset($_POST['password'])?$_POST['password']:"";
$remember = isset($_POST['remember'])?$_POST['remember']:"";

// 判断用户名和密码是否为空
if(!empty($username)&&!empty($password)) {
	// 建立连接
	// 使用面向对象的方法
	$conn = new mysqli('localhost','root','','php');
	// 准备SQL语句
	$sql_select = "SELECT user,password FROM User WHERE user = '$username' AND password = '$password'";
	//执行SQL语句
	$result = $conn->query($sql_select);

	$row = $result->fetch_assoc();

	//判断用户名或密码是否正确
	if($username==$row['username']&&$password==$row['password']) {
		//选中“记住我”
		if($remember=="on") {
			//创建cookie
			setcookie("wang", $username, time()+7*24*3600);
		}
		//开启session
		session_start();
		//创建session
		$_SESSION['user']=$username;
		$_SESSION['password']=$password;
		//写入日志
		$ip = $_SERVER['REMOTE_ADDR'];
		$date = date('Y-m-d H:m:s');

		$info = sprintf("当前访问用户：%s,IP地址：%s,时间：%s \n",$username, $ip, $date);
		$sql_logs = "INSERT INTO Logs(username,ip,date) VALUES('$username','$ip','$date')";

		//日志写入文件，如实现此功能，需要创建文件目录logs
		$f = fopen('./logs/'.date('Ymd').'.log','a+');

		fwrite($f,$info);
		fclose($f);

		//跳转到loginsucc.php页面
		header("Location:loginsucc.php");
		//关闭数据库
		$conn->close();
	}else {
		//用户名或密码错误，赋值err为1
		header("Location:login.php?err=1");
	}
}else {
	//用户名或密码为空，赋值err为2
	header("Location:login.php?err=2");
}

?>
```

<a name="login-php-2">方法二：</a>

该方法通过使用用户填写的用户名和密码直接尝试登陆MySQL，通过登录结果来判断用户是否拥有数据库的使用权限

```
<?php
// 声明变量
$username = isset($_POST['username'])?$_POST['username']:"";
$password = isset($_POST['password'])?$_POST['password']:"";
$remember = isset($_POST['remember'])?$_POST['remember']:"";

// 判断用户名和密码是否为空
if(!empty($username)&&!empty($password)) {
	// 建立连接
	// 使用面向对象的方法，使用用户填写的用户名和密码直接尝试登陆MySQL
	$conn = new mysqli('localhost',$username,$password,'testdb');
	
	if ($conn->connect_error) {
		// 连接失败，用户名或密码错误，赋值err为1
		header("Location:login.php?err=1");
	}else{
		//选中“记住我”
		if($remember=="on") {
			//创建cookie
			setcookie("wang", $username, time()+7*24*3600);
		}
		//开启session
		session_start();
		//创建session
		$_SESSION['user']=$username;
		$_SESSION['password']=$password;

		//跳转到loginsucc.php页面
		header("Location:loginsucc.php");
		//关闭数据库
		$conn->close();
	}
}else {
	//用户名或密码为空，赋值err为2
	header("Location:login.php?err=2");
}
?>
```

<a name="login-success"><h4>登录成功 [<sup>目录</sup>](#content)</h4></a>

<p align="center"><img src=./picture/InAction-PHP-MySQL-loginsucc.png width=300 /></p>

通过判断全局变量`$_SESSION['user']`是否定义来判断是否成功登录

```
<?php
//开启session
session_start();
//声明变量
$username= isset($_SESSION['user'])?$_SESSION['user']:"";
//判断session是否为空
if(!empty($username)){
?>
	<h1>登录成功！</h1>
	欢迎您！
<?php
	echo $username;
?>
	// 继续检索数据库
	<br/>
	<a href="databaseQuery.php">检索数据库</a>
	<br/>
	<a href="logout.php">退出</a>
<?php
}else {
	//未登录，无权访问
	?>
	<h1>你无权访问！！！</h1>
<?php
}
?>
```

<a name="logout"><h4>退出登录 [<sup>目录</sup>](#content)</h4></a>

```
<?php
//开启session
session_start();
//撤销session
session_unset();
session_destroy();
//跳转到login.php
header("Location:login.php");
?>
```

<a name="query"><h3>检索数据库 [<sup>目录</sup>](#content)</h3></a>

检索数据库，需要用到数据库用户的**用户名**和**密码**，用于在之前的登录过程中，我们已经将用户名和密码存储在 $_SESSION 中，所以可以通过 `$_SESSION['user']` 和 `$_SESSION['password']` 获得

这里将表单部分与PHP命令写在一个PHP脚本中，脚本命名为`databaseQuery.php`

<a name="query-form"><h4>表单部分 [<sup>目录</sup>](#content)</h4></a>

<p align="center"><img src=./picture/InAction-PHP-MySQL-query-form.png width=800 /></p>

```
<form action="#" method="post">
<table>
<tr>
	<td align="left">物&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp种：</td>
	<td colspan="2" align="left">
		<select name="specie">
			<option value="">选择一个物种:</option>
			<option value="human">Homo Sapiens</option>
			<option value="mouse">Mus musculus</option>
		</select>
	</td>
</tr>
<tr>
	<td align="left">基因名/基因ID:</td>
	<td colspan="2" align="left"><input type="text" name="geneName" value="MIR1302-2HG"></td>
</tr>
<tr>
	<td align="left">基&nbsp;&nbsp;&nbsp因&nbsp;&nbsp;&nbsp位&nbsp;&nbsp;&nbsp置：</td>
	<td colspan="2" align="left">
		<select name="chrom">
			<option value="">选择一条染色体：</option>
			<option value="1">chr1</option>
			<option value="2">chr2</option>
			<option value="3">chr3</option>
			<option value="4">chr4</option>
			<option value="5">chr5</option>
			<option value="6">chr6</option>
			<option value="7">chr7</option>
			<option value="8">chr8</option>
			<option value="9">chr9</option>
			<option value="10">chr10</option>
			<option value="11">chr11</option>
			<option value="12">chr12</option>
			<option value="13">chr13</option>
			<option value="14">chr14</option>
			<option value="15">chr15</option>
			<option value="16">chr16</option>
			<option value="17">chr17</option>
			<option value="18">chr18</option>
			<option value="19">chr19</option>
			<option value="20">chr20</option>
			<option value="21">chr21</option>
			<option value="22">chr22</option>
			<option value="X">chrX</option>
			<option value="Y">chrY</option>
		</select>	
	</td>
	<td>
		区间：<input type="text" name="start"> - <input type="text" name="end">
	</td>
</tr>
<tr>
	<td colspan="2" align="center" style="color:red;font-size:10px;">
<!--提示信息-->
<?php
$err=isset($_GET["err"])?$_GET["err"]:"";
switch($err) {
case 1:
	echo "表单填写错误:物种必须选，基因名和基因位置两项至少选一项填写！";
	break;
case 2:
	echo "表单填写错误：区间起始和终止位置需为整数，且起始位置小于终止位置！";
	break;
case 3:
	echo "未登陆，无权访问！";
	echo "快去<a href='login.php'>登录</a>吧！";
	break;
}
?>
	</td>
</tr>
<tr>
	<td align="center"><input type="submit" name="submit" value="Submit"></td>
	<td align="center"><input type="reset" value="Reset"></td>
</tr>
</table>
</form>
```

<a name="query-php"><h4>PHP脚本 [<sup>目录</sup>](#content)</h4></a>


```
<?php
//开启session
session_start();
// 获取用户名和密码
$username=isset($_SESSION['user'])?$_SESSION['user']:"";
$password=isset($_SESSION['password'])?$_SESSION['password']:"";

// 获取表单提交数据
$specie=isset($_POST['specie'])?$_POST['specie']:"";
$geneName=isset($_POST['geneName'])?$_POST['geneName']:"";
$chrom=isset($_POST['chrom'])?$_POST['chrom']:"";
$start=isset($_POST['start'])?$_POST['start']:"";
$end=isset($_POST['end'])?$_POST['end']:"";
$submit=isset($_POST['submit'])?$_POST['submit']:"";

if($submit){
	// 判断提交的检索信息是否满足要求：物种必须选，基因名和基因位置两项至少选一项填写
	if(empty($specie)||(empty($geneName)&&empty($chrom))){
		header("Location:databaseQuery.php?err=1");

	// 判断用户名和密码是否已经设置
	}elseif(!empty($username)&&!empty($password)){
		// 连接数据库
		$conn=new mysqli('localhost',$username,$password,'testdb');
		if($conn->connect_error){
			header("Location:databaseQuery.php?err=3");
		}
		// 设置要检索的数据库表格名
		$table="";
		if($specie=="human"){
			$table="lincRNA_h";
		}else{
			$table="lincRNA_m";
		}
		
		// 创建SQL查询语句
		$sql="SELECT * from $table where";
		// 根据实际表单填写情况追加查询条件
		$bool_and=false;
		if(!empty($geneName)){
			$sql .= " genename='$geneName' or geneid='$geneName'";
			$bool_and=true;
		}
		if(!empty($chrom)){
			if($bool_and){
				$sql .= " and";
			}
			$sql .= " chrom='$chrom'";
			if(!empty($start)&&!empty($end)){
				// 判断提交的起始点与终止点是否正确，即是否为整数，且起始点小于终止点
				if(is_int(intval($start))&&is_int(intval($end))&&intval($start)<intval($end)){
					$sql .= " and start>=$start and end<=$end";
				}else{
					header("Location:databaseQuery.php?err=2");
				}
			}
		}
		$sql .= ";";
	
		// 执行查询语句
		$result=$conn->query($sql);
	
		
	
		if($result->num_rows > 0){
			// 输出查询结果
			echo "<table style='border: solid 1px black;'><tr><th>Id</th><th>chrom</th><th>Biotype</th><th>Feature</th><th>Start</th><th>End</th><th>GeneId</th><th>GeneName</th><th>TranscriptId</th><th>ExonNumber</th></tr>";
			
			while($row = $result->fetch_array()){
        			echo "<tr><td>".$row['id']."</td><td>".$row['chrom']."</td><td>".$row['biotype']."</td><td>".$row['feature']."</td><td>".$row['start']."</td><td>".$row['end']."</td><td>".$row['geneid']."</td><td>".$row['genename']."</td><td>".$row['transcriptid']."</td><td>".$row['exon']."</td></tr>";
    			}
		}else{
    			echo "未检索到满足条件的记录!";
		}
	}else{
		// 跳转到当前页面，并为err赋值1
		header("Location:databaseQuery.php?err=3");
	}
}
?>
```


参考资料：

(1) [PHP+MySQLi实现注册和登陆](https://www.fenxd.com/111.html)

