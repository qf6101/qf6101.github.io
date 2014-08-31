---
layout: post
category : gitpage
tagline: "Supporting tagline"
tags : [gitpage, jekyll, jekyll-bootstrap]
---
{% include JB/setup %}

##Introduction

本文简要描述了如何利用jekyll-bootstrap搭建一个基于gitpage (github page)的个人站点，搭建的环境是ubuntu。

##Key steps

(1) 按照[github pages](https://pages.github.com/)官网说明建立一个github托管的git repository。尽量选择user site，而不是project site，否则jekyll-bootstrap可能会产生BASE_PATH设置导致的路径不一致问题。

(2) 安装[jekyll](http://jekyllcn.com/)，官网说明非常详细。须预装下nodejs (jekyll依赖它)和rake (jekyll-bootstrap依赖它)。

(3) 在shell中clone一个jekyll-bootstrap项目，并安装一个模板 (本文选择the-program)，命令如下。


    git clone https://github.com/plusjade/jekyll-bootstrap.git  
    rake theme:install git="https://github.com/jekyllbootstrap/theme-the-program.git"  
    find . -name ".git" | xargs rm -Rf    #删除jekyll-bootstrap的git配置


(4) 根据个人需要，修改\$project_root\$/_config.yml、\$project_root\$/_includes/themes/the-program/default.html等页面中的内容。详细说明参见[jekyll-bootstrap](http://jekyllbootstrap.com/)官网。

(5) 在jekyll serve或者sublime的markdown preview插件中预览下效果，最后把项目发布到早先建立的git repository中。