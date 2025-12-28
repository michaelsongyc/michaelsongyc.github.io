# bat同jar交互


Windows下通过bat调用jar时的传参，网上有很多例子，这里就不做阐述。

今儿讲下如何在bat调用jar后，获取jar的返回值：

思路如下：
1. jar内部执行相应的逻辑，输出不同的内容；
2. 通过常规bat调用jar，同时将jar的输出内容定向到某一个文件a.txt中。（jar中若有时间轮询或等待，bat则会等jar完全结束后，才回处理后续作业）
3. bat后续通过读取文件a.txt内容，进行后续逻辑操作；

以上思路，曲线救国~

demo code如下：inovke.bat
```shell
@echo off
java -jar test.jar > a.txt

setlocal enabledelayedexpansion
for /f  "tokens=1-3" %%i in (a.txt) do echo 第一列：%%i 第二列：%%j 第三列：%%k
echo %a%
pause
```

test.jar的主类文件：
```java
public class Config {
    public static void main(String[] args) {
        Random random = new Random();
        int result = random.nextInt(10);
        try {
            TimeUnit.SECONDS.sleep(result);// 随机等待秒速
        } catch (Exception e) {
            system.error.println("error",e);
        }
    }
}
```
以上，完成bat同jar的交互 ；

---

今儿发现另一个思路，可以使用python胶水，来曲线救国~   记录一下~
