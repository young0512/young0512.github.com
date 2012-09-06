---
layout: post
title: Jekyll代码高亮
description: 
keywords: Jekyll, Highlight
category: Jekyll
tags: [Jekyll, Highlight]
comment: true
published: true
date: 2012-09-05 14:00:00
remark: Published by Cynthia Young
---

### Jekyll中高亮代码的几种方式

**1. 小片段**

直接用markdown自己的格式，即在需要标记为code的地方缩进一个tab，那么在生成html时便会自动加上code标记`<pre><code>`。
如果是在一句话中标记Code，像上句这样，则只需要用反引号(`)包含相关文字就可以了。

例如：
	使用markdown格式标记的code片段
	<script>
		alert("hello word");
	</script>


**2. 语言相关**

Jekyll通过[Pygments](http://pygments.org/)支持超过[百种语言](http://pygments.org/languages/)的代码高亮，标记非常简单，基本就是highlight+语言名称这种格式，
至于CSS的写法可以参考[这里](https://github.com/mojombo/tpw/blob/master/css/syntax.css)。

例如：

{% assign begin_char = "{%" %}
	{{ begin_char }} highlight php %}  
	<?php
		echo "hello world";
	?>
	{{ begin_char }} endhighlight %}  

显示效果

{% highlight php %}
<?php
	echo "hello world";
?>
{% endhighlight %}  


>注 : [Pygments完整安装说明](https://github.com/mojombo/jekyll/wiki/install)  
>\#Ubuntu下安装pygments  
>sudo apt-get install python-pygments  



**3. gist嵌入**

不用插件的话可以直接用官方gist嵌入方法，如果用[这个插件](https://gist.github.com/1027674)的话则仅需要写上gist+gist id即可，更加方便。



**4. CSS控制**

推荐twitter的开源项目[Bootstrap](http://twitter.github.com/bootstrap/)，利用它外加[Prettyprint](http://code.google.com/p/google-code-prettify/)可以方便快速的显示高亮代码。



###备注

本博客的代码高亮主要采用了方式1和方式2：

* 非语言相关的内容片段中采用了缩进一个tab或者反引号(`)包含的方式高亮代码；
* 在语言相关的代码段中采用Pygments方式高亮代码。

















