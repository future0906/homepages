---
title: 从Wordpress切换到Jekyll
date: '2023-11-10T01:39:40+08:00'
author: shane
layout: article
categories:
    - 未分类
key: 2023-11-10-switch-site-to-jekyll
---

之前新用户买的腾讯轻量服务器到期了，续费就算打折也是贵得飞起。在降本增效的大前提下，感觉Wordpress也不是不可替代。而且我文章的量一向不太多，感觉Jekyll应该就够应付了。
本来之前计划是在内网NAS搭一个Wordpress然后套一个CDN的。不过考虑到源站地址泄露的风险，并且QNAP 内置的Wordpress跟其他服务（PHPMyAdmin）是用同一个端口，暴漏出去公网有点风险。再三考虑还是算了，直接S3+CDN一把梭。

吐槽一下，国内似乎能白嫖jekyll build的平台基本都无了，所以直接用github pages做一个分支去白嫖Action免费额度。通过Github Action 构建Jekyll，可以参考这篇文章[^1]。上传到COS的可以参考这篇[^2]其实都挺简单。
最后把腾讯的CDN配一下，HTTPS、自动刷新之类的就OK，大半天时间。记得配自动刷新的时候去掉日志采集，不然费用蹭蹭。在目前的流量下，只需要买个5G的COS储量包，10G左右的回流流量和CDN流量，总价一年40左右，算还可以接受。

唯一遗憾的是目前国内没有评论服务（多说之类的都跪了）。这样也好，可以安静写自己的东西。


[^1]: [Jekyll site to AWS S3 using GitHub Actions](https://pagertree.com/blog/jekyll-site-to-aws-s3-using-github-actions)
[^2]: [拥抱腾讯云服务：Github Actions+COS，快速搭建你的Wiki文档](https://cloud.tencent.com/developer/article/1545303)
