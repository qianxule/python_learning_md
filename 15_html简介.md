@[toc]
# html 概述和基本结构
## html概述
HTML 是 HyperText Mark-up Language 的首字母简写，意思是超文本标记语言，超文本指的是超链接，标记指的是标签，是一种用来制作网页的语言，这种语言由一个个的标签组成，用这种语言制作的文件保存的是一个文本文件，文件的扩展名为 html 或者 htm，一个 html 文件就是一个网页，html 文件用编辑器打开显示的是文本，可以用文本的方式编辑它，如果用浏览器打开，浏览器会按照标签描述内容将文件渲染成网页，显示的网页可以从一个网页链接跳转到另外一个网页。

## html的基本结构

```xml
<!doctype html>
<html>
	<head>
		<meta charset="utf-8">
		<title>网页标题</title>
	</head>
	<body>
		网页内容
	</body>
</html>
```
第一行是文档声明，第二行“\<html>”标签和最后一行“\</html>”定义 html 文档的整体，“\<html>”标签中的‘lang=“en”’定义网页的语言为英文，定义成中文是'lang="zh- CN"',不定义也没什么影响，它一般作为分析统计用。 “\<head>”标签和“\<body>”标签是它的第一层子元素，“\<head>”标签里面负责对网页进行一些设置以及定义标题，设置包括定义网页的编码格式，外链 css 样式文件和 javascript 文件等，设置的内容不会显示在网页上，标题的内容会显示在标题栏，“\<body>”内编写网页上显示的内容。

## HTML 文档类型
目前常用的两种文档类型是 xhtml 1.0 和 html5

### xhtml 1.0 （html4）
xhtml 1.0 是 html5 之前的一个常用的版本，目前许多网站仍然使用此版本。

创建实际上使用html：xml即可


![[image/ee57265325e38936669c407d47d8e412_MD5.png]]

```xml
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
    <title> xhtml 1.0 文档类型 </title>
</head>
<body>

</body>
</html>
```

### html5
相同的使用html：5即可

```xml
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title> html5文档类型 </title>
</head>
<body>
	html5
</body>
</html>
```


## 两种文档的区别
1、文档声明和编码声明
2、html5 新增了标签元素以及元素属性


## html 注释
html 文档代码中可以插入注释，注释是对代码的说明和解释，注释的内容不会显示在页面上，html 代码中插入注释的方法是：

```xml
<!-- 这是一段注释 -->
```

# html标签介绍
## html 标题标签
通过\<h1>、\<h2>、\<h3>、\<h4>、\<h5>、\<h6>,标签可以在网页上定义 6 种级别的标题。6 种级别的标题表示文档的 6 级目录层级关系，比如说： \<h1>用作主标题，其后是 \<h2>，再其次是 \<h3>，以此类推。搜索引擎会使用标题将网页的结构和内容编制索引，所以网页上使用标题是很重要的。

```xml
<h1>这是一级标题</h1>
<h2>这是二级标题</h2>
<h3>这是三级标题</h3>
<h4>这是四级标题</h4>
<h5>这是五级标题</h5>
<h6>这是六级标题</h6>
```

显示结果：
![[image/83b769bee9c1547b6ae019953d8a5a20_MD5.png]]

## html 段落标签、换行标签与字符实体
### html 段落标签
\<p>标签定义一个文本段落，一个段落含有默认的上下间距，段落之间会用这种默认间距隔开

```xml
<!doctype html>
<html>
<head>
<meta charset="utf-8">
<title>无标题文档</title>
</head>
	<p>HTML是 HyperText Mark-up Language 的首字母简写，意思是超文本标记语言，超文本指的是超链接，标记指的是标签，是一种用来制作网页的语言，这种语言由一个个的标签组成，用这种语言制作的文件保存的是一个文本文件，文件的扩展名为html或者htm。</p>

    <p>一个html文件就是一个网页，html文件用编辑器打开显示的是文本，可以用文本的方式编辑它，如果用浏览器打开，浏览器会按照标签描述内容将文件渲染成网页，显示的网页可以从一个网页链接跳转到另外一个网页。</p>
<body>
</body>
</html>

```
显示结果：
![[image/13663de84195d44ae727f03b767d9656_MD5.png]]

### html 换行标签
代码中成段的文字，直接在代码中回车换行，在渲染成网页时候不认这种换行，如果真想换行，可以在代码的段落中插入\<br >来强制回车换行

