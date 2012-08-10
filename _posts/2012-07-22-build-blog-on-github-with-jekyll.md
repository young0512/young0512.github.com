---
layout: post
title: 使用 Jekyll 在 Github 上搭建个人博客
description: 
keywords: Jekyll, Github
category: Github
tags: [Jekyll, Github]
comment: true
published: true
date: 2012-07-22 13:30:00
remark: Published by Cynthia Young
---

--- 

首先，我要感谢几篇博文，把我渐渐带入 Jekyll + Github 的世界：

* [像黑客一样写博客——Jekyll入门](http://www.soimort.org/tech-blog/2011/11/19/introduction-to-jekyll_zh.html)

其实类似入门介绍的文章不少，但是Mort博客的设计风格还是挺吸引眼球的（起码我就被深深吸引，一个静态博客居然也可以做得如此的漂亮）。

* [理想的写作环境：git+github+markdown+jekyll](http://www.yangzhiping.com/tech/writing-space.html)

git，不用担心写废；github，备份与远程协作；markdown，不再忧虑排版，专注写作；jekyll，简洁，专注。此博客也秉承排版简洁干净、表达清晰的风格，也促使我开始尝试 Jekyll。

---

Jekyll 是一个使用 Ruby 编写的静态站点生成工具，使用 Liquid 模板渲染引擎，支持 Markdown 和 Textile 标记语言，并且可以为所有以 .html、.markdown、.textile 等扩展名结尾的文件使用YAML配置，内置语法高亮功能。
若想详细了解，可以参考 [jekyll-introduction](/2012/07/22/jekyll-introduction)。

Jekyll 有很多吸引我的地方，比如：

* 基于 git，天然的版本控制能力;
* 基于 markdown，干净利落，方便处理，配合 git，写文章只需要 vim 足矣;
* 直接可放在 github 上，无需担心空间和维护问题;
* 轻量纯静态，方便迁移，无需担心安全问题;
* 可定制性强，只要是静态的就可以。如果在 github 以外的空间存放生成好的站点，添加后端代码也是可行的。


### Jekyll + Github 搭建博客步骤

网上 Jekyll + Github 的搭建教程很多，在此就不一一列举。杂七杂八的参照了一些，也就促成了我今天的这个 Blog。为了理清头绪，温故一下这几天的收获，遂将步骤整理如下，也算是自己的学习笔记吧。

若您正考虑用Jekyll作为Blog系统，也可以参考一下[Jekyll-Bootstrap](http://jekyllbootstrap.com/)，上面有从入门到精通的教程。


#### 1. 注册 Github 并安装配置 Git

1）若您没有 Github 账号，请到 [Github](https://github.com/) 注册。

2）请确保安装 Git，并进行了正确的配置。详见：[安装配置Git](https://help.github.com/articles/set-up-git)  

	$ git config --global user.name "Your Name Here"
	$ git config --global user.email "your_email@youremail.com"


#### 2. 创建新的 Github Repository，作为 Blog 仓库

在 Github 上创建一个新的 Repository，名字为USERNAME.github.com, USERNAME为你的 Github 用户名。


#### 3. 安装 Jekyll-Bootstrap，初始化 Blog

在你想放置或部署 Blog 的文件目录，执行下面命令：

	$ git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com
	$ cd USERNAME.github.com
	$ git remote set-url origin git@github.com:USERNAME/USERNAME.github.com.git
	$ git push origin master
	
过一会儿，去访问一下 http://USERNAME.github.com，Github 已经为你生成了一个站点。

#### 4. 写博文

在 Blog 根目录的 _posts 目录里面添加 .html、.markdown 等扩展名的文件，然后根据相应的规则写博文即可。（写博文的时候，需要注意使用 YAML 配置）

写好后，使用相应的 git 命令，提交文件即可。不一会儿，就能在你的站点看到这篇博文喽。


### 后记

若你想在自己的电脑上也可以提前预览访问的话，需要安装配置相应的环境，然后运行下 jekyll --server，在浏览器上访问 http://localhost:4000 就可以了。

** 特点 **

1）生成的 Blog 全为静态页面，对服务器没有数据库要求，速度快，维护也方便。

2）不支持原生评论系统，可以选择Disqus作为评论系统，在jekyll-bootstrap的 _config.yml 配置中可以看到 comments 的配置，可支持 disqus，livefyre，intensedebate，facebook 等。

3）写新文章时可以随便找个编辑器，Vim，文本等都可以，模版语言是 Markdown，简单清爽。

4）可以用 git 来管理整个 Blog 的进化历程。




















