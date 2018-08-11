<a name="content">目录</a>

[AJAX学习笔记](#title)
- [创建 XMLHttpRequest 对象](#create-xmlhttprequest-object)
- [向服务器发送请求](#send-http-request)
- [服务器响应](#response-from-server)
- [onreadystatechange 事件](#onreadystatechange-event)
- [实例1：关键字联想](#example-showhint)
- [实例2：数据库检索](#example-search-database)






<h1 name="title">AJAX学习笔记</h1>

<p align="center"><img src=./picture/ajax-process.gif width=800 /></p>

<a name="create-xmlhttprequest-object"><h2>创建 XMLHttpRequest 对象 [<sup>目录</sup>](#content)</h2></a>

```
var xmlhttp;
if (window.XMLHttpRequest)
{
    //  IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
    xmlhttp=new XMLHttpRequest();
}
else
{
    // IE6, IE5 浏览器执行代码
    xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}
```

<a name="send-http-request"><h2>向服务器发送请求 [<sup>目录</sup>](#content)</h2></a>

```
xmlhttp.open("GET","ajax_info.txt",true);	# 最后一项参数来设定是否异步，true为异步，false为非异步
xmlhttp.send();
```

|方法|描述|
|:---|:---|
|open(method,url,async)|规定请求的类型、URL 以及是否异步处理请求。<br><br>method：请求的类型；GET 或 POST<br>url：文件在服务器上的位置<br>async：true（异步）或 false（同步）|
|send(string)|将请求发送到服务器。<br><br>string：仅用于 POST 请求|

- **GET 请求**

```
xmlhttp.open("GET","/try/ajax/demo_get.php",true);
xmlhttp.send();
```

如果您希望通过 GET 方法发送信息，请向 URL 添加信息：

```
xmlhttp.open("GET","/try/ajax/demo_get2.php?fname=Henry&lname=Ford",true);
xmlhttp.send();
```

- **POST 请求**

```
xmlhttp.open("POST","/try/ajax/demo_post.php",true);
xmlhttp.send();
```

如果需要像 HTML 表单那样 POST 数据，请使用 setRequestHeader() 来添加 HTTP 头。然后在 send() 方法中规定您希望发送的数据：

```
xmlhttp.open("POST","/try/ajax/demo_post2.php",true);
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Henry&lname=Ford");
```

<a name="response-from-server"><h2>服务器响应 [<sup>目录</sup>](#content)</h2></a>

> - responseText：获得字符串形式的响应数据。
> - responseXML：获得 XML 形式的响应数据。

- **responseText 属性**

```
document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
```

- **responseXML 属性**

如果来自服务器的响应是 XML，而且需要作为 XML 对象进行解析，请使用 responseXML 属性：

```
xmlDoc=xmlhttp.responseXML;
txt="";
x=xmlDoc.getElementsByTagName("ARTIST");
for (i=0;i<x.length;i++)
{
    txt=txt + x[i].childNodes[0].nodeValue + "<br>";
}
document.getElementById("myDiv").innerHTML=txt;
```

<a name="onreadystatechange-event"><h2>onreadystatechange 事件 [<sup>目录</sup>](#content)</h2></a>

每当 readyState 改变时，就会触发 onreadystatechange 事件

readyState

> - 0: 请求未初始化
> - 1: 服务器连接已建立
> - 2: 请求已接收
> - 3: 请求处理中
> - 4: 请求已完成，且响应已就绪

status 	200: 

> - "OK"
> - 404: 未找到页面

当 readyState 等于 4 且状态为 200 时，表示响应已就绪：

```
xmlhttp.onreadystatechange=function()
{
    if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
        ...
    }
}
```

<a name="example-showhint"><h2>实例：关键字联想 [<sup>目录</sup>](#content)</h2></a>

```
function showHint(str)
{
  var xmlhttp;
  if (str.length==0)
  { 
    document.getElementById("txtHint").innerHTML="";
    return;
  }
  if (window.XMLHttpRequest)
  {
    // IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
    xmlhttp=new XMLHttpRequest();
  }
  else
  {
    // IE6, IE5 浏览器执行代码
    xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
  xmlhttp.onreadystatechange=function()
  {
    if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
      document.getElementById("txtHint").innerHTML=xmlhttp.responseText;
    }
  }
  xmlhttp.open("GET","/try/ajax/gethint.php?q="+str,true);
  xmlhttp.send();
}
```

用服务器端PHP脚本实现关键词联想

```
<?php
// Fill up array with names
$a[]="Anna";
$a[]="Brittany";
$a[]="Cinderella";
$a[]="Diana";
$a[]="Eva";
$a[]="Fiona";
$a[]="Gunda";
$a[]="Hege";
$a[]="Inga";
$a[]="Johanna";
$a[]="Kitty";
$a[]="Linda";
$a[]="Nina";
$a[]="Ophelia";
$a[]="Petunia";
$a[]="Amanda";
$a[]="Raquel";
$a[]="Cindy";
$a[]="Doris";
$a[]="Eve";
$a[]="Evita";
$a[]="Sunniva";
$a[]="Tove";
$a[]="Unni";
$a[]="Violet";
$a[]="Liza";
$a[]="Elizabeth";
$a[]="Ellen";
$a[]="Wenche";
$a[]="Vicky";

//get the q parameter from URL
$q=$_GET["q"];

//lookup all hints from array if length of q>0
if (strlen($q) > 0)
{
  $hint="";
  for($i=0; $i<count($a); $i++)
  {
	# 忽略大小写，其相同长度的前几个字符进行比较
    if (strtolower($q)==strtolower(substr($a[$i],0,strlen($q))))
    {
      if ($hint=="")
      {
        $hint=$a[$i];	# 若是第一个匹配上的则直接追加
      }
      else
      {
        $hint=$hint." , ".$a[$i];	# 若不是第一个匹配上的，则先添加逗号作为分隔符，再追加
      }
    }
  }
}

// Set output to "no suggestion" if no hint were found
// or to the correct values
if ($hint == "")
{
  $response="no suggestion";
}
else
{
  $response=$hint;
}

//output the response
echo $response;
?>
```
<a name="example-search-database"><h2>实例2：数据库检索 [<sup>目录</sup>](#content)</h2></a>

```
function showCustomer(str)
{
  var xmlhttp;    
  if (str=="")
  {
    document.getElementById("txtHint").innerHTML="";
    return;
  }
  if (window.XMLHttpRequest)
  {
    // IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
    xmlhttp=new XMLHttpRequest();
  }
  else
  {
    // IE6, IE5 浏览器执行代码
    xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
  xmlhttp.onreadystatechange=function()
  {
    if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
      document.getElementById("txtHint").innerHTML=xmlhttp.responseText;
    }
  }
  xmlhttp.open("GET","/try/ajax/getcustomer.php?q="+str,true);
  xmlhttp.send();
}
```

然后可以用PHP脚本处理用户端请求，用`$GET`或`$POST`获取表单数据，用mysqli进行数据库访问

```
<?php

$request=$GET("q");

$conn=new mysqli(localhost,$username,$password,$dbname);

// 检查连接
if($conn->connect_error){
	die("连接失败：".$conn->connect_error);
}

$sql="SELECT * FROM Websites WHERE id = '".$request."'";

$result=$conn->query($sql);

...
```




参考资料：

[菜鸟教程：AJAX](http://www.runoob.com/ajax/ajax-tutorial.html)