这边自己尝试吧，懒得写OxO~

### html 字符实体
代码中成段的文字，如果文字间想空多个空格，在代码中空多个空格，在渲染成网页时只会显示一个空格，如果想显示多个空格，可以使用空格的字符实体，类似之前所说的特殊符号要用特殊的形式来展示。

|符号|说明  |
|--|--|
|&nbsp；  | 空格，如果文中只需要一个空格，直接打空格即可，但是如果需要多个空格，需要使用这个多次 |
|&lt；|<|
|&rt；|>|

## html 块标签、含样式的标签
### html 块标签
\<div> 标签 块元素，表示一块内容，没有具体的语义。
\<span> 标签 行内元素，表示一行中的一小段内容，没有具体的语义。

如果说二者的区别的话：
div存在换行？ span不存在换行，但是实际上绝大多数都使用div

```xml
<!doctype html>
<html>
<head>
<meta charset="utf-8">
<title>无标题文档</title>
</head>

<body>
	<div>
		<div>我是大牛</div>
		<div>我是大牛</div>
	</div>
	<span>我是程序员</span>
	<span>我是程序员</span>
	
	<em>我是em标签</em>
	<i>我是i标签</i>
	<b>我是b标签</b>
	<strong>我是strong标签</strong>
</body>
</html>
```

![[image/b4bf62fd6fe70f60d8071e5ac4407760_MD5.png]]
### 含样式和语义的标签
\<em> 标签 行内元素，表示语气中的强调词，变为斜体
\<i> 标签 行内元素，表示专业词汇 斜体
\<b> 标签 行内元素，表示文档中的关键字或者产品名 我是粗体
\<strong> 标签 行内元素，表示非常重要的内容 我是粗体

具体效果见上


### 语义化的标签
ul、li 标签是表示列表


```xml
<!--这里是样式标签，ol是有序列表，ul是小黑点设置列表-->
<ol>
	<li>第一行</li>
	<li>第二行</li>
	<li>哈哈</li>
	<ol>
		<li>第一行</li>
		<li>第二行</li>
		<li>哈哈</li>
	</ol>
</ol>


<ul>
	<li>第一行</li>
	<li>第二行</li>
	<ul>
		<li>第一行</li>
		<li>第二行</li>
		<ul>
			<li>第一行</li>
			<li>第二行</li>
		</ul>
	</ul>
</ul>
```


![[image/9ba902680e23a10e1dbb0b6373dcdd19_MD5.png]]
## html 图像标签
\<img>标签可以在网页上插入一张图片，它是独立使用的标签，它的常用属有：

1. src 属性 定义图片的引用地址
2. alt 属性 定义图片加载失败时显示的文字，搜索引擎会使用这个文字收录图片、盲人读屏软件会读取这个文字让盲人识别图片，所以此属性非常重要。

```xml
<img src="./images/下载.jfif" alt="产品图片" />
```


![[image/04c1529e6ed0f7c8e4ef3d2c425e70e9_MD5.png]]

### 相对路径补充：
 “ ./ ” 表示当前文件所在目录下，比如：“./pic.jpg” 表示当前目录下的 pic.jpg 的
图片，这个使用时==可以省略==。
“ ../ ” 表示当前文件所在目录下的上一级目录，比如：“../images/pic.jpg” 表示
当前目录下的上一级目录下的 images 文件夹中的 pic.jpg 的图片


## html 链接标签
\<a>标签可以在网页上定义一个链接地址，它的常用属性有：

1. href 属性 定义跳转的地址
2. title 属性 定义鼠标悬停时弹出的提示文字框
3. target 属性 定义链接窗口打开的位置
– target="_self" 缺省值，新页面替换原来的页面，在原来位置打开
– target="_blank" 新页面会在新开的一个浏览器窗口打开

```xml
<a href="#">我在哪里</a> <!--  # 表示链接到页面顶部   -->
```
#号实际上代表着页面顶部


## html 列表
### 有序列表
实际上就是上文的ol ul


### 无序列表
\<ul>、\<li>配合使用来实现

```xml
<ul>
    <li><a href="#">新闻标题一</a></li>
    <li><a href="#">新闻标题二</a></li>
    <li><a href="#">新闻标题三</a></li>
</ul>
```

![[image/69397690374232f781de0df0b82798fe_MD5.png]]

