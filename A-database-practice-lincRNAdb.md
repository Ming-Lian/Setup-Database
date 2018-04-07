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
		- []()





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
}

echo "连接成功！\n";

// 预处理及绑定
$stmt=$conn_prepare("INSERT INTO lincRNA_m (chrom,biotype,feature,start,end,geneid,genename,transcriptid,exon) VALUES (?,?,?,?,?,?,?,?,?)") ;
$stmt->bind_param("sssiisssi",$chrom,$biotype,$feature,$start,$end,$geneid,$genename,$transcriptid,$exon);

// 打开文件
$ file=fopen("lincRNA_GRCh38.91.gtf.format",'r') or exit("Unable to open file!");

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
```
