---
layout: post
category : gitpage
tagline: "Supporting tagline"
tags : [github page, jekyll, jekyll-bootstrap]
---
{% include JB/setup %}

##Introduction

本文简要描述了如何利用jekyll-bootstrap搭建一个基于github page的个人站点，搭建的环境是ubuntu。

##Basic Concepts

> git:	类似cvs、svn的版本控制软件。
 
> github:	git的托管网站。

> github page:	github提供的静态网站服务，采用jekyll解析用户提交的源码。

> jekyll:	将符合自身约定的源码(e.g., markdown language)转成静态网站的ruby工具。

> jekyll-bootstrap:	jekyll的模板框架，包含一些现成的模板，也可以基于它自定义模板。

> mathjax：解析latex等符号的javascript库，通常请求它的cdn服务器进行动态解析。

##Motivations

(1) 为什么使用github page空间？只支持静态网页，适合作为博客空间。但相比传统博客服务 (e.g., csdn blog)，又更具灵活性。

(2) 为什么使用jekyll？github page支持jekyll; markdown有那么点类似于latex，把内容与格式分开，很省心。

(3) 为什么使用mathjax？支持latex语法；非图片格式的公式不易被拷贝。

##Key steps

(1) 按照[github pages](https://pages.github.com/)官网说明建立一个github托管的git repository。尽量选择user site，而不是project site，否则jekyll-bootstrap可能会产生BASE_PATH设置导致的路径不一致问题。

(2) 安装[jekyll](http://jekyllcn.com/)，官网说明非常详细。须预装下nodejs (jekyll依赖它)和rake (jekyll-bootstrap依赖它)。

(3) 在shell中clone一个jekyll-bootstrap项目，并安装一个模板 (本文选择the-program)，命令如下。


    git clone https://github.com/plusjade/jekyll-bootstrap.git  
    rake theme:install git="https://github.com/jekyllbootstrap/theme-the-program.git"  
    find . -name ".git" | xargs rm -Rf    #删除jekyll-bootstrap的git配置


(4) 根据个人需要，修改\$project_root\$/_config.yml、\$project_root\$/_includes/themes/the-program/default.html等页面中的内容。例如，default.html页面中加入[mathjax](http://docs.mathjax.org/en/latest/start.html)的cdn地址。详细说明参见[jekyll-bootstrap](http://jekyllbootstrap.com/)官网。

(5) 在jekyll serve或者sublime的markdown preview插件(可以使用[made-of-code](https://github.com/kumarnitin/made-of-code-tmbundle) color scheme)中预览下效果，最后把项目发布到早先建立的git repository中。

##Miscellaneous

技术博客其实就是技术日记，即使没有其他人看，坚持写，对自己的技术成长也仍然很有帮助。个人觉得，写技术博客，最好采取下面两种策略。

(1) 把长的内容写短。例如，用简短的篇幅讲清楚一篇论文的思想和方法。

(2) 把短的内容写长。例如，把论文中的晦涩内容一层层解释清楚 (可以阅读相关的源码)。