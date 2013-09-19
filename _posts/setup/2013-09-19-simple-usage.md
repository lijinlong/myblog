---
layout: post
title: "simple usage"
description: ""
category: setup
tags: [intro, beginner, jekyll, tutorial]
---
{% include JB/setup %}

## 安装

1. 安装ruby, gem
2. 安装jekyll
       
        $ gem install jekyll
       
3. clone jekyll-bootstrap

        $ git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com
        $ cd USERNAME.github.com
        $ git remote set-url origin git@github.com:USERNAME/USERNAME.github.com.git
        $ git push origin master
      
4. 修改配置文件_config.yml

        # Themes are encouraged to use these universal variables 
        # so be sure to set them if your theme uses them.
        #
        title : Jekyll Bootstrap
        tagline: Site Tagline
        author :
          name : Name Lastname
          email : blah@email.test
          github : username
          twitter : username
5. 启动本地服务器
 
      $ cd USERNAME.github.com
      $ jekyll --server
      
## 基本使用

1. 添加页面

        rake page name="about.md"
        rake page name="pages/about.md"
        rake page name="pages/about"
        
2. 添加post

        rake post title="Hello world"
        
3. 在导航中添加链接About
    修改about.md的Front-matter，添加group: navigation
        
        ---
        layout: page
        title: "About"
        description: ""
        header: About
        group: navigation
        ---

4. 在文章中出现的小于号(&lt;)大于号(&gt;)和and符号(&amp;)
    使用转移字符
    
    | 符号 | 十进制 | 转义字符 |
    | --- | --- | --- |
    | &amp; | &#38; | &amp;amp; |
    | &lt; | &#60; | &amp;lt; |
    | &gt; | &#62; | &amp;gt; |
    
 
   