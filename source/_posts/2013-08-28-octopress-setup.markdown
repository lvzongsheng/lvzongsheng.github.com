---
layout: post
title: "octopress_setup"
date: 2013-08-28 23:34
comments: true
keywords: Octopress
description: Octopress 安装 配置
categories: 
---
接触Github有一段时间了，知道Octopress也有一段时间，只是一直没有把它们整合，搭建一个自己的博客。最近自己过得过于混乱，想有个自己的博客来记录生活上的点点滴滴，所以就动手了……
##1 Octopress的安装
非常感谢室友biaobiaoqi的分享，使自己在Octopress的安装上没有走弯路。<br/>
这里是他的博文链接，对于想安装和定制Octopress的童鞋非常有指导作用。<br/>
[在github上搭建octopress博客 Mac](http://biaobiaoqi.me/blog/2013/03/21/building-octopress-in-github-mac/)和[定制Octopress](http://biaobiaoqi.me/blog/2013/07/10/decorate-octopress/)<br/>
朋友可以参考以上博文来安装和定制Octopress
<!-- more -->
##2 Octopress的定制
###2.1 图片相册
在个人主页中，我针对自己对摄影的爱好，专门配置了图片相册。我采用的是[FancyBox](http://fancyapps.com/fancybox/)的插件。同时也有人根据FancyBox定制了Octopress的第三方[插件](http://tritarget.org/blog/2012/05/07/integrating-photos-into-octopress-using-fancybox-and-plugin/)
    1 Download the fancybox source to /source/fancybox
    2 Add fancybox jquery to _include/custom/head.html
    <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.7/jquery.min.js"></script>
    <link rel="stylesheet" href="/fancybox/jquery.fancybox.css" type="text/css" media="screen" />
    <script type="text/javascript" src="/fancybox/jquery.fancybox.pack.js"></script>

    <script>
    $(document).ready(function() {
        $('.fancybox').fancybox();
    });
    </script>
    3 Add html code in your own page like this:
    <a href="/images/about/pic2.jpg" class="fancybox" rel="gallery" title="摄于杭州钱塘江大桥"><img src="/images/about/pic2_m.jpg" /></a>
    <a href="/images/about/pic4.jpg" class="fancybox" rel="gallery" title="摄于杭州云栖竹径"><img src="/images/about/pic4_m.jpg" /></a>
    <a href="/images/about/pic5.jpg" class="fancybox" rel="gallery" title="摄于绍兴孔乙己酒家"><img src="/images/about/pic5_m.jpg" /></a>
    <a href="/images/about/pic4.jpg" class="fancybox" rel="gallery" title="摄于杭州云栖竹径"><img src="/images/about/pic4_m.jpg" /></a>
FancyBox除了图片之外，还支持视频等其他多媒体形式，要得到更多信息，可以参见官网。
###2.2 搜索的优化
为了使搜索引擎更好的找到我们的文章，我参照[Octopress中的搜索引擎优化](http://dinever.com/blog/2013/01/23/zhuan-zai-fan-yi-octopresszhong-de-seo-youhua/)为每篇元素添加meta元素，优化搜索
当你使用rake new_post[title]生成文章时，layout、title、date、comments这些关键属性会自动生成，但如果你再为它增加两个关键词keywords和description，Octopress便会在rake generate时自动为这篇文章加上meta元素。
    layout: post
    title: "octopress_setup"
    date: 2013-08-28 23:34
    comments: true
    keywords: Octopress
    description: Octopress 安装 配置
那么在生产页面时，就会有
    <meta name="description" content="Octopress 安装 配置">
    <meta name="keywords" content="Octopress">
现在我们还要为首页添加meta标签。首先在_config.yml中加入keywords和description关键字
    title: Coco
    subtitle: A blog for Recording.
    author: Coco
    simple_search: http://google.com/search
    description: cocolighter algorithms data structure computer
    keywords: cocolighter algorithms data structure computer
然后在source/_includes/head.html做如下修改    
	{\% if page.keywords \%}<meta name="keywords" content="{{ page.keywords }}">{\% else if site.keywords\%}<meta name="keywords" content="{{ site.keywords }}">{\% endif \%}



