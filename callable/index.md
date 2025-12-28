# Java 多线程并行处理

## Callable 和 Runnable的区别
### 相同点
1. 都是接口
2. 都可以编写多线程程序
3. 都采用Thread.start()启动线程

### 不同点
1. Runnable没有返回值；Callable可以返回执行结果，是个泛型，和Future、FutureTask配合可以用来获取异步执行的结果;
2. callable接口实现类中的run方法允许异常向上抛出，可以在内部处理，try catch，但是runnable接口实现类中run方法的异常必须在内部处理，不能抛出;
3. 注：Callalble接口支持返回执行结果，需要调用FutureTask.get()得到，此方法会阻塞主进程的继续往下执行，如果不调用不会阻塞。


### Callable 样例

1. 声明线程池；
2. 创建任务；
3. 任务执行；
4. 等待任务执行完成；
5. 解析任务执行结构；
6. 关闭线程池；

```java
public class ExecutorPool {

    /*声明线程池*/
    public static final ExecutorService EXECUTOR = new ThreadPoolExecutor(
            4,
            100,
            0L,
            TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(100),
            new ThreadFactoryBuilder().setNameFormat("customize-pool-%d").build(),
            new ThreadPoolExecutor.CallerRunsPolicy());

}

```

```java

@Data
@Builder
public class ResultDTO {
    Integer id;
    boolean processResult;
}

@Data
@Slf4j
public class CampaignDemoTask implements Callable<ResultDTO> {

    private Integer id;
    private CountDownLatch countDownLatch;

    public CampaignDemoTask(Integer id, CountDownLatch countDownLatch) {
        this.id = id;
        this.countDownLatch = countDownLatch;
    }

    @Override
    public ResultDTO call() throws Exception {
        try {
            log.info("CampaignTaskDemoThread process id={}", this.id);
            //process...
            if (NumberUtils.INTEGER_ONE.equals(this.id)) {
                TimeUnit.SECONDS.sleep(5L);
            } else {
                TimeUnit.SECONDS.sleep(2L);
            }

            log.info("CampaignTaskDemoThread finish id={}", this.id);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            this.countDownLatch.countDown();
        }
        return ResultDTO.builder().id(this.id).processResult(Boolean.TRUE).build();
    }
}
```

```java

@Slf4j
public class CallableTest {

    public static void main(String[] args) {
        CallableTest callTest = new CallableTest();
        callTest.run();
    }

    public void run() {

        //总任务数
        int total = 3;
        //线程任务结果集
        List<Future<ResultDTO>> futureList = Lists.newArrayList();

        //1. 组装线程任务
        CountDownLatch countDownLatch = new CountDownLatch(total);

        for (int i = 0; i < total; i++) {
            CampaignDemoTask campaignTaskDemoThread = new CampaignDemoTask(i, countDownLatch);

            //2. 线程池执行
            Future<ResultDTO> result = ExecutorPool.EXECUTOR.submit(campaignTaskDemoThread);
            futureList.add(result);
        }

        log.info("futureList={}", JSON.toJSONString(futureList));

        //3. 等待线程池任务执行完成
        try {
            countDownLatch.await();

            // 个别线程执行时间会很长，如果线程执行时间超过某段时间，自动结束该线程; 可使用下面方式
            // countDownLatch.await(3L, TimeUnit.SECONDS);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        log.info("futureList={}", JSON.toJSONString(futureList));
        //3. 解析处理结果
        futureList.forEach(t -> {
            try {
                ResultDTO result = t.get();
                log.info("result={}", JSON.toJSONString(result));
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (ExecutionException e) {
                e.printStackTrace();
            }
        });

        //4.线程池关闭
        ExecutorPool.EXECUTOR.shutdown();
        log.info("done!");

    }
}

```


