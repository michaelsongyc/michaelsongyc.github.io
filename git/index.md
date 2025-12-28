# Git小结


## 版本控制 : 集中式 vs 分布式 
### 集中式版本控制（csv、svn）
- 集中式版本控制系统，版本库是集中存放在中央服务器的，而干活的时候，用的都是自己的电脑，所以要先从中央服务器取得最新的版本，然后开始干活，干完活了，再把自己的活推送给中央服务器。中央服务器就好比是一个图书馆，你要改一本书，必须先从图书馆借出来，然后回到家自己改，改完了，再放回图书馆。
- 缺点
  - 集中式版本控制系统最大的毛病就是必须联网才能工作；
  - 集中式版本控制系统的中央服务器要是出了问题，所有人都没法干活了。

### 分布式版本控制（git）
- 分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样，你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上.
- 优点
  - 不需要联网，也可以工作；
  - 安全性要高很多，因为每个人电脑里都有完整的版本库，某一个人的电脑坏掉了不要紧，随便从其他人那里复制一个就可以了。
  

## Git安装&&配置

### yum安装(Linux)
在Linux上是有yum安装Git，非常简单，只需要一行命令
> yum -y install git

输入 
> git version

查看Git是否安装完成以及查看其版本号

备注： yum安装git被安装在 `/usr/libexec/git-core` 目录下

### Git初始化配置
a、设置用户名：
` git config --global user.name "<用户名>"`

b、设置用户邮箱：
` git config --global user.email "<电子邮件>" `

注意：该配置会在github主页上显示谁提交了该文件

c、配置ok之后，我们用如下命令来看看是否配置成功

`git config --list`

注意：`git config --global` 参数，有了这个参数表示你这台机器上所有的git仓库都会使用这个配置，当然你也可以对某个仓库指定不同的用户名和邮箱


### Git 配置SSH
git使用SSH，需要以下三个步骤:
- 使用秘钥生成工具生成rsa秘钥和公钥
- 将rsa公钥添加到git代码托管平台
- 将rsa秘钥添加到ssh-agent中，为ssh client指定使用的秘钥文件

第一步：检查本地主机是否已经存在ssh key
```shell
cd ~/.ssh
ls
//看是否存在 id_rsa 和 id_rsa.pub文件，如果存在，说明已经有SSH Key
```

第二步：生成ssh key
如果不存在ssh key，使用如下命令生成
```
ssh-keygen -t rsa -C "xxx@xxx.com"
//执行后一直回车即可
```

第三步：获取ssh key公钥内容（id_rsa.pub）
```
cd ~/.ssh
cat id_rsa.pub
```

第四步：Github账号上添加公钥
进入Settings设置

第五步：验证是否设置成功
```
ssh -T git@github.com
```


## Git使用
### 命令流程图

{{< figure src="/image/tech/tools/git_0.png" title="远程仓库-本地仓库-工作区" >}}

![徒手深蹲](/image/tech/tools/git_2.png)

{{< figure src="/image/tech/tools/git_1.png" title="本地仓库-工作区" >}}

