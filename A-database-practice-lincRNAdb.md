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
Chrom	Resource	Feature	 Start	 End	Score	Strand	Frame 	Attributes
1       havana  	gene    3073253 3074322 .       +       .       gene_id "ENSMUSG00000102693"; gene_version "1"; gene_name "4933401J01Rik"; gene_source "havana"; gene_biotype "TEC"; havana_gene "OTTMUSG00000049935"; havana_gene_version "1";
1       havana  	transcript      3073253 3074322 .       +       .       gene_id "ENSMUSG00000102693"; gene_version "1"; transcript_id "ENSMUST00000193812"; transcript_version "1"; gene_name "4933401J01Rik"; gene_source "havana"; gene_biotype "TEC"; havana_gene "OTTMUSG00000049935"; havana_gene_version "1"; transcript_name "4933401J01Rik-201"; transcript_source "havana"; transcript_biotype "TEC"; havana_transcript "OTTMUST00000127109"; havana_transcript_version "1"; tag "basic"; transcript_support_level "NA";
1       havana  	exon    3073253 3074322 .       +       .       gene_id "ENSMUSG00000102693"; gene_version "1"; transcript_id "ENSMUST00000193812"; transcript_version "1"; exon_number "1"; gene_name "4933401J01Rik"; gene_source "havana"; gene_biotype "TEC"; havana_gene "OTTMUSG00000049935"; havana_gene_version "1"; transcript_name "4933401J01Rik-201"; transcript_source "havana"; transcript_biotype "TEC"; havana_transcript "OTTMUST00000127109"; havana_transcript_version "1"; exon_id "ENSMUSE00001343744"; exon_version "1"; tag "basic"; transcript_support_level "NA";

```

可以看到，最后一列的**gene_biotype**中保存的是基因类型，例如protein_coding，lincRNA，miRNA等，所以针对这一列来提取

用Perl单行命令提取lincRNA注释记录

```
$ perl -ne 'chomp;next if (/^\#/);@gtf=split /\t/;if(($gtf[2] =~ /gene/) && ($gtf[8] =~ /gene_biotype\s\"lincRNA\"/)) {print "$_\n";}' /Path/To/dir/Mus_musculus.GRCm38.91.gtf >/Path/To/dir/lincRNA_GRCm38.91.gtf
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
Chrom	Biotype	Feature	 	Start	 End	 GeneId		GeneName	TranscriptId	TranscriptName	ExonNumber
 1	lincRNA	gene		29554	31109	ENSG00000243485	MIR1302-2HG	-	-	-
 1	lincRNA	transcript	29554	31097	ENSG00000243485	MIR1302-2HG	ENST00000473358		MIR1302-2HG-202	-
 1	lincRNA	exon		29554	30039	ENSG00000243485	MIR1302-2HG	ENST00000473358		MIR1302-2HG-202	1
```

利用Perl的正则匹配提取目标字符串进行文本格式化，想了解正则表达式，请点 [这里](http://www.runoob.com/perl/perl-regular-expressions.html)

```
# 正则匹配的正则表达式可能有点复杂
$ perl -ne 'chomp;next if (/^\#/);@linc=split /\t/;if ($linc[8] =~ /gene_id\s\"(\w+)\";.{18}\sgene_name\s\"((\w|-|\.)+)\";\sgene_source\s\"((\w|_)+)\"/){print "$linc[0]\tlincRNA\t$linc[2]\t$linc[3]\t$linc[4]\t$1\t$2\t$4\n";}' /Path/To/dir/lincRNA_GRCm38.91.gtf 
```
