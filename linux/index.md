# Linux相关

## Linux 单机最大进程数、线程数、连接数

### 1、查看Linux系统开启的最大进程数
> cat /proc/sys/kernel/pid_max


### 2、查看Lixu系统开启的最大线程数
> 查看 / 设置进程的最大用户线程数（user processes），这是对单个进程线程数的直接限制
>
> ulimit -u
> 
> ulimit -a

```html
➜  ~ ulimit -u
2784

➜  ~ ulimit -a
-t: cpu time (seconds)              unlimited
-f: file size (blocks)              unlimited
-d: data seg size (kbytes)          unlimited
-s: stack size (kbytes)             8192
-c: core file size (blocks)         0
-v: address space (kbytes)          unlimited
-l: locked-in-memory size (kbytes)  unlimited
-u: processes                       2784
-n: file descriptors                256
```

### 3、 一个进程允许启动的最大线程数

Linux环境下：
理论最大线程数 ≈ 进程可用内存 / 单个线程栈大小

示例：若进程可用内存为 16GB，每个线程Stack栈大小为 8MB，则理论最大线程数为 16GB / 8MB = 2048。
### 4、最大连接数

最大连接数限制就是系统所能打开的最大文件数（文件描述符）的限制，分全局和进程两种，相应的命令如下：

> sysctl kern.maxfiles
- 说明：全局限制，也就是系统默认的最大连接数限制是122880
```shell
➜  ~ sysctl kern.maxfiles
kern.maxfiles: 122880
```


> sysctl kern.maxfilesperproc
- 说明：单个进程默认最大连接数限制是61440

```shell
➜  ~ sysctl kern.maxfilesperproc
kern.maxfilesperproc: 61440
```

> ulimit －n

- 说明：“ulimit －n”命令显示当前shell能打开的最大文件数，默认值：256，该值总是小于kern.maxfilesperproc的值，因为一个shell就是一个进程。

```shell
➜  ~ ulimit -n
256
```

## Linux base64 编码与解码命令
1、 base64 编码
- (1) `base64 file` 功能：从指定的文件file中读取数据，编码为base64的字符串然后输出；

- (2) `echo "string" | base64` 功能：将宇符串stringt换行编码为base64的字符串然后输出;

- (3) `echo -n "string" I base64` 功能：将字符串string编码为base64的字符串然后输出；

2、base64 解码
- (1) `base64 -d file` 
功能：从指定的文件file中读取已经过base64编码的数据，然后进行解码，并输出解码后的字符串;

- (2) `echo "str" I base64 -d` 
功能：对base64编码的字符串str和空行进行解码，然后将解码后的字符串输出；

- (3) `echo -n "str" | base64 -d` 
功能：对base64编码的字符串str进行解码，然后将解码后的字符串输出；

## 常用命令
### 压缩命令
```shell
unrar x  *.rar
```

### 查看目录大小及文件数量
查看当前目录的空间大小:
> du -h -d 1
- 命令-d表示目录的深度，修改-d的值能看更多层目录的空间大小

查看根分区所有的文件和目录的大小。
> du -sh /*

以对单个文件或者目录执行
```shell
du -sh /home

du -sh *

du -lh --max-depth=1
```

### 查找大文件
> find . -type f -size +800M

### 查找Linux下的大目录

- [linux]
```shell
du -h --max-depth=1
#对结果排序
du -h --max-depth=1 | sort -n
```

- [mac]
```shell
du -hd1
#对结果排序
du -hd1 | sort -n 
```
