# Java常用工具类方法


## JDK
### Objects
```java
Objects.equal(Object a, Object b)
```

## JDK8
```java
stream.anyMath(...)


```

### Function.identity()
- [Function.identity()的使用详解](https://blog.csdn.net/qq_41378597/article/details/103942253)

```java
List<Student> studentLists = new ArrayList();

// list -> map
Map<Long, Student> collect = studentLists.stream()
        .collect(Collectors.toMap(a -> a.getId(), Function.identity()));

Map<Long, Student> collec1 = studentLists.stream()
        .collect(Collectors.toMap(Student::getId, Function.identity()));
```

## 3方库
### fastJson
```java
JSON.parseObject(str, Obj.class);
```

## 其他
### volatile
```java
privat volatile boolean inited = false;
public volatile int inc = 0;

```

## MapStruct
### 背景
在我们日常开发的分层结构的应用程序中，为了各层之间互相解耦，一般都会定义不同的对象用来在不同层之间传递数据，因此，就有了各种 XXXDTO、XXXVO、XXXBO 等基于数据库对象派生出来的对象，当在不同层之间传输数据时，不可避免地经常需要将这些对象进行相互转换。

一般处理两种处理方式：
- ① 直接使用 Setter 和 Getter 方法转换、
  - 第一种方式如果对象属性比较多时，需要写很多的 Getter/Setter 代码。

- ② 使用一些工具类进行转换（e.g. BeanUtil.copyProperties）。
  - 第二种方式看起来虽然比第一种方式要简单很多，但是因为其使用了反射，性能不太好，而且在使用中也有很多陷阱。

MapStruct 是一个代码生成器，它基于约定优于配置方法极大地简化了 Java bean 类型之间映射的实现。自动生成的映射转换代码只使用简单的方法调用，因此速度快、类型安全而且易于理解阅读，源码仓库 Github 地址 MapStruct。总的来说，有如下三个特点：
- 基于注解
- `在编译期自动生成映射转换代码`
- 类型安全、高性能、无依赖性
### 原理
通过 debug 可以看出，XXXMapper.INSTANCE 获取到的是接口的实现类 XXXMapperImpl。这个转换器接口实现类是在编译期自动生成;

自动生成的代码和我们平时手写的差不多，简单易懂，代码完全在编译期间生成，没有运行时依赖。和使用反射的实现方式相比还有一个优点就是，出错时容易通过 debug 查询实现源码的方式来定位，而反射相对来说定位问题就要困难很多。

### 使用3个步骤
1. 引入依赖
```xml
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>
    <version>1.4.2.Final</version>
</dependency>
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct-processor</artifactId>
    <version>1.4.2.Final</version>
</dependency>
```
2. 创建相关转换对象
```java
@Data
public class ActivityTypeConfigDO implements Serializable {
    private Long id;
    private String name;
    private String umpConfig;
    private String extra;
}
```
```java
@Data
public class ActivityTypeConfigDTO implements Serializable {
    private Long id;
    private String name;
    private UmpConfig umpConfig;
    private Map<String,String> extra;\
}
```
3. 创建转换器类(Mapper)
```java
@Mapper
public interface ActivityTypeMapper {

    ActivityTypeMapper instance = Mappers.getMapper(ActivityTypeMapper.class);

    @Mapping(source = "umpConfig", target = "umpConfig", qualifiedByName = "parse2UmpConfig")
    @Mapping(source = "extra", target = "extra", qualifiedByName = "parse2ExtraMap")
    ActivityTypeConfigDTO toActivityTypeConfig(ActivityTypeConfigDO  activityTypeConfigDo);

    List<ActivityTypeConfigDTO> toActivityTypeConfigList(List<ActivityTypeConfigDO> activityTypeConfigDos);

    @Named("parse2UmpConfig")
    default UmpConfig parse2UmpConfig(String umpConfig) {
        return JSON.parseObject(umpConfig, UmpConfig.class);
    }

    @Named("parse2ExtraMap")
    default Map<String,String> parse2ExtraMap(String extra) {
        return JSON.parseObject(extra, new TypeReference<Map<String,String>>(){});
    }
}
```
4. 获取转换器类(Mapper)
   获取转换器的方式根据 @Mapper 注解的 componentModel 属性不同而不同，常用以下2种取值方式：
- default 默认方式，默认方式，使用工厂方式（Mappers.getMapper(Class)）来获取
  - 上文的示例中都是通过工厂方式获取的，也就是使用 MapStruct 提供的 Mappers.getMapper(Class<T> clazz) 方法来获取指定类型的 Mapper。然后在调用的时候就不需要反复创建对象了，方法的最终实现是通过我们定义接口的类加载器加载 MapStruct 生成的实现类（类名称规则为：接口名称 + Impl），然后调用该类的无参构造器创建对象。核心源码如下所示：
```java
    private static <T> T doGetMapper(Class<T> clazz, ClassLoader classLoader) throws NoSuchMethodException {
        try {
            Class<T> implementation = classLoader.loadClass(clazz.getName() + "Impl");
            Constructor<T> constructor = implementation.getDeclaredConstructor();
            constructor.setAccessible(true);
            return constructor.newInstance();
        } catch (ClassNotFoundException var4) {
            return getMapperFromServiceLoader(clazz, classLoader);
        } catch (InvocationTargetException | IllegalAccessException | InstantiationException var5) {
            throw new RuntimeException(var5);
        }
    }
```      

- spring Spring 的方式，可以通过 @Autowired 注解来获取，在 Spring 框架中推荐使用此方式
  - 对于依赖注入（dependency injection），使用 Spring 框架开发的朋友们应该很熟悉了，工作中经常使用。MapStruct 也支持依赖注入的使用方式，并且官方也推荐使用依赖注入的方式获取。使用 Spring 依赖注入的方式只需要指定 @Mapper 注解的 componentModel = "spring" 即可，示例代码如下：
```java
@Mapper(componentModel = "spring")
public interface SourceMapper {

  SourceMapper INSTANCE = Mappers.getMapper(SourceMapper.class);

  @Mapping(source = "sourceName", target = "targetName")
  Target toTarget(Source source);
}  

```
我们可以使用 @Autowired 获取的原因是 SourceMapper 接口的实现类已经被注册为容器中一个 Bean 了，通过如下生成的接口实现类的代码也可以看到，在类上自动加上了 @Component 注解。