### 定义列表
\<dl>标签表示列表的整体。\<dt>标签定义术语的题目。\<dd>标签是术语的解释。

```xml
<h3>前端三大块</h3>
<dl>
    <dt>html</dt>
    <dd>负责页面的结构</dd>

    <dt>css</dt>
    <dd>负责页面的表现</dd>

    <dt>javascript</dt>
    <dd>负责页面的行为</dd>

</dl>
```
![[image/0d6a4e255add7a4f6d027babb4a9b9f3_MD5.png]]

## html 表格
\<table>标签：声明一个表格，它的常用属性如下：
1. border 属性 定义表格的边框，设置值是数值
2. cellpadding 属性 定义单元格内容与边框的距离，设置值是数值
3. cellspacing 属性 定义单元格与单元格之间的距离，设置值是数值
4. align 属性 设置整体表格相对于浏览器窗口的水平对齐方式,设置值有：left |
center | right

\<tr>标签：定义表格中的一行
\<td>和\<th>标签：定义一行中的一个单元格，td 代表普通单元格，th 表示表头
单元格，它们的常用属性如下：
1. align 设置单元格中内容的水平对齐方式,设置值有：left | center | right • valign设置单元格中内容的垂直对齐方式 top | middle | bottom
2. colspan 设置单元格水平合并，设置值是数值
3. rowspan 设置单元格垂直合并，设置值是数值
\<th>和\<td>标签都是用于表格单元格显示的。不同的是\<th>在单元格中加粗显示。\<th>：定义表格内的表头单元格。此 th 元素内部的文本通常会呈现为粗体。


## 表单
表单用于搜集不同类型的用户输入，表单由不同类型的标签组成，相关标签及属性用法如下：

\<form>标签 定义整体的表单区域
1. action 属性 定义表单数据提交地址
2. method 属性 定义表单提交的方式，一般有“get”方式和“post”方式

\<label>标签 为表单元素定义文字标注

\<input>标签 定义通用的表单元素
type 属性:
1. type="text" 定义单行文本输入框
2. type="password" 定义密码输入框
3. type="radio" 定义单选框
4. type="checkbox" 定义复选框
5. type="file" 定义上传文件
6. type="submit" 定义提交按钮
7. type="reset" 定义重置按钮
8. type="button" 定义一个普通按钮
9. type="image" 定义图片作为提交按钮，用 src 属性定义图片地址
10. type="hidden" 定义一个隐藏的表单域，用来存储值
11. value 属性 定义表单元素的值
12. name 属性 定义表单元素的名称，此名称是提交数据时的键名

 \<textarea>标签 定义多行文本输入框
\<select>标签 定义下拉表单元素
\<option>标签 与<select>标签配合，定义下拉表单元素中的选项

```xml
<form action="./test.html" method="get">
    <p>
        <label>姓名：</label><input type="text" name="username" />
    </p>
    <p>
        <input type="hidden" name="id" value="123456" />
    </p>
    <p>
        <label>密码：</label><input type="password" name="password" />
    </p>
    <p>
        <label>性别：</label>
        <input type="radio" name="gender" value="0" /> 男
        <input type="radio" name="gender" value="1" /> 女
    </p>
    <p>
        <label>爱好：</label>
        <input type="checkbox" name="like" value="sing" /> 唱歌
        <input type="checkbox" name="like" value="run" /> 跑步
        <input type="checkbox" name="like" value="swiming" /> 游泳
    </p>
    <p>
        <label>照片：</label>
        <input type="file" name="person_pic">
    </p>
    <p>
        <label>个人描述：</label>
        <textarea name="about"></textarea>
    </p>
    <p>
        <label>籍贯：</label>
        <select name="site">
            <option value="0">北京</option>
            <option value="1">上海</option>
            <option value="2">广州</option>
            <option value="3">深圳</option>
        </select>
    </p>
    <p>
        <input type="submit" name="" value="提交">
        <!-- input类型为submit定义提交按钮  ![[image/882e49ecb6bb2bd3a4fbe3f7d999d864_MD5.png]]
还可以用图片控件代替submit按钮提交，一般会导致提交两次，不建议使用。如：
     <input type="image" src="xxx.gif">
-->
        <input type="reset" name="" value="重置">
    </p>
</form>
```

![[image/882e49ecb6bb2bd3a4fbe3f7d999d864_MD5.png]]

























