# Spring


## @Autowired 和 @Resource
### 定义
- @Autowired 对类成员变量、方法及构造函数进行标注，完成自动装配的工作。

- @Resource 在语义上被定义为通过其唯一的名称来标识特定的目标组件，其中声明的类型与匹配过程无关。

### 区别

在Spring框架中，如果在Service层中需要注入其他依赖的对象，通常我们都会使用@Autowired或者@Resource注解，但是它们是有区别的，比如@Autowired跟Spring框架强耦合了， 如果换成其他框架，@Autowired就没作用了。而@Resource是JSR-250提供的，它是Java标准，绝大部分框架都支持。

1. 包含的属性不同
@Autowired只包含一个参数：required，表示是否开启自动注入，默认是true。而@Resource包含七个参数，其中最重要的两个参数是：name 和 type。

2. @Autowired默认按byType自动装配，而@Resource默认byName自动装配。

@Autowired如果要使用byName，需要使用@Qualifier一起配合。而@Resource如果指定了name，则用byName自动装配，如果指定了type，则用byType自动装配。

3. 注解应用的地方不同

@Autowired能够用在：构造器、方法、参数、成员变量和注解上，而@Resource能用在：类、成员变量和方法上。

4. 出处不同

@Autowired是Spring定义的注解，而@Resource是JSR-250定义的注解。所以@Autowired只能在Spring框架下使用，而@Resource则可以与其他框架一起使用。

5. 装配顺序不同

@Autowired的装配顺序如下：
`
@Autowired默认先按byType进行匹配，如果发现找到多个bean，则又按照byName方式进行匹配，如果还有多个，则报出异常。
`

@Resource的装配顺序如下：
```html

如果同时指定了name和type，流程如下：
找到name和type唯一的bean，找得到，进行装配；找不到或者找到多个，都会抛出异常。

如果指定了name，流程如下：
只是指定了@Resource注解的name，则按name后的名字去bean元素里查找有与之相等的name属性的bean，找不到或者找到多个，都会抛出异常。

如果指定了type，流程如下：
只指定@Resource注解的type属性，则从上下文中找到类型匹配的唯一bean进行装配，找不到或者找到多个，都会抛出异常。

如果既没有指定name，也没有指定type，流程如下：
既不指定name属性，也不指定type属性，则自动按byName方式进行查找。如果没有找到符合的bean，则回退为一个原始类型进行进行查找，如果找到就注入。
```



