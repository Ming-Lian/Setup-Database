[toc]

<p align="right">——该笔记由小组成员周振钓同学提供</p>

# 获得FASTA格式的序列
## 利用Gene stable ID批量下载序列
第一次写作业时，在mysql数据库中添加了lncRNA的位置信息、ID等。这次从之前建立数据得到的`lincRNA_GRCh38.91.gtf.format`和`lincRNA_GRCm38.91.gtf.format`文件中提取`Gene stable ID`，批量下载FASTA格式的序列信息。
## 得到lnRNA的ID
先以`lincRNA_GRCh38.91.gtf.format`（人类）为例
1. 通过`vi lincRNA_GRCh38.91.gtf.format`查看文件

实例如下，第6列的就是基因ID（ENSG00000243485开始）
```
1       lincRNA gene    29554   31109   ENSG00000243485 MIR1302-2HG     -       -
1       lincRNA transcript      29554   31097   ENSG00000243485 MIR1302-2HG     ENST00000473358 -
1       lincRNA exon    29554   30039   ENSG00000243485 MIR1302-2HG     ENST00000473358 1
1       lincRNA exon    30564   30667   ENSG00000243485 MIR1302-2HG     ENST00000473358 2
1       lincRNA exon    30976   31097   ENSG00000243485 MIR1302-2HG     ENST00000473358 3
1       lincRNA transcript      30267   31109   ENSG00000243485 MIR1302-2HG     ENST00000469289 -
1       lincRNA exon    30267   30667   ENSG00000243485 MIR1302-2HG     ENST00000469289 1
1       lincRNA exon    30976   31109   ENSG00000243485 MIR1302-2HG     ENST00000469289 2
1       lincRNA gene    34554   36081   ENSG00000237613 FAM138A -       -
1       lincRNA transcript      34554   36081   ENSG00000237613 FAM138A ENST00000417324 -
1       lincRNA exon    35721   36081   ENSG00000237613 FAM138A ENST00000417324 1
1       lincRNA exon    35277   35481   ENSG00000237613 FAM138A ENST00000417324 2
1       lincRNA exon    34554   35174   ENSG00000237613 FAM138A ENST00000417324 3
1       lincRNA transcript      35245   36073   ENSG00000237613 FAM138A ENST00000461467 -
1       lincRNA exon    35721   36073   ENSG00000237613 FAM138A ENST00000461467 1
1       lincRNA exon    35245   35481   ENSG00000237613 FAM138A ENST00000461467 2
1       lincRNA gene    89295   133723  ENSG00000238009 AL627309.1      -       -
1       lincRNA transcript      89295   120932  ENSG00000238009 AL627309.1      ENST00000466430 -
1       lincRNA exon    120775  120932  ENSG00000238009 AL627309.1      ENST00000466430 1
1       lincRNA exon    112700  112804  ENSG00000238009 AL627309.1      ENST00000466430 2
1       lincRNA exon    92091   92240   ENSG00000238009 AL627309.1      ENST00000466430 3
1       lincRNA exon    89295   91629   ENSG00000238009 AL627309.1      ENST00000466430 4

```
2.用`cut -f 6  lincRNA_GRCh38.91.gtf.format|sort|uniq > out`命令截取第6列ID信息，并输出到out文件中

