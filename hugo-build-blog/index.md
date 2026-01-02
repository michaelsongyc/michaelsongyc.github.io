# Hugo搭建博客(How to build a blog quickly?)

## Hugo介绍

[//]: # (![图片引用]&#40;/image/hexo-blog.png&#41;)
[![pic.png](https://i.postimg.cc/SxPK5808/pic.png)]()
Hugo 几年前的影响力是不如 hexo 的，但现在越来越多的人从 hexo 迁移到了 Hugo，Hugo使用人数也多了起来，GitHub 上 Hugo 项目有 56.2k 个 star，已远远超过了 hexo，因此你也不用太担心 Hugo 会不会太小众化的问题，但是 Hugo 上的主题选择会更少一些，其中最受欢迎的是 wowchemy，但也仅有 6.1k 个star，而本站采用的是 LoveIt 主题，它的 star 就更少了，才 1.6k 个。当然，如果你是搞前端开发的，或者乐意自己写主题，那这些就不重要了。

### 优点
#### 速度快

Hugo 采用 Go 语言编写，它的速度用作者的话来形容就是世界上最快的构建网站工具。并且 Hugo 是即时渲染的，这意味着你可以边写边改样式，直到你满意为止。即使是你写了几百篇文章，它也能在几秒之内全部渲染完成。

The world’s fastest framework for building websites

#### 配置更为简单

你需要安装只是 Hugo，不像 hexo 还得安装 Node.js。并且Hugo 中是不区分站点和主题的配置文件的，Hugo 中只有一个位于站点根目录下的 config.toml 配置文件，你只用在这里面进行修改就可以了。

#### 方便自定义

你可以在不修改主题文件的前提下方便地定制主题。在 Hugo 中，如果你想要定制主题，你只需在站点目录下新建相应的文件即可。这是非常利于主题的维护的，你只需使用 Git 的 submodule 的方式安装 Hugo 的主题，然后更新时只需直接在站点根目录下敲一条命令回车即可，非常方便！

### 缺点

主题比较少，很可能大家都是用的同一个主题，并且主题作者更新会更少一点。

总结：如果你喜欢 DIY，我建议使用 Hugo。如果你是个专业博主，写了很多文章需要渲染，我建议使用 Hugo！

## 环境准备
### 安装 hugo
- 自动安装（依赖网速，可能会非常慢） 在终端中输入 `brew install hugo` 安装。 安装成功后可输入 `hugo version` 查看版本信息。

- 手工安装
  首先，请前往 GitHub 上下载最新版的 Hugo 压缩包，Releases · gohugoio/hugo (github.com)，建议选择 extended 版本，这将更有利于后续的 DIY 操作！ 
下载完成后解压到一个你认为合适的位置，然后把 hugo.exe 所在的文件夹添加至环境变量中的 Path 中即可。

## 使用流程
### 1.创建新网址
> hugo new site quickstart 

### 2.添加一个主题
- 在 quickstart 目录下打开新的终端，输入 `git init`
> git clone https://github.com/olOwOlo/hugo-theme-even themes/even

把 \themes\LoveIt\exampleSite目录下的config.toml复制下来，替换掉 `站点根目录` 下的同名文件。
- 然后对这个文件进行一些自定义修改。

- 然后进入根目录下的archetypes文件夹中，修改default.md文件为下面的内容（这个文件是模板文件，通过指令创建的文章将以模板为基础内容）
```angular2html
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
tags: [""]
categories: [""]
toc:
  enable: true
description: 
draft: true

```
> 其中title表示文章标题，date为生成文章当时的时间，tags为标签，categories为目录，toc enable为启用文章目录（需要自己在文章中生成），description为文章摘要，draft表示是否为草稿（写完了文章把这里改为 false 即可），<!--more-->为 LoveIt 主题的摘要标识符，该标识符上方的内容为文章摘要，如果上方为空，则采用 frontmatter 中设置的descriptions为文章摘要。

### 3.添加文件
现在开始撰写文章
> hugo new posts/my-first-post.md

并将draft：ture更改为draft：false。

注意: 对于even这个主题，应该使用 post 而不是 posts，即 
>hugo new post/some-content.md

### 4.构建页面
写完了文章进行网页的构建

> hugo server -D -e production

-serve表示启动一个本地服务器，即时渲染，方便修改； -D表示草稿也要渲染。

hugo serve 的默认运行环境是 development, 而 hugo 的默认运行环境是 production。

由于本地 development 环境的限制, 评论系统**, **CDN 和 fingerprint 不会在 development 环境下启用。

你可以使用 
> hugo serve -e production 

命令来开启这些特性。

值得一提的是不论输入的是server还是serve都是一样的。

在浏览器中前往它给出的 [http://localhost:1313](http://localhost:1313) 就能看到你刚生成的博客了。

当你运行 hugo serve 时, 当文件内容更改时, 页面会随着更改自动刷新.

现在再输入指令
> hugo -D

这会生成一个 public 目录, 其中包含你网站的所有静态内容和资源. 现在可以将其部署在任何 Web 服务器上。

确认无误后就要把它发到公网上了，这里采用 GitHub pages 进行部署（当然，也有很多种方法也能达成这一目的）

### 5. 启动 Hugo 服务器
``` shell
hugo server -D
```
 -D 代表草稿态
###  6. 上传至 github

## Refer
[参考](https://spongecaptain.cool/post/hugo/howtousehugo/#1-%E5%AE%89%E8%A3%85-hugo)

###  知识点
用hugo生成的网站包含以下文件和文件夹：
- archetypes: 储存.md的模板文件，该文件夹的优先级高于themes下的archetypes文件夹
- config.toml: 配置文件
- content: 储存网站的所有内容
- data: 储存数据文件供模板调用
- layouts: 储存.html模板，该文件夹的优先级高于主题下的layouts文件夹
- static: 储存图片、css、js等静态文件，该目录下的文件会直接拷贝到/public，该文件夹的优先级高于主题下的/static文件夹
- themes: 储存主题
- public: 执行hugo命令后，储存生成的静态文件

### Hugo置顶文章
Hugo 置顶文章很简单，只需要将以下放在文章头部即可置顶
```angular2html
---
weight: 1
---
``` 
### command
```hugo
#查看版本
hugo version

#版本和环境详细信息
hugo env

#创建新站点
hugo new site "mysite"

#创建文章
hugo new index.md  

在content/文件夹可以看到，此时多了一个markdown格式的文件index.md，打开文件可以看到时间和文件名等信息已经自动加到文件开头，包括创建时间，页面名，是否为草稿等。

#编译生成静态文件
hugo
Hugo将编译所有文件并输出到public目录     

#编译生成静态文件并启动web服务
hugo server
```
