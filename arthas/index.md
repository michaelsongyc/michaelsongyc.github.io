# 阿里开源-Arthas(阿尔萨斯)

## 概要
- Arthas 是Alibaba 开源的 Java 诊断工具，是一款线上(运行时)监控诊断产品，通过全局视角实时查看应用 load、内存、gc、线程的状态信息，并能在不修改应用代码的情况下，对业务问题进行诊断，包括查看方法调用的出入参、异常，监测方法执行耗时，类加载信息等，大大提升线上问题排查效率。
- [Arthas官网](https://arthas.aliyun.com/)


### Arthas（阿尔萨斯）能为你做什么？
Arthas 是 Alibaba 开源的 Java 诊断工具，深受开发者喜爱。

当你遇到以下类似问题而束手无策时，Arthas可以帮助你解决：

- 这个类从哪个 jar 包加载的？为什么会报各种类相关的 Exception？
- 我改的代码为什么没有执行到？难道是我没 commit？分支搞错了？
- 遇到问题无法在线上 debug，难道只能通过加日志再重新发布吗？
- 线上遇到某个用户的数据处理有问题，但线上同样无法 debug，线下无法重现！
- 是否有一个全局视角来查看系统的运行状况？
- 有什么办法可以监控到 JVM 的实时运行状态？
- 怎么快速定位应用的热点，生成火焰图？
- 怎样直接从 JVM 内查找某个类的实例？

Arthas 支持 JDK 6+，支持 Linux/Mac/Windows，采用命令行交互模式，同时提供丰富的 Tab 自动补全功能，进一步方便进行问题的定位和诊断。
### 一键安装
```shell
curl -L https://arthas.aliyun.com/install.sh | sh 
```

## Arthas原理
- 如此强大的工具，究竟是如何做到的呢?
  - `它通过 Instrument 机制和ASM字节码框架，干涉类的加载过程，动态注入字节码，搜集接口调用信息并反馈。`

&emsp;&emsp;在JDK5之后提供的新特性JavaAgent(Java代理）, java.lang.instrument包支持以Java代理的形式监控或重新定义运行中的服务．可以让我们在不修改程序代码的前提下通过 Instrumentation API改变运行中的java程序.

&emsp;&emsp;Instrumentation 是一个JVM接口，该接口提供了一组查看和操作Java类的方法，如修改类的字节码、向 classLoader
的classpath下加入jar文件等。开发者可以通过Java语言来操作和监控JVM内部的状态，进而实现Java程序的监控分析。

&emsp;&emsp;如Arthas的watch命令就是使用了 java.lang.instrument.Instrumentation#addTransformer(java.lang.instrument.ClassFileTransformer)来
动态增加字节码文件。
```java
public interface Instrumentation{
}
```
&emsp;&emsp;在动态修改字节码文件中，用到了[ASM框架](https://asm.ow2.io/index.html)，ASM是一个通用的Java字节码操作和分析框架。一般用来动态生成类或者增强既有类的功能。也就是既可以创建class文件，也可以修改class文件。

Arthas增强功能的核心是Enhancer和AdviceWeaver这两个类，对方法进行AOP织入，达到watch，trace等效果。

## 特性
### 后台异步任务，任务输出重定向
可通过 `>` 或者 `>>` 将任务输出结果输出到指定的文件中，可以和&一起使用，实现 arthas 命令的后台异步任务。
>  trace Test t >> /Users/michael/tmp_del/test.out & 
###  批处理功能
通过批处理功能，arthas 支持一次性批量运行多个命令，并取得命令执行的结果。

## 常用命令
- [命令列表](https://arthas.aliyun.com/doc/commands.html)
### 线程查看
当没有参数时，显示第一页线程的信息
> thread

支持一键展示当前最忙的前 N 个线程并打印堆栈：
> thread -n 3

thread id，显示指定线程的运行堆栈
> thread 1

找出当前阻塞其他线程的线程
有时候我们发现应用卡住了，通常是由于某个线程拿佳了某个锁， 并且其他线程都在等待这把锁造成的。 为了排查这类问题，arthas 提供 `thread -b` 一键找出那个 罪魁祸首。
> thread -b

查看指定状态的线程
> thread --state WAITING

### watch（函数执行数据观测）
查看日志
```shell
$ watch demo.MathGame primeFactors -x 2
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 32 ms, listenerId: 5
method=demo.MathGame.primeFactors location=AtExceptionExit
ts=2021-08-31 15:22:57; [cost=0.220625ms] result=@ArrayList[
    @Object[][
        @Integer[-179173],
    ],
    @MathGame[
        random=@Random[java.util.Random@31cefde0],
        illegalArgumentCount=@Integer[44],
    ],
    null,
]
```
### monitor（监控）
monitor 方法执行监控
> monitor com.xx.global.campaign.facade.impl.MerchantsSellerQueryFacadeImpl queryActivityListData -n 10 --cycle 10

```shell
$ monitor -c 5 demo.MathGame primeFactors
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 94 ms.
 timestamp            class          method        total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2018-12-03 19:06:38  demo.MathGame  primeFactors  5      1        4     1.15        80.00%

 timestamp            class          method        total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2018-12-03 19:06:43  demo.MathGame  primeFactors  5      3        2     42.29       40.00%

```
### trace（耗时）
trace 方法内部调用路径，并输出方法路径上的每个节点上耗时
> trace com.xx.global.campaign.service.business.service.core.impl.ActivitySearchService queryAllCampaignList -n 5 --skipJDKMethod false

```shell
$ trace demo.MathGame run -n 1
Press Q or Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 20 ms.
`---ts=2019-12-04 00:45:53;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[0.549379ms] demo.MathGame:run()
        +---[0.059839ms] demo.MathGame:primeFactors() #24
        `---[0.232887ms] demo.MathGame:print() #25

```

### 指定 classloader 更新 logger level
> logger
> logger -c 2a139a55 --name ROOT --level debug

### 查看所有的 option
> vmoption
 
## 其他
Arthas Tunnel??
## 案例
- [实际案例](https://github.com/alibaba/arthas/issues?q=label%3Auser-case)

## FAO
### 怎么以json格式查看结果？
> options json-format true