3.打开ensembl的[biomart](http://asia.ensembl.org/biomart/martview/274417045864fe1b2b7636ae8c6c67d8)，按照图片调整参数，最后上传ID信息文件即可。

==（由于全基因组数据太大，所以我在REGION中只选择了x染色体）==
![image](https://github.com/zzddsg/population-genetics/blob/master/1527257019(1).jpg?raw=true)

![image](https://github.com/zzddsg/population-genetics/blob/master/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20180525220421.png?raw=true)

![image](https://github.com/zzddsg/population-genetics/blob/master/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20180525221212.png?raw=true)

4.点击`results`,跳转页面点GO生成FASTA序列。

5.小鼠数据如法炮制，只是在第一张照片处把Human参数改成Mouse。

# 构建BLAST索引
## 删除课堂教学用的数据
每人配额只有2G，所以清空一下磁盘，方便做作业

```
#查看文件大小
du -h
#删除文件夹
rm -rf filename
-r ：循环删掉文件夹中的文件
-f ：删除文件时不提示
```

## 构建BLAST索引
在biomart下载完FASTA格式序列后，会得到`mart_export.txt`文件，将其导入服务器。


调用BLAST命令构建索引

```
makeblastdb -in /opt/lampp/htdocs/student2018/201728016715029/mart_export.txt -input_type fasta -dbtype nucl -title lncRNA_h -parse_seqids -out lncRNA_h -logfile File_h
```
参数介绍

- -in:FASTA文件位置
- -input_type:输入格式
- -dbtype:数据库格式
- -title:索引名称
- -parse_seqids:不知道啥意思
- -out:输出文件名（title感觉没啥用，最后blast调用索引都是调用输出文件）
- -logfile:软件日志

命令完成，打开软件日志File_h查看：

```
Building a new DB, current time: 05/26/2018 15:17:13
New DB name:   /home/201728016715029/blastdb/lncRNA_h
New DB title:  lncRNA_h
Sequence type: Nucleotide
Keep MBits: T
Maximum file size: 1000000000B
Adding sequences from FASTA; added 246 sequences in 0.00809789 seconds.

```

成功！

# 搭建BLAST网页
## 表单部分

```
<html>
<head>
<meta charset = 'utf-8'>
</head>
<body>
<form action="localblast1.php" method="post">
  <p>添加你的序列<br></p>

  <textarea name="query" >>ENSG00000226530|ENST00000418775
CACGCTCCTCCAGGGCGCAGAGAGCATCAAAGCATTATCACCGACCCAACACACACCGGC
GCTGCGTAGTTTCTGCAGCAGGTGTGGGGTGAAGAGTTGCCACACAGCTGTCTGACACCG
GGTGTACGGCGTCACCCCTCACTTCCTGGACAAGGCAACATTTTCCTAAAGGTCAAAGGA
GAAAAATCCTATCATCTGAGGTGCTGAACAAAGAATTCAATAAATTCAATCAATCTGATG
CACTCTGCAGTCGTTCTAAGTTCTTGTCTACTAAAATCCTCGACTACTACACATCCACAA
TCGCTTCTGTGAAACTCTTGGGACCAAATGGTTCAGAATTTGTCTCCCATACATACATAA
TATCTATGTCAAGGCCTGTAGTAATACTGTGGAATCTAATACAATGATATTTCTGTAGTG
AAACATACAAATGTTCACTCCAAGATCTAAAGATTACAAATAGCATCATGTCAGTTCAAA
TCAGGTTTTATGGATAAATGAGTCAGGAAAAGTATGTTTTTCAAAGCATTTTGAATGTTA
GACTTGTGCACAAGGGATTGTGAGTCTGAATCATATTTCCGTCAAC</textarea>
  <input type="submit" value="Submit">

</form>
</body>
</html>
```
## 将结果打印到浏览器上
思路是这样的：
1. 收集表单内容（即FASTA序列）
2. 传到`localblast.php`脚本中
3. 将FASTA写入`query.fa`文件中
4. 调用system命令，调用服务器的BLAST对`query.fa`文件中的序列进行序列比对

代码：（比较乱）

```
<html>
<head>
<!--
// Created on: 20180527
// Created by: Simon Chou
// Modified on: 
// Modified by: 
-->
        <title>Blast Search</title>
</head>
<body>
<?php
$BLASTDB="/home/201728016715029/blastdb/index";

$qfile = "/opt/lampp/htdocs/student2018/201728016715029/query.fa";
$out = "/opt/lampp/htdocs/student2018/201728016715029/outblast.txt";
if(!($fp=fopen($qfile, "w"))){
        print"cannot open file to write";
}
$query = trim($_POST['query']);
fputs($fp, "$query");
print"<pre>";
//$fp=popen("/home/Ben/software/blast/ncbi-blast-2.6.0+/bin/blastp -query $qfile -db $BLASTDB/dbdownload", "r");
//echo "这是在人类索引中的结果：\n";
print("system('/home/Ben/software/blast/ncbi-blast-2.6.0+/bin/blastn -query query.fa -db /home/201728016715029/blastdb/index/lncRNA_h -html -evalue 1  -num_descriptions 10 -num_alignments 10')");
system('/home/Ben/software/blast/ncbi-blast-2.6.0+/bin/blastn -query query.fa -db /home/201728016715029/blastdb/index/lncRNA_h -html -evalue 1  -num_descriptions 10 -num_alignments 10')

//echo "这是在小鼠索引中的结果：\n";

//print("system('/home/Ben/software/blast/ncbi-blast-2.6.0+/bin/blastn -query query.fa -db /home/201728016715029/blastdb/index/lncRNA_m -html -evalue 1  -num_descriptions 10 -num_alignments 10')");
//system('/home/Ben/software/blast/ncbi-blast-2.6.0+/bin/blastn -query query.fa -db /home/201728016715029/blastdb/index/lncRNA_m -html -evalue 1  -num_descriptions 10 -num_alignments 10')
//passthru("/home/Ben/software/blast/ncbi-blast-2.6.0+/bin/blastp -query $qfile -db $BLASTDB/dbdownload -html -evalue 1  -num_descriptions 10 -num_alignments 10");
/*
while(!feof($fp)){
        $line = fgets($fp, 1024);
        print("$line");
}

pclose($fp);
*/
//print"</pre>";
?>
</body>
</html>

```
