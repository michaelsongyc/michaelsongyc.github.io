# Hugo配置

## Hugo如何添加about页
### 创建页面
此处以even模板为例
```angular2html
hugo new post/about.md
```
文件创建完成后，会生成在 ${根目录}/content/post/目录下；
将about.md上移一层，到${根目录}/content/下, 和post目录同级。

### 修改配置
修改根目录文件config.toml，新增如下配置
```angular2html
[[menu.main]]
  name = "关于"
  identifier = "about"
  url = "/about/"
  weight = 50
```
weight 根据menu的顺序自定义数字即可；

### 启动服务
```angular2html
hugo server -D
```
访问：[http://localhost:1313](http://localhost:1313) 即可查看效果；

## Hugo添加文章目录toc

config.toml中加上toc的显示控制变量.
```java
toc = true
```

## 访问计数
如下配置
```java
[params]
  [params.busuanzi]         ## count web traffic by busuanzi                             ## 是否使用不蒜子统计站点访问量
    enable = true
    siteUV = true
    sitePV = true
    pagePV = true
```

## 评论
```java
 [params.utteranc]       ## https://utteranc.es/
   enable = true
   repo = "lovelock/blog-comments"               ## The repo to store comments
   issueTerm = "pathname"  ##表示你选择以那种方式让github issue的评论和你的文章关联。
   theme = "github-light" ## 样式主题，有github-light和github-dark两种
   async = true

```
## Emoji 表情 支持
Emoji 可以通过多种方式在 Hugo 项目中启用.

`emojify` 方法可以直接在模板中调用, 或者使用`行内 Shortcodes`.

要全局使用 emoji, 需要在你的网站配置中设置 `enableEmoji 为 true`, 然后你就可以直接在文章中输入 emoji 的代码.

它们以`冒号`开头和结尾，并且包含 emoji 的 代码:
```markdown
去露营啦! :tent: 很快就回来.
真开心! :joy:
```
去露营啦! :tent: 很快就回来.

真开心! :joy:

[Emoji 支持](https://hugoloveit.com/zh-cn/emoji-support)

## 其他
### 统计
[hugo不蒜子统计数量](https://www.cnblogs.com/brady-wang/p/13837812.html)

### 增强
- [https://lewky.cn/posts/hugo-3.2.html/](https://lewky.cn/posts/hugo-3.2.html/)

### 文章总数展示
- [参见](https://immmmm.com/hugo-total-count/)
- [官方doc](https://gohugo.io/templates/lists/)
- 新增文章总数
- 对于 LoveIt 主题，在`LoveIt/layouts/_default/section.html`中，新增一行code `共 {{ len (where .Site.RegularPages "Section" "posts") }} 篇文章`
如下：
```html
{{- define "content" -}}
    <div class="page archive">
        {{- /* Title */ -}}
        <h2 class="single-title animate__animated animate__pulse animate__faster">
            {{- .Params.Title | default (T .Section) | default .Section | dict "Some" | T "allSome" -}}
        </h2>
        共 {{ len (where .Site.RegularPages "Section" "posts") }} 篇文章
        {{- /* Paginate */ -}}
        .....
```
### 文章列表页置顶
使用`weight`标识符，如下示例：
```html
title: "心灵鸡汤"
date: 2021-01-03T20:19:50+08:00
draft: false
tags: ["励志"]
categories: ["生活"]
weight: 1
```

##  LoveIt模板 结合 Algolia搜索
loveit支持两种搜索：lunr和algolia。

lunr性能实在堪忧，特别是对于中文而言基本上不能用。

因此我们选用algolia，社区版10000条（不知道是按什么计算的，反正肯定不是按文章）搜索项，每月10000次搜索，对于小网站来说也够用了。之后我打算抽空自己用微服务写一个，到时候和博客挂在一起。

操作：
1. 前往官网注册账号[algolia](https://www.algolia.com/apps/Z3HRU4UP1I/dashboard)
2. 在左侧第二个Indices下选择New，创建Index（我的名称为 blog ）
3. 在API Keys上拿到Search-Only API Key作为searchKey和Application ID作为appID。
4. 配置 config.toml 文件，如下：
```javascript
  [params.search]
    enable = true
    type = "algolia"
    contentLength = 4000
    placeholder = ""
    maxResultLength = 10
    snippetLength = 50
    highlightTag = "em"
    absoluteURL = false
  [params.search.algolia]
    index = "blog"
    appID = "xxxx"
    searchKey = "xxxx"
[outputs]
  home = ["HTML", "RSS", "JSON"] # 这个一定要带上JSON，不然不会产生对应的JSON文件
```
每次 hugo 命令之后会产生/public/index.json文件，需要将这个文件上传到algolia中
json文件大小有限制，内容至多1024k=1MB，超限则上传失败；


## hugofastsearch

- [hugofastsearch.md](https://gist.github.com/cmod/5410eae147e4318164258742dd053993#setup)
- 当前仅支持搜索标题

### Setup
- Add index.json file to layouts/_default
- Add JSON as additional output format in config.toml
- Add fastsearch.js and fuse.js (downloaded from fusejs.io) to static/js
- Add searchbox html to bottom of layouts/_default/baseof.html
- Add css styles to your site's main css file or top of baseof.html
- hugo
- Visit localhost:1313/
- Press CMD-/ to invoke search
> You can check the json index by visiting localhost:1313/index.json

### Detail
### index.json
- layouts/_default/index.json
- Hugo already builds indexes of all pages, we can cherry-pick which aspects should be searchable. The result is a newly created JSON index at /index.json
```javascript
{{- $.Scratch.Add "index" slice -}}
{{- range .Site.RegularPages -}}
    {{- $.Scratch.Add "index" (dict "title" .Title "tags" .Params.tags "categories" .Params.categories "contents" .Plain "permalink" .Permalink) -}}
{{- end -}}
{{- $.Scratch.Get "index" | jsonify -}}
```

### Config.toml
Add this snippet to your config file to instruct Hugo to create the index file in JSON format. (RSS and HTML are default outputs, what's important is to add JSON.
```javascript
[outputs]
  home = ["HTML", "RSS", "JSON"]

```

### static/js/fastsearch.js
This "makes" the search engine based on the index.json file, and wires up all keyboard handling
```javascript
var fuse; // holds our search engine
var searchVisible = false;
var firstRun = true; // allow us to delay loading json data unless search activated
var list = document.getElementById('searchResults'); // targets the <ul>
var first = list.firstChild; // first child of search list
var last = list.lastChild; // last child of search list
var maininput = document.getElementById('searchInput'); // input box for search
var resultsAvailable = false; // Did we get any search results?

// ==========================================
// The main keyboard event listener running the show
//
document.addEventListener('keydown', function (event) {

    // CMD-/ to show / hide Search
    if (event.metaKey && event.which === 191) {
        // Load json search index if first time invoking search
        // Means we don't load json unless searches are going to happen; keep user payload small unless needed
        if (firstRun) {
            loadSearch(); // loads our json data and builds fuse.js search index
            firstRun = false; // let's never do this again
        }

        // Toggle visibility of search box
        if (!searchVisible) {
            document.getElementById("fastSearch").style.visibility = "visible"; // show search box
            document.getElementById("searchInput").focus(); // put focus in input box so you can just start typing
            searchVisible = true; // search visible
        } else {
            document.getElementById("fastSearch").style.visibility = "hidden"; // hide search box
            document.activeElement.blur(); // remove focus from search box
            searchVisible = false; // search not visible
        }
    }

    // Allow ESC (27) to close search box
    if (event.keyCode == 27) {
        if (searchVisible) {
            document.getElementById("fastSearch").style.visibility = "hidden";
            document.activeElement.blur();
            searchVisible = false;
        }
    }

    // DOWN (40) arrow
    if (event.keyCode == 40) {
        if (searchVisible && resultsAvailable) {
            console.log("down");
            event.preventDefault(); // stop window from scrolling
            if (document.activeElement == maininput) {
                first.focus();
            } // if the currently focused element is the main input --> focus the first <li>
            else if (document.activeElement == last) {
                last.focus();
            } // if we're at the bottom, stay there
            else {
                document.activeElement.parentElement.nextSibling.firstElementChild.focus();
            } // otherwise select the next search result
        }
    }

    // UP (38) arrow
    if (event.keyCode == 38) {
        if (searchVisible && resultsAvailable) {
            event.preventDefault(); // stop window from scrolling
            if (document.activeElement == maininput) {
                maininput.focus();
            } // If we're in the input box, do nothing
            else if (document.activeElement == first) {
                maininput.focus();
            } // If we're at the first item, go to input box
            else {
                document.activeElement.parentElement.previousSibling.firstElementChild.focus();
            } // Otherwise, select the search result above the current active one
        }
    }
});


// ==========================================
// execute search as each character is typed
//
document.getElementById("searchInput").onkeyup = function (e) {
    executeSearch(this.value);
}


// ==========================================
// fetch some json without jquery
//
function fetchJSONFile(path, callback) {
    var httpRequest = new XMLHttpRequest();
    httpRequest.onreadystatechange = function () {
        if (httpRequest.readyState === 4) {
            if (httpRequest.status === 200) {
                var data = JSON.parse(httpRequest.responseText);
                if (callback) callback(data);
            }
        }
    };
    httpRequest.open('GET', path);
    httpRequest.send();
}


// ==========================================
// load our search index, only executed once
// on first call of search box (CMD-/)
//
function loadSearch() {
    fetchJSONFile('/index.json', function (data) {

        var options = { // fuse.js options; check fuse.js website for details
            shouldSort: true,
            location: 0,
            distance: 100,
            threshold: 0.4,
            minMatchCharLength: 2,
            keys: [
                'title',
                'permalink',
                'summary'
            ]
        };
        fuse = new Fuse(data, options); // build the index from the json file
    });
}


// ==========================================
// using the index we loaded on CMD-/, run
// a search query (for "term") every time a letter is typed
// in the search box
//
function executeSearch(term) {
    let results = fuse.search(term); // the actual query being run using fuse.js
    let searchitems = ''; // our results bucket
    console.log('results.len= %s', results.length)

    if (results.length === 0) { // no results based on what was typed into the input box
        resultsAvailable = false;
        searchitems = '';
    } else { // build our html
        for (let item in results.slice(0, 5)) { // only show first 5 results
            // console.log('article= %s results[item].item.title=%s', item, results[item].item.title)
            // console.log('article= %s results[item].item.permalink=%s', item, results[item].item.permalink)

            // searchitems = searchitems + '<li><a href="' + results[item].item.permalink + '" tabindex="0">' + '<span class="title">' + results[item].item.title + '</span><br /> <span class="sc">'+ results[item].item.section +'</span> — ' + results[item].item.date + ' — <em>' + results[item].item.desc + '</em></a></li>';
            var index = results[item].item.contents.indexOf(term)
            var showContent = results[item].item.contents.substring(index, index + term.len + 20)
            // var showContent = results[item].item.contents.substring(0, 10)
            searchitems = searchitems + '<li><a href="' + results[item].item.permalink + '" tabindex="0">' + '<span class="title">' + results[item].item.title + '</span><br /> <span class="sc">' + results[item].item.categories[0] + '</span> — ' + results[item].item.date + ' — <em>' + showContent + '</em></a></li>';

            // console.log('对象类型 typeof(results[item])=%s', typeof(results[item]))
            // console.log('对象属性 Object.getOwnPropertyNames(results[item])=%s', Object.getOwnPropertyNames(results[item]))
            // for(var name in results[item]){
            //     console.log('name=%s, results[item][name]= %s',name, results[item][name])
            // }
            console.log('searchitems= %s', searchitems)
        }
        resultsAvailable = true;
    }

    document.getElementById("searchResults").innerHTML = searchitems;
    if (results.length > 0) {
        first = list.firstChild.firstElementChild; // first result container — used for checking against keyboard up/down location
        last = list.lastChild.firstElementChild; // last result container — used for checking against keyboard up/down location
    }
}
```
### static/js/fuse.js
fuse.js (downloaded from [fusejs.io](https://fusejs.io/getting-started/installation.html) )) to static/js

### baseof.html

Add this block to the bottom of your baseof.html file, just before the footer
```java
 <div id="fastSearch">
   <input id="searchInput" tabindex="0">
   <ul id="searchResults">
   </ul>
 </div>
 <script src="/js/fuse.js"></script> <!-- download and copy over fuse.js file from fusejs.io -->
 <script src="/js/fastsearch.js"></script>
```

CSS Styling

Add css styles to your site's main css file or top of baseof.html

```javascript
#fastSearch { 
  visibility: hidden;
  position: absolute;
  right: 0px;
  top: 0px;
  display: inline-block;
  width: 300px;
}      

#fastSearch input { 
  padding: 4px 10px;
  width: 100%;
  height: 31px;
  font-size: 1.6em;
  color: #aaa;
  font-weight: bold;
  background-color: #000;
  border-radius: 3px 3px 0px 0px;
  border: none;
  outline: none;
  text-align: left;
  display: inline-block;
}

#searchResults li { 
  list-style: none; 
  margin-left: 0em;
  background-color: #aaa; 
  border-bottom: 1px dotted #000;
}
  #searchResults li .title { font-size: 1.1em; margin-bottom: 10px; display: inline-block;}

#searchResults { visibility: inherit; display: inline-block; width: 320px; }
#searchResults a { text-decoration: none !important; padding: 10px; display: inline-block; }
#searchResults a:hover, a:focus { outline: 0; background-color: #666; color: #fff; }
  
```
