# Mac文字语音转换

## 文字转语音

### say的常用方法
> say -v '?' ;列出所有播音员
> 
> say -o xxx.acc ;指出输出文件名称
> 
> say -f xxx.xxx ;指出输入文件名称
> 
> say -v Ting-Ting 我爱你中国 ;指定播音员”Ting-Ting“说”我爱你中国“。

参见：[https://www.jianshu.com/p/5d01cc011c6c](https://www.jianshu.com/p/5d01cc011c6c)

### 简单调用
在mac的命令行下面，执行：
> say hello world

就可以发出"hello world"的声音了，支持中文。

### 文本文件播放语音
新建一个文本文件aa.txt，在终端里在，cd 到该目录，然后执行:
> say -f aa.txt

### 文本文件转为语音文件
新建一个文本文件aa.txt，在终端里在，cd 到该目录，然后执行:
> say -f aa.txt -o a.m4a

即可生成a.m4a这个语音文件。 -f 后面跟的是需要转换成语音的文件，-o 是 -out 的意思。

### 设置
修改语音声音
打开Mac系统偏好设置->辅助功能->语音

对于国人来说，Sin-ji是讲粤语的，而Ting-ting是讲普通话的。当然，所有的演员都认识英文。
- 可切换语种
- 可调整语速

## 语音转文字
### 1. MAC系统自带的听写功能
- [http://blog.itpub.net/69983418/viewspace-2772692/](http://blog.itpub.net/69983418/viewspace-2772692/)
- 为获得最佳效果，每次说话的时长不要超过 40 秒。如果周围环境太吵，会让识别效果变差。

### 2. Google Docs Voice Typing
Mac听写软件下载：很少有人知道 Google Docs 有一个非常强大的免费语音识别引擎，它不仅可以将语音转录为文本，还可以让你使用语音命令更改文档的格式。

Google Docs 语音输入可以说比 Apple Dictation 更准确，但它仅适用于 Google Chrome 浏览器——它不适用于 Safari 或 Firefox。

要激活 Google Docs 语音输入功能，请打开任何 Google 文档并转到` “工具” -> “语音输入” `，然后单击出现的麦克风以开始转录。

Google Docs Voice Typing 对于免费的在线文本转语音工具来说非常准确。

### 3. T



## 参照
- https://javascript.net.cn/articles/721
- https://newsn.net/say/mac-command-say.html
