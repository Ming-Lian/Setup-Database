<a name="content">目录</a>

[PHP+MySQL实战：小鼠与人类lincRNA数据库](#title)
- [Part1：lincRNA注释信息数据库](#part1)
	- [实现目标](#goal-of-part1)
	- [思路](#idea-of-part1)
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
- [Part2：内容与功能升级](#part2)
	- [实现目标](#goal-of-part2)
	- [思路](#idea-of-part2)
	- [添加blast功能](#add-blast-application)
	- [添加lincRNA表达谱](#add-lincrna-expression-profile)
		- [获取在两个样本组的表达谱](#acquire-lincrns-profile)
			- [数据集简单介绍与下载](#dataset-introduction-and-download)
			- [跑RNA-seq分析流程](#run-rnaseq-pipeline)
		- [将差异表达结果写进MySQL](#write-profile-data)
		- [检索表达谱数据库](#search-profile-database)
	- [添加批量提交数据功能](#add-batch-submit)
- [番外篇：前端优化——html+css](#front-end-optimization)
	- [登录界面优化](#updata-login-ui)
	- [导航条菜单的制作](#navigation-menus)




<h1 name="title">PHP+MySQL实战：小鼠与人类lincRNA据库</h1>

<a name="part1"><h2>Part1：lincRNA注释信息数据库 [<sup>目录</sup>](#content)</h2></a>

<a name="goal-of-part1"><h2>实现目标 [<sup>目录</sup>](#content)</h2></a>

- 将lincRNA的gtf格式的注释信息写到MySQL数据库中
- 用户需要登录才具有数据库访问权限
- 提供数据库的检索功能

<a name="idea-of-part1"><h2>思路 [<sup>目录</sup>](#content)</h2></a>

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

- 编辑php脚本`lincRNA_Ano_data_batch_import.php`

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
$stmt=$conn_prepare("INSERT INTO $argv[1] (chrom,biotype,feature,start,end,geneid,genename,transcriptid,exon) VALUES (?,?,?,?,?,?,?,?,?)") ;
$stmt->bind_param("sssiisssi",$chrom,$biotype,$feature,$start,$end,$geneid,$genename,$transcriptid,$exon);

// 打开文件
$ file=fopen("$argv[2]",'r') or exit("Unable to open file!");

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
$ php -f lincRNA_Ano_data_batch_import.php <tablename> <data-to-import>
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
	<td colspan="2"><input type="checkbox" name="remember" value="on"><small>记住我</td>
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
	<a href="databaseQuery_Ano.php">检索数据库</a>
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

这里将表单部分与PHP命令写在一个PHP脚本中，脚本命名为`databaseQuery_Ano.php`

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
	echo "未登陆，无权访问！<br>";
	echo "快去<a href='login.php'>登录</a>吧";
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
			header("Location:databaseQuery_Ano.php?err=3");
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
					header("Location:databaseQuery_Ano.php?err=2");
				}
			}				
		}
		$sql .= ";";
	
		// 执行查询语句
		$result=$conn->query($sql);
	
		
	
		if($result->num_rows > 0){
			// 输出查询结果
			echo "<table ><tr><th>chrom</th><th>Biotype</th><th>Feature</th><th>Start</th><th>End</th><th>GeneId</th><th>GeneName</th><th>TranscriptId</th><th>ExonNumber</th><th>操作</th></tr>";
			
			while($row = $result->fetch_array()){
				echo "<tr><td>".$row['chrom']."</td><td>".$row['biotype']."</td><td>".$row['feature']."</td><td>".$row['start']."</td><td>".$row['end']."</td><td>".$row['geneid']."</td><td>".$row['genename']."</td><td>".$row['transcriptid']."</td><td>".$row['exon']."</td><td><a style=\"padding:2px;\" href=\"Update_Ano.php?id=".$row['id']."\">Update</a></tr>";
    		}
		}else{
    		echo "未检索到满足条件的记录!";
    	}
	}else{
		// 跳转到当前页面，并为err赋值1
		header("Location:databaseQuery_Ano.php?err=3");
	}
}
?>
```

<a name="part2"><h2>Part2：内容与功能升级 [<sup>目录</sup>](#content)</h2></a>

<a name="goal-of-part2"><h2>实现目标 [<sup>目录</sup>](#content)</h2></a>

- 增加 blast 功能：将用户提交的序列与 lncRNA 的序列库进行 blast 比对
- 创建 lncRNA 表达数据库，除了 part1 中提供的基础检索功能外，还要提供绘图功能
- 增加数据库的删除、追加（单条记录追加和批量追加）与编辑功能

<a name="idea-of-part2"><h2>思路 [<sup>目录</sup>](#content)</h2></a>

**1\. 增加 blast 功能**

目标序列的指定方式：
- 用户在输入框中输入
- 用户提交目标序列的 fasta 文件

创建lncRNA的 blast 序列库（即lncRNA的 blast 索引)：

- 根据 part1 [《准备需要写入数据库中的数据》](#prepare-data) 中得到的文件`lincRNA_GRCm38.91.gtf.format`和`lincRNA_GRCh38.91.gtf.format`，从中提取unique的lincRNA的Ensembl gene id
- 利用lincRNA的Ensembl gene id，从Ensembl的BioMart中下载lincRNA对应的序列
- 对这些序列建立blast索引

**2\. 创建 lncRNA 表达数据库**

从公共数据库（如GEO）中直接下载RNA-seq表达谱文件，或者下载原始fastq文件，自己跑RNA-seq分析流程来获取表达谱，然后提取其中的lincRNA部分，进行差异表达分析后将差异表达结果写进MySQL数据库中，同时保留表达谱文件，以供后期绘图时访问

- 表达数据来源：从GEO数据库中下载某个RNA-seq实验的表达谱文件

- 差异表达分析：使用DESeq2进行差异表达分析，DESeq2使用方法请点 [这里](https://github.com/Ming-Lian/NGS-analysis/blob/master/RNA-seq.md#deseq2)

- 将差异表达结果写进MySQL数据库中：过程与 part1 [《将数据写入数据库中——方法一：使用MySQLi》](#use-mysqli)一样

- 绘图：即在检索获得差异表达结果列表后，每条记录后提供一个绘图按钮，用户点击后即可绘制出该基因在不同samples的表达柱状图，可通过调用R脚本实现

**3\. 增加数据库的删除、追加与编辑功能**

这部分应该是整个part2中最容易实现的部分


- 删除 —— **注意：在真正删除之前，请要求用户进行再次确认**
	- 删除一条记录：用SQL的DELETE语句
	- 删除多条记录：在每条记录前有一个复选框，选中多条记录前的复选框，然后点击页面某个角落的“Delete”按钮，提交删除操作请求
	
- 追加
	- 追加一条记录
	- 批量追加：可以让用户提交文件来实现批量追加，不过真正进行追加操作之前，务必进行文件格式的检查

- 编辑 —— 用UPDATE语句

<a name="add-blast-application"><h2>添加blast功能 [<sup>目录</sup>](#content)</h2></a>





<a name="add-lincrna-expression-profile"><h2>添加lincRNA表达谱 [<sup>目录</sup>](#content)</h2></a>

<a name="acquire-lincrns-profile"><h3>获取在两个样本组的表达谱 [<sup>目录</sup>](#content)</h3></a>

<a name="dataset-introduction-and-download"><h4>数据集简单介绍与下载 [<sup>目录</sup>](#content)</h4></a>

该笔记中使用的数据集为 [Nature Protocols 文章](#nat-pro)中用到的数据集

| sample id | Sex | population |
|:---|:---|:---|
|	ERR188245 	|	Female 	|	GBR	|
|	ERR188428 	|	Female 	|	GBR	|
|	ERR188337 	|	Female 	|	GBR	|
|	ERR188401 	|	Male 	|	GBR	|
|	ERR188257 	|	Male 	|	GBR	|
|	ERR188383 	|	Male 	|	GBR	|
|	ERR204916 	|	Female 	|	YRI	|
|	ERR188234 	|	Female 	|	YRI	|
|	ERR188273 	|	Female 	|	YRI	|
|	ERR188454 	|	Male 	|	YRI	|
|	ERR188104 	|	Male 	|	YRI	|
|	ERR188044 	|	Male 	|	YRI	|

文章作者已经将数据集打包好了，下载链接：`ftp://ftp.ccb.jhu.edu/pub/RNAseq_protocol/chrX_data.tar.gz`

```
# 下载
$ nohup wget -c ftp://ftp.ccb.jhu.edu/pub/RNAseq_protocol/chrX_data.tar.gz >download.log 2>&1 &

# 解压
$ tar zxvf chrX_data.tar.gz
```

解压后可以看到数据集的组成
```
|---chrX_data
	|---genes
		|---chrX.gtf
	|---genome
		|---chrX.fa
	|---indexes
		|---chrX_tran.[1-7].ht2
	|---samples
		|---ERR*_chrX_[12].fastq.gz
```

<a name="run-rnaseq-pipeline"><h4>跑RNA-seq分析流程 [<sup>目录</sup>](#content)</h4></a>

RNA-seq分析流程，请参考 [这里](https://github.com/Ming-Lian/NGS-analysis/blob/master/RNA-seq.md)

一般第一步是创建hisat2索引，由于数据集中已经提供了（在`chrX_data/indexes`文件夹下），所以跳过这一步

- **hisat2比对**

在比对前，先准备好一个保存样本Id的文本`samplelist.txt`：

```
ERR188245
ERR188428
ERR188337
ERR188401
ERR188257
ERR188383
ERR204916
ERR188234
ERR188273
ERR188454
ERR188104
ERR188044
```

开始执行批量比对

```
$ mkdir map

$ nohup cat samplelist.txt | while read i;
do
	hisat2 -p 10 --dta -x indexes/chrX_tran -1 samples/${i}_chrX_1.fastq.gz -2 samples/${i}_chrX_2.fastq.gz | \
	samtools sort -@ 8 -O bam -o map/${i}_chrX.sort.bam 1>map/${i}_map.log 2>&1
done &
```

- **stringtie转录本拼接**

```
$ mkdir asm

# 拼接
$ cat cat samplelist.txt | while read i;
do
	stringtie -p 16 -G genes/chrX.gtf -o asm/${i}_chrX.gtf -l $i map/${i}_chrX.sort.bam 1>asm/${i}_strg_assm.log 2>&1
done

# 准备mergelist.txt文件
$ awk '{print "asm/"$0"_chrX.gtf"}' samplelist.txt >mergelist.txt

# merge多样本的转录本拼接结果
$ stringtie --merge -p 16 -G genes/chrX.gtf -o asm/merge.gtf mergelist.txt 1>asm/strg_merge.log 2>&1
```

- **stringtie定量**

以read count进行定量，作为DESeq2或edgeR的输入

```
$ mkdir quant

# 转录本定量
$ cat samplelist.txt | while read i;
do
	stringtie -e -p 16 -G asm/merge.gtf -o quant/${i}_chrX_quant.gtf map/${i}_chrX.sort.bam 1>quant/${i}_chrX_quant.log 2>&1
done

# 整合多样本的转录本定量结果
## 先准备sample_lst.txt文件，格式如下：
		ERR188021 <PATH_TO_ERR188021.gtf>
		ERR188023 <PATH_TO_ERR188023.gtf>
		...
$ perl -ne 'chomp;print "$_\tquant/${_}_chrX_quant.gtf\n"' samplelist.txt >samplelist.quant.txt

## 执行定量结果整合
$ python2 /Path/To/prepDE.py -i sample_lst.txt
```

最后会在工作目录下产生两个read count定量文件：
> - gene定量：gene_count_matrix.csv
> - transcript定量：transcript_count_matrix.csv

想下载这两个文件，请点 [这里](./txt-supply/)

- **差异表达分析：DESeq2**

DESeq2要求输入的表达矩阵是read counts

构建 DESeqDataSet 对象

```
# 构建表达矩阵
count_table<-read.csv("gene_count_matrix.csv",row.names="gene_id")
count_matrix<-as.matrix(count_table)
# 构建分组矩阵
phenodata<-read.csv("geuvadis_phenodata.csv",row.names="ids")
# 构建 DESeqDataSet 对象
dds <- DESeqDataSetFromMatrix(countData = count_matrix,
	colData = phenodata,
	design = ~ population)
```

差异表达分析

```
dds <- DESeq(dds)
res <- results(dds)
resOrdered <- res[order(res$padj),]
diffResult <- as.data.frame(resOrdered)
write.table(diffResult,"gene_diffResult.txt",sep="\t",quote=F)
```

这一步最终得到差异表达分析结果文件`gene_diffResult.txt`，想下载这两个文件，请点 [这里](./txt-supply/)

<a name="write-profile-data"><h3>将差异表达结果写进MySQL [<sup>目录</sup>](#content)</h3></a>

过程与 part1 [方法一：使用MySQLi](#use-mysqli) 相同

```
# 创建表格DiffExp_result

mysql> CREATE TABLE DiffExp_result (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `RefSeq` varchar(255) NOT NULL DEFAULT '' COMMENT 'RefSeq id',
  `baseMean` DOUBLE(40,20) NOT NULL DEFAULT '0' COMMENT 'the base mean over all rows',
  `log2FoldChange` DOUBLE(40,20) NOT NULL DEFAULT '0' COMMENT 'log2 fold change (MAP): treatment OHT vs Control',
  `lfcSE` DOUBLE(40,20) NOT NULL DEFAULT '0' COMMENT 'standard error: treatment OHT vs Control',
  `stat` DOUBLE(40,20) NOT NULL DEFAULT '0' COMMENT 'Wald statistic: treatment OHT vs Control',
  `pvalue` FLOAT(20) NOT NULL DEFAULT '0' COMMENT 'Wald test p-value: treatment OHT vs Control',
  `padj` FLOAT(20) NOT NULL DEFAULT '0' COMMENT 'BH adjusted p-values',
  PRIMARY KEY (`id`) 
) ;
```

|	数据类型	|	描述	|
|:---|:---|
|	TINYINT(size)	|	-128 到 127 常规。0 到 255 无符号*。在括号中规定最大位数。	|
|	SMALLINT(size)	|	-32768 到 32767 常规。0 到 65535 无符号*。在括号中规定最大位数。	|
|	MEDIUMINT(size)	|	-8388608 到 8388607 普通。0 to 16777215 无符号*。在括号中规定最大位数。	|
|	INT(size)	|	-2147483648 到 2147483647 常规。0 到 4294967295 无符号*。在括号中规定最大位数。	|
|	BIGINT(size)	|	-9223372036854775808 到 9223372036854775807 常规。0 到 18446744073709551615 无符号*。在括号中规定最大位数。	|
|	FLOAT(size,d)	|	带有浮动小数点的小数字。在括号中规定最大位数。在 d 参数中规定小数点右侧的最大位数。	|
|	DOUBLE(size,d)	|	带有浮动小数点的大数字。在括号中规定最大位数。在 d 参数中规定小数点右侧的最大位数。	|
|	DECIMAL(size,d)	|	作为字符串存储的 DOUBLE 类型，允许固定的小数点。	|


基于 part1 中的php脚本`data_batch_import.php`稍作修改即可复用

需要改动的位置如下图：

<p align="center"><img src=./picture/InAction-PHP-MySQL-part2-modify-phpscript-location.jpg width=900 /></p>

直接手动修改PHP脚本，保存为`lincRNA_DiffExp_data_batch_import.php`

以下只给出修改部分的代码

```
// 预处理及绑定
$stmt=$conn->prepare("INSERT INTO DiffExp_result (RefSeq,baseMean,log2FoldChange,lfcSE,stat,pvalue,padj) VALUES (?,?,?,?,?,?,?)");
$stmt->bind_param("sdddddd",$RefSeq,$baseMean,$log2FoldChange,$lfcSE,$stat,$pvalue,$padj);

// 打开文件，通过$argv[1]进行脚本传参
$file=fopen("$argv[1]",'r') or exit("Unable to open file!");

// 逐行读取文件，并写入数据库
fgets($file);	// 跳过第一行表头
while(!feof($file)){
	$data=explode("\t",fgets($file)); // 以tab为间隔标识将字符串打散
	
	// 设置参数
	$RefSeq=$data[0];
	$baseMean=$data[1];
	$log2FoldChange=$data[2];
	$lfcSE=$data[3];
	$stat=$data[4];
	$pvalue=$data[5];
	$padj=$data[6];
	// 执行
	$stmt->execute();
}
```

最后执行php脚本，完成数据的批量导入

```
$ php -f lincRNA_DiffExp_data_batch_import.php <data-to-import>
```

<a name="search-profile-database"><h3>检索表达谱数据库 [<sup>目录</sup>](#content)</h3></a>

过程类似于 part1 [检索数据库](#query)

表单部分，提供以下检索筛选条件
> - log2FoldChange 一般认为其绝对值大于1，差异显著
> - p-value 一般认为其值小于等于0.05，差异显著
> - q-value 一般认为其值小于等于0.05，差异显著

```
<form action="#" method="post">
<table style="text-align:center; border:0;">
<tr>
	<td>|log2FoldChange| >=</td>
	<td><input type="text" name="log2FoldChange" value="1"></td>
</tr>
<tr>
	<td>p-value <=</td>
	<td><input type="text" name="p_value" value="0.05"></td>
</tr>
<tr>
	<td>q-value <=</td>
	<td><input type="text" name="q_value"></td>
</tr>
<tr>
	<td align="center"><input type="submit" name="submit" value="Submit"></td>
	<td align="center"><input type="reset" value="Reset"></td>
</tr>
<!-- 错误提示信息 -->
<tr>
	<td style="color:red; font-size:10px;">
<?php
$err=isset($_GET["err"])?$_GET["err"]:"";
switch($err) {
case 1:
	echo "表单填写错误：至少要填写一项！";
	break;
case 2:
	echo "表单填写错误：填写信息必须是数字！";
	break;
case 3:
	echo "未登陆，无权访问！";
	echo "快去<a href='login.php'>登录</a>吧！";
	break;
}
?>	
	</td>
</tr>
</table>
</form>
```

php脚本部分

```
<?php
//开启session
session_start();
// 获取用户名和密码
$username=isset($_SESSION['user'])?$_SESSION['user']:"";
$password=isset($_SESSION['password'])?$_SESSION['password']:"";

// 获取表单提交数据
$log2FoldChange=isset($_POST['log2FoldChange'])?$_POST['log2FoldChange']:"";
$p_value=isset($_POST['p_value'])?$_POST['p_value']:"";
$q_value=isset($_POST['q_value'])?$_POST['q_value']:"";
$submit=isset($_POST['submit'])?$_POST['submit']:"";

if($submit){
	// 判断提交的检索信息是否满足要求：三个提交的变量,至少一个不为空，且若不为空则必须是数值
	if(empty($log2FoldChange)&&empty($p_value)&&empty($q_value)){
		header("Location:databaseQuery_DiffExp.php?err=1");	
	}elseif((empty($log2FoldChange)||is_numeric($log2FoldChange))&&(empty($p_value)||is_numeric($p_value))&&(empty($q_value)||is_numeric($q_value))){
		// 连接数据库
		$conn=new mysqli('localhost',$username,$password,'testdb');
		if($conn->connect_error){
			header("Location:databaseQuery_DiffExp.php?err=3");
		}
		// 创建SQL查询语句
		$sql="SELECT * from DiffExp_result where";
		// 根据实际表单填写情况追加查询条件
		$bool_and=false;
		if(!empty($log2FoldChange)){
			$sql .= " log2FoldChange >= $log2FoldChange or log2FoldChange <= -$log2FoldChange";
			$bool_and=true;
		}
		if(!empty($p_value)){
			if($bool_and){
				$sql .= " and";
			}
			$sql .= " pvalue <= $p_value";
			$bool_and=true;
		}
		if(!empty($q_value)){
			if($bool_and){
				$sql .= " and";
			}
			$sql .= " padj <= $q_value";
		}
		$sql .= ";";
		
		// 执行查询语句
		$result=$conn->query($sql);
		if($result->num_rows > 0){
			// 输出查询结果
			echo "<table><tr><th>RefSeq</th><th>baseMean</th><th>log2FoldChange</th><th>lfcSE</th><th>stat</th><th>pvalue</th><th>padj</th><th>操作</th></tr>";
			
			while($row = $result->fetch_array()){
        			echo "<tr><td>".$row['RefSeq']."</td><td>".$row['baseMean']."</td><td>".$row['log2FoldChange']."</td><td>".$row['lfcSE']."</td><td>".$row['stat']."</td><td>".$row['pvalue']."</td><td>".$row['padj']."</td><td><a href=\"Updata_DiffExp.php\" style=\"padding:2px;\">Update</a><a href=\"Barplox_profile.php\" style=\"padding:2px;\">Barplox</a></td></tr>\n";
    		}
		}else{
    			echo "未检索到满足条件的记录!";
		}
	
	}else{
		header("Location:databaseQuery_DiffExp.php?err=2");
	}
}
?>
```

<a name="add-batch-submit"><h2>添加批量提交数据功能 [<sup>目录</sup>](#content)</h2></a>

基本的实现思路为：
> 提供文件提交的输入框，同时让用户指定向哪个数据库（实际上是不同的表格）提交数据。将用户提交的文件保存为服务器某个目录下的临时文件，然后用 
> - part1 [将数据写入数据库中：方法一：使用MySQLi](#use-mysqli)的脚本`lincRNA_Ano_data_batch_import.php`
> - 或者，part2 [将差异表达结果写进MySQL](#write-profile-data)的脚本`lincRNA_DiffExp_data_batch_import.php`
> 
> 将临时文件的数据追加导入相应的表格中

预备知识：
> - 文件上传，请点 [这里](https://github.com/Ming-Lian/Memo/blob/master/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%EF%BC%9APHP%E4%B8%80%E5%91%A8%E9%80%9F%E6%88%90.md#upload-file)

要实现文件上传，首先要在php脚本所在的文件夹下创建一个权限全开的`upload`文件夹

```
$ mkdir upload

$ chmod 777 upload
```  

接着开始编辑php脚本，保存为`batch_submit_data.php`

表单部分

```
<form action="batch_submit_data.php" method="post" enctype="multipart/form-data">
<table>
<tr>
	<td>选择提交数据库：</td>
	<td>
		<select name="dbname">
			<option value="">选择数据库:</option>
			<option value="ano">基因结构注释</option>
			<option value="diffexp">基因表达谱</option>
		</select>
	</td>
</tr>
<tr>
	<td>选择物种（基因结构注释）：</td>
	<td>
		<select name="specie">
			<option value="">选择一个物种:</option>
			<option value="human">Homo Sapiens</option>
			<option value="mouse">Mus musculus</option>
		</select>
	</td>
</tr>
<tr>
	<td>选择上传数据文件：</td>
	<td>
    	<label for="file">文件名：</label>
    	<input type="file" name="file" id="file"><br>
    </td>
</tr>
<tr>
	<td align="center"><input type="submit" name="submit" value="提交"></td>
	<td align="center"><input type="reset" value="Reset"></td>
</tr>
<!-- 报错信息 -->
<tr>
	<td style="color:red; font-size:10px;">
<?php
$err=isset($_GET["err"])?$_GET["err"]:"";
switch($err) {
case 1:
	echo "表单填写错误：必须选择一个数据库！";
	break;
case 2:
	echo "上传文件格式不符合要求！请参照<a href=\"formatGuid.html\">格式要求</a>进行修改！";
	break;
case 3:
	echo "未登陆，无权访问！";
	echo "快去<a href='login.php'>登录</a>吧！";
	break;
}
?>
</form>
```

php脚本

```
<?php
//开启session
session_start();
// 获取用户名和密码
$username=isset($_SESSION['user'])?$_SESSION['user']:"";
$password=isset($_SESSION['password'])?$_SESSION['password']:"";

// 创建Ano_check函数，进行上传文本的文件格式检查
function Ano_check($fname)
{
	$file=fopen("upload/$fname","r" or exit("Unable to open file!");
	$bool_mark=1;
	while(!feof($file)){
		$data=explode("\t",fgets($file));
		// 检查第一列，是否为1-22或XY
		if(!preg_match("\d{1,2}|[XY]",$data[0])){
			$bool_mark=0;
			break;
		}
		// 检查第二三列，是否都是英文字母
		if(!preg_match("[a-zA-Z]",$data[1])||!preg_match("[a-zA-Z]",$data[2])){
			$bool_mark=0;
			break;
		}
		// 检查第四五列，即start和end，是否是数字
		if(!is_numeric($data[3])||!is_numeric($data[4])){
			$bool_mark=0;
			break;
		}
		// 检查第六八列，即GeneId和TranscriptId,是否以ENSG或ENSG打头
		if(!preg_match("^ENSG",$data[5])||!preg_match("^(ENST)|-",$data[5])){
			$bool_mark=0;
			break;
		}
	}
	return $bool_mark;
}

// 创建DiffExp_check函数，进行上传文本的文件格式检查
function DiffExp_check($fname)
{
	$file=fopen("upload/$fname","r" or exit("Unable to open file!");
	$bool_mark=1;
	while(!feof($file)){
		$data=explode("\t",fgets($file));
		// 检查第一列，RefSeq，是否以大写字母打头
		if(!preg_match("^[A-Z]{2,}",$data[0])){
			$bool_mark=0;
			break;
		}
		// 检查其他列，是否都是数字
		if(!is_numeric($data[1])||!is_numeric($data[2])||!is_numeric($data[3])||!is_numeric($data[4])||!is_numeric($data[5])||!is_numeric($data[6])){
			$bool_mark=0;
			break;
		}
	}
	return $bool_mark;
}


// 获取表单提交数据
$dbname=isset($_POST['dbname'])?$_POST['dbname']:"";
$specie=isset($_POST['specie'])?$_POST['specie']:"";
$submit=isset($_POST['submit'])?$_POST['submit']:"";

if($submit){
	// 检查文件上传是否成功
	if ($_FILES["file"]["error"] > 0)
	{
    	echo "错误：" . $_FILES["file"]["error"] . "<br>";
	}
	
	// 进行文件格式检查
	if(empty($dbname)){
		header("Location:batch_submit_data.php?err=1");
	}
	elseif($dbname="ano"){
		$check_stat=Ano_check($_FILE['file']['name']);
	}else{
		$check_stat=DiffExp_check($_FILE['file']['name']);
	}
	
	// 判断文件格式是否符合要求
	if(!$check_stat){
		header("Location:batch_submit_data.php?err=2");
	// 判断用户名和密码是否已经设置
	}elseif(!empty($username)&&!empty($password)){
		// 连接数据库
		$conn=new mysqli('localhost',$username,$password,'testdb');
		if($conn->connect_error){
			header("batch_submit_data.php?err=3");
		}
		
		// 设置数据库表格名，并执行批量操作
		if($dbname="ano"){
			if($specie=="human"){
				$table="lincRNA_h";
			}else{
				$table="lincRNA_m";
			}
			system("php -f lincRNA_Ano_data_batch_import.php ".$table." upload/".$_FILE['file']['name']." &");
		}else{
			system("php -f lincRNA_DiffExp_data_batch_import.php upload/".$_FILE['file']['name']." &");
		}
		
		// 调用之前的php脚本进行批量提交操作		
	}else{
		header("Location:batch_submit_data.php?err=3");
	}
}
?>
```

<a name="front-end-optimization"><h2>番外篇：前端优化——html+css [<sup>目录</sup>](#content)</h2></a>

<a name="updata-login-ui"><h3>登录界面优化 [<sup>目录</sup>](#content)</h3></a>

实现效果：
> 山寨主流网站的登录界面，即将页面分为左右两半，左侧放置图片，右侧放置登录信息输入框

<p align="center"><img src=./picture/InAction-PHP-MySQL-part3-login-ui.png width=800 /></p>

```
<head>
<title>登录</title>
<style>
/* 设置背景色：碧绿色 */
body{
	background-color:#7FFFAA;
	margin:50px;
}
form {
	background-color:white;
	width:100%;
	margin:auto;
	padding:70px 0;
}
</style>
</head>
<body>
<div>
	<div style="float:left; width:50%; height:100%;">
		<img src=./picture/lncRNAdb-logo.jpg width=100% />
	</div>
	<div style="float:right; width:50%; height:100%;">
	.
	.
	.
	</div>
</div>
</body>
```

<a name="navigation-menus"><h3>导航条菜单的制作 [<sup>目录</sup>](#content)</h3></a>

实现效果：
> 将页面分为左右部分，左侧放置垂直导航栏，右侧放置主面板

<p align="center"><img src=./picture/InAction-PHP-MySQL-part3-page-with-navigation.png width=800 /></p>

使用外部css制定页面渲染风格，css文件保存为`private.css`

```
/* 导航栏样式 */
/* 页面左侧主导航栏（垂直导航栏），占据页面左侧10%的空间，且位置固定 */
.nav_vertical {
	margin:0;
	padding:0;
	width:10%;
	list-style-type:none;
	background-color: #f1f1f1;
	position: fixed;
	height: 100%;
}
.nav_vertical li a{
	display:block;
	padding: 8px 16px;
	text-decoration:none;
	color: #000;	/*  字体颜色：灰色 */
}
.nav_vertical li a:hover{	/* 鼠标悬停时链接块颜色：橙色 */
	background-color:#F60;
	color:#fff
}
.nav_vertical li a.active{	
	background-color: #4CAF50;	/*  激活的链接块颜色：绿色 */
	color:#fff;
}
/* 页面右侧子导航栏（水平导航栏），用于在进行数据库检索时，在不同数据库间切换 */
.nav_horizontal {
	margin-left:10px;
	padding:0;
	list-style-type:none;
}
.nav_horizontal li {
	display:inline;
}
.nav_horizontal li a{
	padding: 8px 16px;
	text-decoration:none;
	background-color: #f1f1f1;
	color: #000;	/*  字体颜色：灰色 */
}
.nav_horizontal li a:hover{	/* 鼠标悬停时链接块颜色：橙色 */
	background-color:#F60;
	color:#fff
}
.nav_horizontal li a.active{	
	background-color: #4CAF50;	/*  激活的链接块颜色：绿色 */
	color:#fff;
}

/* 链接样式 */
a:link {color:#0000FF;text-decoration:none;} /* 蓝色 */
a:visited {color:#778899;text-decoration:none;} /* 灰色 */
a:hover {color:#000000;text-decoration:none;} /* 黑色 */
a:active {color:#FF704D;text-decoration:none;}

/* 表格样式 */
table {border-collapse:collapse;}
table,th,td {border:1px solid black;}
th {width:100%;height:50px;}
```

则在每个php脚本前加入以下代码：

```
<head>
<link rel="stylesheet" type="text/css" href="private.css">
</head>
<body>
<ul class="nav_vertical">
  	<li><a class="active" href="loginsucc.php">主页</a></li>
  	<li><a href="databaseQuery_Ano.php">检索</a></li>
  	<li><a href="batch_submit_data.php">提交数据</a></li>
	<li><a href="#">BLAST</a></li>
	<li><a href="help.html">帮助</a></li>
	<li><a href="logout.php">退出</a></li>
</ul>
<div style="margin-left:10%; padding:10px; height:100%;">
<!-- 若为检索部分，请添加以下部分代码 -->
<!-- <ul class="nav_horizontal">
  	<li><a class="active" href="databaseQuery_Ano.php">基因结构注释</a></li>
  	<li><a href="databaseQuery_DiffExp.php">基因表达谱</a></li>
</ul> -->
.
.
.
</div>
</body>
```







参考资料：

(1) [PHP+MySQLi实现注册和登陆](https://www.fenxd.com/111.html)

<a name="nat-pro">(2) Pertea M, Kim D, Pertea G M, et al. Transcript-level expression analysis of RNA-seq experiments with HISAT, StringTie and Ballgown[J]. Nature Protocols, 2016, 11(9):1650.</a>

(3) [Analysis pipeline for RNA-seq](https://github.com/Ming-Lian/NGS-analysis/blob/master/RNA-seq.md)

(4) [W3School：SQL 数据类型](http://www.w3school.com.cn/sql/sql_datatypes.asp)