[//]: # (![徒手深蹲]&#40;/image/tech/tools/git_1.png&#41;)

### git help
要想获得 git config 命令的手册，执行
> git help add

如果你不需要全面的手册，只需要可用选项的快速参考，那么可以用 -h 选项获得更简明的 “help” 输出：
> git add -h

### 常规git操作Demo

你应该能够置并初始化一个仓库（repository）、开始或停止跟踪（track）文件、暂存（stage）或提交（commit）更改.

```shell
1.进入目标文件夹
cd demo

2.初始化仓库
git init

3.仓库中创建 readme.txt文件，文本内容：test

4.查看仓库状态
git status

请记住，你工作目录下的每一个文件都不外乎这两种状态：已跟踪 或 未跟踪。

5.添加文件到暂存区中
git add readme.txt

6.提交文件到仓库
git commit -m "readme.txt提交"

7.查看仓库状态
git status

8.提交到远程库
git push

9.本地修改 readme.txt 文件后可以使用diff查看文件修改前后对比
git diff readme.txt

10.查看最近到最远的历史提交日志
git log
[不传入任何参数的默认情况下，git log 会按时间先后顺序列出所有的提交，最近的更新排在最上面。 
正如你所看到的，这个命令会列出每个提交的 SHA-1 校验和、作者的名字和电子邮件地址、提交时间以及提交说明。]

11.回退版本
git reset  --hard 版本号
```
## Git常用基本命令

```shell
   mkdir  XX (创建一个空目录 XX指目录名)
   git init     把当前的目录变成可以管理的git仓库，生成隐藏.git文件
   pwd    显示当前目录的路径
   cat XX             查看XX文件内容
   
   git add XX         把xx文件添加到暂存区去
   git commit –m “XX” 提交文件到本地仓库 –m 后面的是注释
   
   git push –u(第一次要用-u 以后不需要) origin master 把当前master分支推送到远程库
   git push origin master  Git会把master分支推送到远程库对应的远程分支上
   
   git push origin --delete serverfix 删除远程分支
   
   git status         查看仓库状态

   git diff  XX   查看XX文件修改了那些内容
   git log        查看历史记录
   git reflog       查看历史记录的版本号id

   git reset  --hard HEAD^ 回退到上一个版本 
   git reset  --hard HEAD~ 回退到上一个版本
   git reset  –-hard HEAD~100 (回退到100个版本)
      
   git checkout -- XX  把XX文件在工作区的修改全部撤销
   git rm XX          删除XX文件

   git remote 查看远程库的信息
   git remote –v 查看远程库的详细信息
   git remote add origin https://github.com/tugenhua0707/testgit 关联一个远程库
   
   git fetch 从远程仓库中抓取与拉取
    
   git clone https://github.com/tugenhua0707/testgit  从远程库中克隆
   git checkout –b dev  创建dev分支 并切换到dev分支上； 等同于两条命令的: git branch dev; git checkout dev; 
   git checkout master 切换到master分支
   
   git branch  查看当前所有的分支[查看当前所有分支的一个列表]
   git branch -v 查看每一个分支的最后一次提交
   git branch –d bug_fix 删除bug_fix分支
   git branch <name>  创建新分支
  
   git branch --no-merged master 尚未合并到 master 分支列表
   
   git merge dev    在当前的分支上合并dev分支

   git stash 把当前的工作隐藏起来 等以后恢复现场后继续工作
   git stash list 查看所有被隐藏的文件列表
   git stash apply 恢复被隐藏的文件，但是内容不删除
   git stash drop 删除文件
   git stash pop 恢复文件的同时 也删除文件

```

## tmp
```html
初始化一个Git仓库，使用: git init
添加文件到Git仓库，分两步：
使用命令: git add <file>  注意，可反复多次使用，添加多个文件；
使用命令: git commit -m <message>
    
要随时掌握工作区的状态，使用命令: 
    git status
    请记住，你工作目录下的每一个文件都不外乎这两种状态：已跟踪 或 未跟踪。    
如果git status告诉你有文件被修改过, 则用 git diff可以查看修改内容: 
    git diff readme.txt

HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令：
    git reset --hard commit_id。
穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--pretty=oneline参数：
git log --pretty=oneline

如果用git log可以按s向下翻log
git log --oneline 可以一行展现
    
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令: git checkout -- file。
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令 git reset HEAD <file>，就回到了场景1，第二步按场景1操作。
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。
    
远程库已经准备好了，下一步是用命令git clone克隆一个本地库：
git clone git@github.com:xxx/xxx.git
    
每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

命令git rm用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。

git remote -v 查看远程库信息.
$ git remote -v
origin  git@github.com:michaelliao/learn-git.git (fetch)
origin  git@github.com:michaelliao/learn-git.git (push)

然后，根据名字删除，比如删除origin：
$ git remote rm origin
    
我们创建dev分支，然后切换到dev分支:
$ git checkout -b dev
git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：

or 创建并切换dev分支：
$ git switch -c dev
    
用git branch命令查看当前分支：
git branch命令会列出所有分支，当前分支前面会标一个*号。
    
我们把dev分支的工作成果合并到master分支上：
$ git merge dev

合并完成后，就可以放心地删除dev分支了：
$ git branch -d dev

Git鼓励大量使用分支：
查看分支：git branch
创建分支：git branch <name>
切换分支：git checkout <name>或者git switch <name>
创建+切换分支：git checkout -b <name>或者git switch -c <name>
合并某分支到当前分支：git merge <name>
删除分支：git branch -d <name>

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：
$ git push origin master

如果要推送其他分支，比如dev，就改成：
$ git push origin dev
      
因此，多人协作的工作模式通常是这样：
首先，可以试图用git push origin <branch-name>推送自己的修
如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
如果合并有冲突，则解决冲突，并在本地提交；
没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！

如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

小结
查看远程库信息，使用git remote -v；
本地新建的分支如果不推送到远程，对其他人就是不可见的；
从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；
在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；
建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；
从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。
```

## Refer to
- [https://git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)
- [https://www.liaoxuefeng.com/wiki/896043488029600](https://www.liaoxuefeng.com/wiki/896043488029600)
- [https://www.cnblogs.com/upstudy/p/15868898.html](https://www.cnblogs.com/upstudy/p/15868898.html)
- [https://www.cnblogs.com/upstudy/p/15870787.html](https://www.cnblogs.com/upstudy/p/15870787.html)
