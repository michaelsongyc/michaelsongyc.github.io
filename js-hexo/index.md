# Hugo同Hexo对比 && Hexo简要使用

Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

Hexo 和 Hugo 都是流行的静态网站生成器，常用于搭建个人博客等静态网站，以下是它们的一些对比：

## hugo同hexo对比
### 性能

* **Hugo**：用 Go 语言编写，执行效率高，生成静态页面速度极快，尤其是在处理大量文章和复杂布局时，性能优势明显。例如，一个包含上千篇文章的博客，Hugo 能在几秒内完成构建。
* **Hexo**：基于 Node.js，生成速度也较快，但相比 Hugo，在处理大规模内容时性能稍逊一筹。

### 易用性

* **Hugo**：虽然也有简洁的命令行界面，但对于不熟悉 Go 语言的用户来说，可能需要花费一些时间学习其配置和使用方法。不过，一旦掌握，使用起来也非常方便。
* **Hexo**：使用 JavaScript 编写，对于熟悉 JavaScript 和 Node.js 的用户来说，上手容易。其命令行工具简单直观，通过几条命令就能完成博客的创建、文章的撰写以及网站的部署等操作。

### 主题和插件

* **Hugo**：主题数量也不少，且质量较高，很多主题都具有良好的响应式设计。在插件方面，Hugo 的插件相对较少，但通过其强大的模板系统，也能实现各种复杂的功能。
* **Hexo**：拥有丰富的主题资源，且主题的定制相对容易，用户可以根据自己的需求修改主题的样式和功能。同时，插件生态也比较完善，能通过插件扩展博客的功能，如添加搜索功能、评论系统等。

### 部署

* **Hugo**：同样支持多种部署方式，与各种云服务和静态文件托管平台兼容性良好，部署过程高效快捷。
* **Hexo**：生成的静态文件可以方便地部署到 GitHub Pages、Netlify 等平台上，也可以部署到自己的服务器上。部署过程简单，只需将生成的文件上传到相应的服务器即可。

### 社区支持

* **Hugo**：社区也在不断发展壮大，虽然在规模上可能略小于 Hexo 社区，但也有丰富的文档和活跃的用户群体，能为用户提供支持。
* **Hexo**：社区活跃，文档丰富，用户在使用过程中遇到问题，很容易在网上找到相关的解决方案或向社区寻求帮助。

如果你对性能要求极高，不介意学习一些新的配置方法，且希望在处理大规模内容时能有出色的表现，那么 Hugo 可能更适合你。

如果你熟悉 JavaScript 和 Node.js，注重主题的丰富性和定制性，以及社区的活跃度，那么 Hexo 可能是一个不错的选择；

## Hexo Quick Start

### Create a new post

``` java
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)


## refer to
GitHub Pages + Hexo搭建个人博客网站，史上最全教程
https://blog.csdn.net/yaorongke/article/details/119089190

### setup node js
   验证 安装完成后打开终端输入
   npm -v
   node -v
   出现版本信息，说明安装成功
   refer to: https://blog.csdn.net/qq_45220508/article/details/122972391

###  setup Hexo
    1. 输入npm i hexo-cli -g安装Hexo; 若提示权限不足，使用sudo 开头
    2. 输入hexo -v验证是否安装成功。
    3. 输入hexo g生成静态网页
    4. 输入hexo s打开本地服务器，浏览器打开http://localhost:4000/
    5. 按ctrl+c关闭本地服务器。

###  写文章、发布文章
   首先在博客根目录下右键打开git bash，安装一个扩展npm i hexo-deployer-git

然后输入 hexo new post "article title"，新建一篇文章。

然后打开D:\study\program\blog\source\_posts的目录，可以发现下面多了一个文件夹和一个.md文件，一个用来存放你的图片等数据，另一个就是你的文章文件啦。

编写完markdown文件后，
然后输入hexo s可以本地预览效果，最后输入hexo d上传到github上。这时打开你的github.io主页就能看到发布的文章啦

1. github 上有很多theme ，search keyword： hexo-theme
2. 不同的hexo theme，先下载them到project的themes包中，然后copy修改_config.yml（不同的themes 对应不同的_config.yml）。
