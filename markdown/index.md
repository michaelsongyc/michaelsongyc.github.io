# Markdown 基本语法

<!-- toc -->

[markdown-it-demo](https://markdown-it.github.io/)

# Refer 
[markdown grammar](https://wizardforcel.gitbooks.io/markdown-simple-world/content/2.html)

[Markdown，写博客当然要用这个。在线编辑器，所见即所得地编写。](https://www.zybuluo.com/mdeditor)

## 标题
markdown总支持1~6六级标题，通过在一行之前加上不同数量的井号来表示。
```angular2html
# 这是 H1 #

## 这是 H2 ##

### 这是 H3 ###
```
...
## 段落
非常自然，一行文字就是一个段落。
<p>这是一个段落1</p>
<p>这是一个段落2</p>

&emsp;&emsp; 段落开头的一个空格`&emsp;&emsp;`
> &emsp;&emsp;

## 文本样式

字体样式常用的有粗体、斜体、删除线等。

`*斜体字*` 对应 *斜体字*

`**粗体字**` 对应 **粗体字**

`~~删除线~~` 对应 ~~删除线~~

` 行内代码 `  对应 `行内代码`

还有一些样式原生不支持，但是可以通过html间接支持。

可以使用星号*或下划线_指定粗体或者斜体。
*这是斜体*
_这也是斜体_

**这是粗体**
***这是粗体+斜体***

```angular2html
*这是斜体*
_这也是斜体_

**这是粗体**
***这是粗体+斜体***

` 行内代码 `
```

## 引用
通过在行首加上大于号>来添加引用格式。
> DD
>
> DD

引用可以嵌套：
>w
> >引用可以嵌套：
```angular2html
>w
> >引用可以嵌套：
```

也可以嵌套其他格式：

## task list ( checkbox )
```html
- [ ] todo: task one not finish `- + SPACE +[ ]`
- [x] finish: task twp finish `- + SPACE +[x]`
```
- [ ] todo: task one not finish `- + SPACE +[ ]`
- [x] finish: task twp finish `- + SPACE +[x]`

## 列表
无序列表使用星号、加号或是减号作为列表标记：

* Red
* Green
* Blue

等同于
+ Red
+ Green
+ Blue

和
- Red
- Green
- Blue 

```angular2html
- Red
- Green
- Blue 
```


有序列表则使用数字接着一个英文句点：

1. Bird
2. McHale
3. Parish 

数字并不会影响输出的 HTML 结果，也就是说上面的例子等同于：
1. Bird
2. McHale
3. Parish

## 内联代码
用反引号` 来标记内联代码，它们会解释成<code>标签</code>
```angular2html
<code>标签</code>
```
如果代码的内容中有反引号，请用两个反引号包裹。

代码中的&、<、>符号都会自动转义，请放心使用。
```CODE
`
eee
`
```
`
eee
`

还有一种是github的风格，代码段的前后用三个反引号 ``` 独占一行来标记。

```
ww
```

## 分隔线
你可以在一行中用三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西。你也可以在星号或是减号中间插入空格。下面每种写法都可以建立分隔线：

* * *
***
*****
- - -
---------------------------------------
```angular2html
* * *
***
*****
- - -
---------------------------------------
```

## 超链接
[an example](http://example.com/)

[an example](http://example.com/ "Optional Title")
```angular2html
[an example](http://example.com/)

[an example](http://example.com/ "Optional Title")
```
会被解释为
```angular2html
<a href='https://www.google.com/'>google</a>
<a href='https://www.google.com/' title="Optional Title">google</a>
```

## 转义
markdown支持在以下字符前面插入反斜杠，插入之后，将不再解析这些字符，而是原样输出。

```angular2html
\   反斜线
`   反引号
*   星号
_   底线
{}  花括号
[]  方括号
()  括弧
#   井字号
+   加号
-   减号
.   英文句点
!   惊叹号

```
## 表格(table)

| Item     | Value | Qty   |
| :------- | ----: | :---: |
| Computer | $1600 |  5    |
| Phone    | $12   |  12   |
| Pipe     | $1    |  234  |

```angular2html
| Item     | Value | Qty   |
| :------- | ----: | :---: |
| Computer | $1600 |  5    |
| Phone    | $12   |  12   |
| Pipe     | $1    |  234  |
```

|title1| title2 |
|:---|:------:|
|v1|v2|

要注意第二行的冒号决定了居左居右还是居中，如果你不加冒号，默认是居左的。

另外可以把第一行去掉，做成没有表头的表格，**但第二行始终是要有的**。

## TOC
TOC = Table of content ， 将内容制作成导航

[hexo toc](https://blog.csdn.net/qq_46527915/article/details/105466439)

[快速生成markdown目录TOC](https://blog.csdn.net/qq_25760623/article/details/107739436)

[快速生成markdown目录TOC，页内跳转](https://www.jianshu.com/p/57f23e4140ba)

# 图片
## 内联

![Alt text](/path/img.jpg)

```angular2html
![Alt text](/path/img.jpg)
```

会被解释为

```angular2html
<img src='/path/to/img.jpg'/>
```

这种格式显示图片不能控制对齐和大小，需要的话可以采取raw html的方法：

```HTML
<p align="center">
    <img src="https://i.loli.net/2021/01/31/4ZHoWduI59fiqJY.png" width="100" />
</p>
```

自适配图片 引用方式如下：

{{< figure src="/image/travel/2022xinjiang/day9-02.png" title="独库公路-雪山" >}}

## 外联
```angular2html
![图片](https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png)
```
![图片](https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png)

## refer 
- [Refer](https://hugoloveit.com/zh-cn/basic-markdown-syntax)
