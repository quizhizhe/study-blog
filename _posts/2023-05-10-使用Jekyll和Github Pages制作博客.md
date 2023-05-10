---
title: 使用Jekyll和Github Pages制作博客
author: quizhi
date: 2023-05-09 10:14:00 +0800
categories: [Blogging]
tags: [Jekyll]

---

### 这里介绍的是Github Pages搭配Jekyll的使用

## Github

首先创建一个仓库，仓库名可以是 `username.github.io` 或者任意，如果是第一个，那么就是通过 `username.github.io` 来访问，如果是其他的，这需要或者这样访问 `username.github.io/仓库名` 前面的 `username` 都是你自己github的用户名

然后转到 `Setting>Pages` 设置好。以上详细的可以看[Github Pages](https://docs.github.com/zh/pages/getting-started-with-github-pages/creating-a-github-pages-site)官方文档

## Jekyll

有两种形式让Github pages使用jekyll:
> 1.这也是我使用的方式，直接在仓库里创建一个文件名为 `_config.yml` 的文件，添加以下内容：
> ```yml
> title: Study Blog # 这个是页面的主题
> description: 学习随笔 # 页面的描述
> # 上面这两个都会体现在页面里，自定义修改
>
> theme: jekyll-theme-leap-day # 这个就是用到的主题，把主题名填上即可
> ```
> 上面的主题可以去这里挑 [Jekyll Theme](http://jekyllthemes.org/) 都是可以自定义的
> 填完提交即可，Github Action会自动识别并且构建，完成后访问`username.github.io` 或者 `username.github.io/仓库名` 使用哪个访问取决你仓库名，上面`Github`部分有说明。  
> 详细的官方文档 [使用 Jekyll 向 GitHub Pages 站点添加主题](https://docs.github.com/zh/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll)

> 2.fork对应主题到自己的仓库下，可以自行clone对应主题项目，然后往你创建的仓库提交，相关教程的自行搜索，这里只详细介绍我用的方式

## 自定义

使用了第一种方式的直接在你仓库对应文件位置提交修改后的文档即可生效，添加的文件也一样

比如我这里修改的 `_layouts > default.html` 文件，添加的 `_layouts > home.html` 文件，这些都是与原来主题不一样的文件，这的方式就是比较简洁，仓库没有那么多你不会去动的文件

jekyll文件里的修改，请看官方文档，这里不详细介绍

Jekyll官方文档[中文](https://www.jekyll.com.cn/docs/)，[英文](https://jekyllrb.com/docs/)

## 自定义域名

先在`Setting > Pages`里设置后，然后解析你自定义的域名指向你Pages的地址即可，https生效也许需要一些时间才行

这里是Github Pages的说明文档 [配置 GitHub Pages 站点的自定义域](https://docs.github.com/zh/pages/configuring-a-custom-domain-for-your-github-pages-site)

## 后记

其他Jekyll的操作，比如文章怎么发布，参考你选择的主题Readme文件和Jekyll的文档 [Posts](https://jekyllrb.com/docs/posts/)