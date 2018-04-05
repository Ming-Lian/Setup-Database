<a name="content">目录</a>

[PHP+MySQL实战：小鼠与人类lincRNA注释信息数据库](#title)
- [思路](#idea)
- [准备需要写入数据库中的数据](#prepare-data)
	- [下载注释信息文件](#download-gtf)
	- [提取lincRNA部分记录](#extract-lincRNA)
- [将数据写入数据库中](#write-data-into-database)
	- [准备sql脚本](#prepare-sql)
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
1	pseudogene	gene	11869	14412	.	+	.	gene_id "ENSG00000223972"; gene_name "DDX11L1"; gene_source "ensembl_havana"; gene_biotype "pseudogene";
1	processed_transcript	transcript	11869	14409	.	+	.	gene_id "ENSG00000223972"; transcript_id "ENST00000456328"; gene_name "DDX11L1"; gene_source "ensembl_havana"; gene_biotype "pseudogene"; transcript_name "DDX11L1-002"; transcript_source "havana";
1	processed_transcript	exon	11869	12227	.	+	.	gene_id "ENSG00000223972"; transcript_id "ENST00000456328"; exon_number "1"; gene_name "DDX11L1"; gene_source "ensembl_havana"; gene_biotype "pseudogene"; transcript_name "DDX11L1-002"; transcript_source "havana"; exon_id "ENSE00002234944";
```

可以看到，第二列中保存的是基因类型，例如protein_coding，lincRNA，miRNA等，所以针对这一列来提取

用Perl单行命令提取lincRNA注释记录

```
$ perl -ane 'chomp;next if (/^\#/);if(($F[1] =~ /lincRNA/) && ($F[2] =~ /gene/)) {print "$_\n";}' /Path/To/dir/Mus_musculus.GRCm38.91.gtf >/Path/To/dir/lincRNA_GRCm38.91.gtf

$ perl -ane 'chomp;next if (/^\#/);if(($F[1] =~ /lincRNA/) && ($F[2] =~ /gene/)) {print "$_\n";}' /Path/To/dir/Homo_sapiens.GRCh38.91.gtf >/Path/To/dir/lincRNA_GRCh38.91.gtf
```

提取出来的lincRNA注释记录为以下形式：

```
Chrom	Biotype	Element	 Start	 End	Score	Strand	Frame 	Attributes
 1	lincRNA	gene	29554	31109	.	+	.	gene_id "ENSG00000243485"; gene_name "MIR1302-10"; gene_source "ensembl_havana"; gene_biotype "lincRNA";
 1	lincRNA	gene	34554	36081	.	-	.	gene_id "ENSG00000237613"; gene_name "FAM138A"; gene_source "ensembl_havana"; gene_biotype "lincRNA";
 1	lincRNA	gene	89295	133566   .	-	.	gene_id "ENSG00000238009"; gene_name "RP11-34P13.7"; gene_source "havana"; gene_biotype "lincRNA";
 1	lincRNA	gene	89551	91105	.	-	.	gene_id "ENSG00000239945"; gene_name "RP11-34P13.8"; gene_source "havana"; gene_biotype "lincRNA";
```

接下来，将以上格式的信息格式化成以下形式：

```
Chrom	Biotype	Element	 Start	 End	 GeneId		GeneName	GeneSource
 1	lincRNA	gene	29554	31109	ENSG00000243485	MIR1302-10	ensembl_havana
 1	lincRNA	gene	34554	36081	ENSG00000237613	FAM138A		ensembl_havana
 1	lincRNA	gene	89295	133566	ENSG00000238009	RP11-34P13.7	havana
```

利用Perl的正则匹配提取目标字符串进行文本格式化，想了解正则表达式，请点 [这里](http://www.runoob.com/perl/perl-regular-expressions.html)

```
# 正则匹配的正则表达式可能有点复杂
$ perl -ne 'chomp;next if (/^\#/);@linc=split /\t/;if ($linc[8] =~ /gene_id\s\"(ENSG\d+)\";\sgene_name\s\"((\w|-|\.)+)\";\sgene_source\s\"((\w|_)+)\"/){print "$linc[0]\t$linc[1]\t$linc[2]\t$linc[3]\t$linc[4]\t$1\t$2\t$4\n";}' /Path/To/dir/lincRNA_GRCm38.91.gtf  >incRNA_GRCm38.91.gtf.format
```
