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
    
5. 服务器没有应用css
    比较发现assets没有commit去，原因是css所在的文件夹assets被我设置成了全局ignore，传上去就能显示了。跟本地风格还是不太一致，摘事件在研究

6. 安装主题与切换主图
    浏览主题
        [themes.jekyllbootstrap.com](http://themes.jekyllbootstrap.com)
        
    安装主题
    
        $ rake theme:install git="https://github.com/jekyllbootstrap/theme-tom.git"

    切换主题
    
        $ rake theme:switch name="the-program"
 
7. 使用rdiscount作为模版引擎，替换原来默认的Maruku
    * 安装rdiscount, 可能需要sudo权限
        $ gem install rdiscount 
    * 修改_config.xml, 添加下面内容
        markdown: rdiscount

8. 代码的语法高亮
    * 参考文章 [jekyll-code-highlight-and-line-numbers-problem-solved](http://thanpol.as/jekyll/jekyll-code-highlight-and-line-numbers-problem-solved/)
    * 使用`\{\% highlight html%\}` and `\{\% endhighlight \%\}`包含需要语法高亮的代码
    * 使用`\{\% highlight html linenos=table \%\}`添加行号, 这样拷贝时不会把行号一起拷贝
    
9. 添加评论， 使用disqus
    * 去disqus注册
    * 修改_config.yml
    
            comments :
                provider : disqus
                disqus :
                  short_name : lijinlongblog

10. 更换评论系统， 更换为友言
    disqus虽好, 可惜不是很符合中国国情，中国用户用的比较多的微博，QQ在disqus上没有相应支持，因此开率更换评论系统，最后选择了友言。
    
    * 注册友言账号 [http://www.uyan.cc/](http://www.uyan.cc/)
    
    * 注册完成后，网站会给你一段包含你的用户id的javascript代码。记录下里面的userId
    * 修改`_include/JB/comments` 在 case site.JB.comments.provider 的代码段添加下面的代码,`去掉百分号与或括号之间的减号`
    
            {-% when "uyan" %-}
              {-% include JB/comments-providers/uyan %-}
    
    * 添加`_includes/JB/comments-providers/uyan`文件，内容如下
    
            <!-- UY BEGIN -->
            <div id="uyan_frame"></div>
            <script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js?uid=\{\{ site.JB.comments.uyan.user_id \}\}"></script>
            <!-- UY END -->

    * 修改`_config.yml`文件 JB.comments段, 添加JB.comments.uyan段
    
              comments :
                provider : uyan
                uyan :
                  user_id : <your_id>

11. 添加分享功能，使用的是加网
    * 注册账号[http://www.jiathis.com/](http://www.jiathis.com/)与友言使用相同账号即可
    * 修改`_includes/JB/sharing`在case段添加友言的处理， `去掉百分号与或括号之间的减号`
    
              {-% when "jiathis" %-}
                {-% include JB/sharing-provider/jiathis %-}
                
    * 添加`_includes/JB/sharing-provider/jiathis`文件，将在网站上定制好的代码粘贴到这个文件里，然后将uid=后面的id替换为变量\{\{ site.JB.sharing.uyan.user\_id \}\}, 如果不考虑给别人用，直接把代码粘过来就可以了。
    * 修改`_config.yml`文件JB.sharing段
    
            sharing :
              provider : jiathis
              jiathis :
                user_id : <your_id>
 
    * 在对应主题的post.html中添加{-% include JB/sharing %-}
