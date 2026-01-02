# SpringBean


![bean](/image/tech/spring/spring-framework.png)

## 简易Spring程序
1. 导入基础包
```xml
org.springframework.core-xx.jar
org.springframework.beans-xx.jar
spring-context-xx.jar
spring-expression-xx.jar
commons.logging-xx.jar
```
2. 创建Java (HelloWorld.java && MainApp.java)
```java
public class HelloWorld {
    private String message;

    public void setMessage(String message) {
        this.message = message;
    }

    public void getMessage() {
        System.out.println("message : " + message);
    }
}
```

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        HelloWorld obj = context.getBean("helloWorld",HelloWorld.class);
        obj.getMessage();
    }
}
```
> 关于以上代码，需要注意以下两点：

> 创建 ApplicationContext 对象时使用了 ClassPathXmlApplicationContext 类，这个类用于加载 Spring 配置文件、创建和初始化所有对象（Bean）。 

> ApplicationContext.getBean() 方法用来获取 Bean，该方法返回值类型为 Object，通过强制类型转换为 HelloWorld 的实例对象，调用其中的 getMessage() 方法。

3. 创建Beans.xml

在 src 目录下，创建一个 Spring 配置文件 Beans.xml，内容如下:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <bean id="helloWorld" class="net.biancheng.c.HelloWorld">
        <property name="message" value="Hello World!" />
    </bean>

</beans>
```

4. 运行程序

运行 MainApp.java，控制台中显示信息如下:
> message : Hello World!


## Spring IOC

Spring 有两个核心部分： IoC 和 AOP。

- IOC	Inverse of Control 的简写，译为“控制反转”，指把创建对象过程交给 Spring 进行管理。

- AOP	Aspect Oriented Programming 的简写，译为“面向切面编程”。
  用来封装多个类的公共行为，将那些与业务无关，却为业务模块所共同调用的逻辑封装起来，减少系统的重复代码，降低模块间的耦合度。另外，AOP 还解决一些系统层面上的问题，比如日志、事务、权限等。

在实际开发中，服务器端应用程序通常采用三层体系架构，分别为表现层（web）、业务逻辑层（service）、持久层（dao）。


IoC 是 Inversion of Control 的简写，译为“控制反转”，它不是一门技术，而是一种设计思想，是一个重要的面向对象编程法则，能够指导我们如何设计出松耦合、更优良的程序。

Spring 通过 IoC 容器来管理所有 Java 对象的实例化和初始化，控制对象与对象之间的依赖关系。我们将由 IoC 容器管理的 Java 对象称为 Spring Bean，它与使用关键字 new 创建的 Java 对象没有任何区别。

### 控制反转（IoC）

在传统的 Java 应用中，一个类想要调用另一个类中的属性或方法，通常会先在其代码中通过 new Object() 的方式将后者的对象创建出来，然后才能实现属性或方法的调用。为了方便理解和描述，我们可以将前者称为“调用者”，将后者称为“被调用者”。也就是说，调用者掌握着被调用者对象创建的控制权。

但在 Spring 应用中，Java 对象创建的控制权是掌握在 IoC 容器手里的，其大致步骤如下。

1. 开发人员通过 XML 配置文件、注解、Java 配置类等方式，对 Java 对象进行定义，例如在 XML 配置文件中使用 <bean> 标签、在 Java 类上使用 @Component 注解等。

2. Spring 启动时，IoC 容器会自动根据对象定义，将这些对象创建并管理起来。这些被 IoC 容器创建并管理的对象被称为 Spring Bean。

3. 当我们想要使用某个 Bean 时，可以直接从 IoC 容器中获取（例如通过 ApplicationContext 的 getBean() 方法），而不需要手动通过代码（例如 new Obejct() 的方式）创建。

IoC 带来的最大改变不是代码层面的，而是从思想层面上发生了“主从换位”的改变。原本调用者是主动的一方，它想要使用什么资源就会主动出击，自己创建；但在 Spring 应用中，IoC 容器掌握着主动权，调用者则变成了被动的一方，被动的等待 IoC 容器创建它所需要的对象（Bean）。

这个过程在职责层面发生了控制权的反转，把原本调用者通过代码实现的对象的创建，反转给 IoC 容器来帮忙实现，因此我们将这个过程称为 Spring 的“控制反转”。

### 依赖注入（DI）
在了解了 IoC 之后，我们还需要了解另外一个非常重要的概念：依赖注入。

依赖注入（Denpendency Injection，简写为 DI）是 Martin Fowler 在 2004 年在对“控制反转”进行解释时提出的。Martin Fowler 认为“控制反转”一词很晦涩，无法让人很直接的理解“到底是哪里反转了”，因此他建议使用“依赖注入”来代替“控制反转”。

在面向对象中，对象和对象之间是存在一种叫做“依赖”的关系。简单来说，依赖关系就是在一个对象中需要用到另外一个对象，即对象中存在一个属性，该属性是另外一个类的对象。

例如，有一个名为 B 的 Java 类，它的代码如下:
```java
public class B {
    String bid;
    A a;
}
```
从代码可以看出，B 中存在一个 A 类型的对象属性 a，此时我们就可以说 B 的对象依赖于对象 a。而依赖注入就是就是基于这种“依赖关系”而产生的。

我们知道，控制反转核心思想就是由 Spring 负责对象的创建。在对象创建过程中，Spring 会自动根据依赖关系，将它依赖的对象注入到当前对象中，这就是所谓的“依赖注入”。

依赖注入本质上是 Spring Bean 属性注入的一种，只不过这个属性是一个对象属性而已。


### IoC 的工作原理
在 Java 软件开发过程中，系统中的各个对象之间、各个模块之间、软件系统和硬件系统之间，或多或少都存在一定的耦合关系。

若一个系统的耦合度过高，那么就会造成难以维护的问题，但完全没有耦合的代码几乎无法完成任何工作，这是由于几乎所有的功能都需要代码之间相互协作、相互依赖才能完成。因此我们在程序设计时，所秉承的思想一般都是在不影响系统功能的前提下，最大限度的降低耦合度。

IoC 底层通过工厂模式、Java 的反射机制、XML 解析等技术，将代码的耦合度降低到最低限度，其主要步骤如下:
1. 在配置文件（例如 Bean.xml）中，对各个对象以及它们之间的依赖关系进行配置；
2. 我们可以把 IoC 容器当做一个工厂，这个工厂的产品就是 Spring Bean；
3. 容器启动时会加载并解析这些配置文件，得到对象的基本信息以及它们之间的依赖关系；
4. IoC 利用 Java 的反射机制，根据类名生成相应的对象（即 Spring Bean），并根据依赖关系将这个对象注入到依赖它的对象中。

由于对象的基本信息、对象之间的依赖关系都是在配置文件中定义的，并没有在代码中紧密耦合，因此即使对象发生改变，我们也只需要在配置文件中进行修改即可，而无须对 Java 代码进行修改，这就是 Spring IoC 实现解耦的原理。

### IoC 容器的两种实现
IoC 思想基于 IoC 容器实现的，IoC 容器底层其实就是一个 Bean 工厂。Spring 框架为我们提供了两种不同类型 IoC 容器，它们分别是 BeanFactory 和 ApplicationContext。

### BeanFactory
BeanFactory 是 IoC 容器的基本实现，也是 Spring 提供的最简单的 IoC 容器，它提供了 IoC 容器最基本的功能，由 org.springframework.beans.factory.BeanFactory 接口定义。

BeanFactory 采用懒加载（lazy-load）机制，容器在加载配置文件时并不会立刻创建 Java 对象，只有程序中获取（使用）这个对对象时才会创建。

示例 1

下面我们通过一个实例演示，来演示下 BeanFactory 的使用。

1. 在 HelloSpring 项目中，将 MainApp 的代码修改为使用 BeanFactory 获取 HelloWorld 的对象，具体代码如下:
```java
public static void main(String[] args) {
   BeanFactory context = new ClassPathXmlApplicationContext("Beans.xml");
   HelloWorld obj = context.getBean("helloWorld", HelloWorld.class);
   obj.getMessage();
}
```  

2. 运行 MainApp.java，控制台输出如下。
> message : Hello World!   

注意：BeanFactory 是 Spring 内部使用接口，通常情况下不提供给开发人员使用。

### ApplicationContext
ApplicationContext 是 BeanFactory 接口的子接口，是对 BeanFactory 的扩展。ApplicationContext 在 BeanFactory 的基础上增加了许多企业级的功能，例如 AOP（面向切面编程）、国际化、事务支持等。

ApplicationContext 接口有两个常用的实现类，具体如下表:

实现类：`ClassPathXmlApplicationContext`

描述：加载类路径 ClassPath 下指定的 XML 配置文件，并完成 ApplicationContext 的实例化工作	
```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext(String configLocation);
```

实现类：`FileSystemXmlApplicationContext`

描述：加载指定的文件系统路径中指定的 XML 配置文件，并完成 ApplicationContext 的实例化工作	
```java
ApplicationContext applicationContext = new FileSystemXmlApplicationContext(String configLocation);
```

在上表的示例代码中，参数 configLocation 用于指定 Spring 配置文件的名称和位置，如 Beans.xml。

示例 2

下面我们就通过一个实例，来演示 ApplicationContext 的使用。

1. 修改 HelloSpring 项目 MainApp 类中 main() 方法的代码，具体代码如下。
```java
public static void main(String[] args) {
   //使用 FileSystemXmlApplicationContext 加载指定路径下的配置文件 Bean.xml
   BeanFactory context = new FileSystemXmlApplicationContext("E:\\workspace\\HelloSpring\\src\\Beans.xml");
   HelloWorld obj = context.getBean("helloWorld", HelloWorld.class);
   obj.getMessage();
}
```   

2. 运行 MainApp.java，控制台输出如下。
> message : Hello World!   

## Spring Bean加载流程

Spring IOC容器就像是一个生产产品的流水线上的机器，Spring创建出来的Bean就好像是流水线的终点生产出来的一个个精美绝伦的产品。既然是机器，总要先启动，Spring也不例外。因此Bean的加载流程总体上来说可以分为两个阶段：

容器启动阶段
Bean创建阶段
### 一、容器启动阶段：
容器的启动阶段做了很多的预热工作，为后面Bean的实例化做好了充分的准备，我们首先看一下容器的启动阶段都做了哪些预热工作。
- 配置元信息
- BeanDefination
- BeanDefinationReader 
- BeanDefinationRegistry
- BeanFactoryPostProcessor

1. 配置元信息

Spring IOC容器将对象实例的创建与对象实例的使用分离，当业务中需要依赖某个对象，不再依靠我们自己手动创建，只需向Spring要，Spring就会以注入的方式交给我们需要的依赖对象。既然将对象创建的任务交给了Spring，那么Spring就需要知道创建一个对象所需要的一些必要的信息。而这些必要的信息可以是Spring过去支持最完善的xml配置文件，或者是其他形式的例如properties的磁盘文件，也可以是现在主流的注解，甚至是直接的代码硬编码。总之，这些创建对象所需要的必要信息称为配置 `元信息`。
```properties
<bean id="role" class="com.wbg.springxmlbean.entity.Role">
  
    <!-- property元素是定义类的属性，name属性定义的是属性名称 value是值
    相当于：
    Role role=new Role();
    role.setId(1);
    role.setRoleName("高级工程师");
    role.setNote("重要人员");-->

    <property name="id" value="1"/>
    <property name="roleName" value="高级工程师"/>
    <property name="note" value="重要人员"/>
</bean>
```

2. BeanDefination：

在Java世界中，万物皆对象，散落于程序代码各处的注解以及保存在磁盘上的xml或者其他文件等等配置元信息，在内存中总要以一种对象的形式表示，就好比我们活生生的人对应到Java世界中就是一个Person类，而Spring选择在内存中表示这些配置元信息的方式就是BeanDefination，这里我们不会去分析BeanDefination的代码，这里我们只需要知道配置元信息被加载到内存之后是以BeanDefination的形存在的即可。

3. BeanDefinationReader：

Spring是如何看懂这些配置元信息的呢？这个就要靠我们的BeanDefinationReader了。不同的BeanDefinationReader拥有不同的功能:

如果我们要读取xml配置元信息，那么可以使用XmlBeanDefinationReader。

如果我们要读取properties配置文件，那么可以使用PropertiesBeanDefinitionReader加载。

而如果我们要读取注解配置元信息，那么可以使用 AnnotatedBeanDefinitionReader加载。

我们也可以很方便的自定义BeanDefinationReader来自己控制配置元信息的加载。

总的来说，`BeanDefinationReader的作用就是加载配置元信息，并将其转化为内存形式的BeanDefination` ，存在某一个地方，至于这个地方在哪里，不要着急，接着往下看！

4. BeanDefinationRegistry：

执行到这里，Spring已经将存在于各处的配置元信息加载到内存，并转化为BeanDefination的形式，这样我们需要创建某一个对象实例的时候，找到相应的BeanDefination然后创建对象即可。那么我们需要某一个对象的时候，去哪里找到对应的BeanDefination呢？这种通过Bean定义的id找到对象的BeanDefination的对应关系或者说映射关系又是如何保存的呢？这就引出了BeanDefinationRegistry了。

`Spring通过BeanDefinationReader将配置元信息加载到内存生成相应的BeanDefination之后，就将其注册到BeanDefinationRegistry中，BeanDefinationRegistry就是一个存放BeanDefination的大篮子，它也是一种键值对的形式，通过特定的Bean定义的id，映射到相应的BeanDefination。`

5. BeanFactoryPostProcessor：

BeanFactoryPostProcessor是容器启动阶段Spring提供的一个扩展点，主要负责对注册到BeanDefinationRegistry中的一个个的BeanDefination进行一定程度上的修改与替换。例如我们的配置元信息中有些可能会修改的配置信息散落到各处，不够灵活，修改相应配置的时候比较麻烦，这时我们可以使用占位符的方式来配置。例如配置Jdbc的DataSource连接的时候可以这样配置：
```xml
<bean id="dataSource"  
class="org.apache.commons.dbcp.BasicDataSource"  
destroy-method="close">  
<property name="maxIdle" value="${jdbc.maxIdle}"></property>  
<property name="maxActive" value="${jdbc.maxActive}"></property>  
<property name="maxWait" value="${jdbc.maxWait}"></property>  
<property name="minIdle" value="${jdbc.minIdle}"></property>

    <property name="driverClassName"  
        value="${jdbc.driverClassName}">  
    </property>  
    <property name="url" value="${jdbc.url}"></property>  
  
    <property name="username" value="${jdbc.username}"></property>  
    <property name="password" value="${jdbc.password}"></property>  
</bean> 
```

BeanFactoryPostProcessor就会对注册到BeanDefinationRegistry中的BeanDefination做最后的修改，替换$占位符为配置文件中的真实的数据。

至此，整个容器启动阶段就算完成了，容器的启动阶段的最终产物就是注册到BeanDefinationRegistry中的一个个BeanDefination了，这就是Spring为Bean实例化所做的预热的工作。让我们再通过一张图的形式回顾一下容器启动阶段都是搞了什么事吧。


![bean](/image/tech/spring/springbean-defination.png)

## Spring Bean定义
由 Spring IoC 容器管理的对象称为 Bean，Bean 根据 Spring 配置文件中的信息创建。

我们可以把 Spring IoC 容器看作是一个大工厂，Bean 相当于工厂的产品。如果希望这个大工厂生产和管理 Bean，就需要告诉容器需要哪些 Bean，以哪种方式装配。

Spring 配置文件支持两种格式，即 XML 文件格式和 Properties 文件格式。
- Properties 配置文件主要以 key-value 键值对的形式存在，只能赋值，不能进行其他操作，适用于简单的属性配置。
- XML 配置文件采用树形结构，结构清晰，相较于 Properties 文件更加灵活。但是 XML 配置比较繁琐，适用于大型的复杂的项目。

通常情况下，Spring 的配置文件都是使用 XML 格式的。XML 配置文件的根元素是 <beans>，该元素包含了多个子元素 <bean>。每一个 <bean> 元素都定义了一个 Bean，并描述了该 Bean 是如何被装配到 Spring 容器中的。

如Beans.xml 配置文件，代码如下所示：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

  <bean id="helloWorld" class="net.biancheng.c.HelloWorld">
    <property name="message" value="Hello World!"/>
  </bean>
</beans>
```
## Spring Bean属性注入
所谓 Bean 属性注入，简单点说就是将属性注入到 Bean 中的过程，而这属性既可以普通属性，也可以是一个对象（Bean）。

Spring 主要通过以下 2 种方式实现属性注入：
- 构造函数注入
- setter 注入（又称设值注入）


### 构造函数注入
我们可以通过 Bean 的带参构造函数，以实现 Bean 的属性注入。

使用构造函数实现属性注入大致步骤如下：
1. 在 Bean 中添加一个有参构造函数，构造函数内的每一个参数代表一个需要注入的属性；
2. 在 Spring 的 XML 配置文件中，通过 <beans> 及其子元素 <bean> 对 Bean 进行定义；
3. 在 <bean> 元素内使用 <constructor-arg> 元素，对构造函数内的属性进行赋值，Bean 的构造函数内有多少参数，就需要使用多少个 <constructor-arg> 元素。

示例 1

下面我们就通过一个实例，来演示下如何构造函数注入的方式实现属性注入。
- 创建一个名为 Grade、Student 的类，
```java
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;

public class Grade {
    private static final Log LOGGER = LogFactory.getLog(Grade.class);
    private Integer gradeId;
    private String gradeName;

    public Grade(Integer gradeId, String gradeName) {
        LOGGER.info("正在执行 Course 的有参构造方法，参数分别为：gradeId=" + gradeId + ",gradeName=" + gradeName);
        this.gradeId = gradeId;
        this.gradeName = gradeName;
    }

    @Override
    public String toString() {
        return "Grade{" +"gradeId=" + gradeId +", gradeName='" + gradeName + '\'' +'}';
    }
}

```

```java
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
public class Student {
    private static final Log LOGGER = LogFactory.getLog(Student.class);
    private int id;
    private String name;
    private Grade grade;
    public Student(int id, String name, Grade grade) {
        LOGGER.info("正在执行 Course 的有参构造方法，参数分别为：id=" + id + ",name=" + name + ",grade=" + grade);
        this.id = id;
        this.name = name;
        this.grade = grade;
    }
    @Override
    public String toString() {
        return "Student{" +"id=" + id +", name='" + name + '\'' +", grade=" + grade +'}';
    }
}

```

在 src 目录下创建 Spring 配置文件 Beans.xml，配置如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <bean id="student" class="net.biancheng.c.Student">
        <constructor-arg name="id" value="2"></constructor-arg>
        <constructor-arg name="name" value="李四"></constructor-arg>
        <!-- 使用constructor-arg 标签： 依赖 grade -->
        <constructor-arg name="grade" ref="grade"></constructor-arg>
    </bean>

    <bean id="grade" class="net.biancheng.c.Grade">
        <constructor-arg name="gradeId" value="4"></constructor-arg>
        <constructor-arg name="gradeName" value="四年级"></constructor-arg>
    </bean>
</beans>
```

创建一个名为 MainApp 的类，代码如下
```java
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
    private static final Log LOGGER = LogFactory.getLog(MainApp.class);
    public static void main(String[] args) {
        //获取 ApplicationContext 容器
        ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        //获取名为 student 的 Bean
        Student student = context.getBean("student", Student.class);
        //通过日志打印学生信息
        LOGGER.info(student.toString());
    }
}
```

执行 MainApp 中的 main() 方法，控制台输出如下：
```html
十二月 16, 2021 4:38:42 下午 net.biancheng.c.Grade <init>
信息: 正在执行 Course 的有参构造方法，参数分别为：gradeId=4,gradeName=四年级
十二月 16, 2021 4:38:42 下午 net.biancheng.c.Student <init>
信息: 正在执行 Course 的有参构造方法，参数分别为：id=2,name=李四,grade=Grade{gradeId=4, gradeName='四年级'}
十二月 16, 2021 4:38:42 下午 net.biancheng.c.MainApp main
信息: Student{id=2, name='李四', grade=Grade{gradeId=4, gradeName='四年级'}}
```


### setter 注入

我们可以通过 Bean 的 setter 方法，将属性值注入到 Bean 的属性中。

在 Spring 实例化 Bean 的过程中，IoC 容器首先会调用默认的构造方法（无参构造方法）实例化 Bean（Java 对象），然后通过 Java 的反射机制调用这个 Bean 的 setXxx() 方法，将属性值注入到 Bean 中。

使用 setter 注入的方式进行属性注入，大致步骤如下：
1. 在 Bean 中提供一个默认的无参构造函数（在没有其他带参构造函数的情况下，可省略），并为所有需要注入的属性提供一个 setXxx() 方法；
2. 在 Spring 的 XML 配置文件中，使用 <beans> 及其子元素 <bean> 对 Bean 进行定义；
3. 在 <bean> 元素内使用  <property> 元素对各个属性进行赋值。

示例 2

下面，我们就通过一个实例，来演示如何通过 setter 注入的方式实现属性注入，步骤如下:
```java
@Data
public class Student {
  private int id;
  private String name;
  private Grade grade;
}

@Data
public class Grade {
  private Integer gradeId;
  private String gradeName;
}
```

在 src 目录下，修改配置文件 Beans.xml 的内容:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    <bean id="student" class="net.biancheng.c.Student">
        <!--使用 property 元素完成属性注入
            name: 类中的属性名称，例如 id,name
            value: 向属性注入的值 例如 学生的 id 为 1，name 为张三
        -->
        <property name="id" value="1"></property>
        <property name="name" value="张三"></property>
        <property name="grade" ref="grade"></property>
    </bean>

    <bean id="grade" class="net.biancheng.c.Grade">
        <property name="gradeId" value="3"></property>
        <property name="gradeName" value="三年级"></property>
    </bean>
</beans>
```

MainApp.java
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
    public static void main(String[] args) {
      //获取 ApplicationContext 容器
      ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
      //获取名为 student 的 Bean
      Student student = context.getBean("student", Student.class);
      //通过日志打印学生信息
      LOGGER.info(student.toString());
    }
}
```

## Spring Bean作用域
默认情况下，所有的 Spring Bean 都是单例的，也就是说在整个 Spring 应用中， Bean 的实例只有一个。

我们可以在 <bean> 元素中添加 scope 属性来配置 Spring Bean 的作用范围。例如，如果每次获取 Bean 时，都需要一个新的 Bean 实例，那么应该将 Bean 的 scope 属性定义为 prototype，如果 Spring 需要每次都返回一个相同的 Bean 实例，则应将 Bean 的 scope 属性定义为 singleton。

Spring 5 共提供了 6 种 scope 作用域，如下表:


- singleton	
  - 默认值，单例模式，表示在 Spring 容器中只有一个 Bean 实例
- prototype	
  - 原型模式，表示每次通过 Spring 容器获取 Bean 时，容器都会创建一个新的 Bean 实例。
- request	
  - 每次 HTTP 请求，容器都会创建一个 Bean 实例。该作用域只在当前 HTTP Request 内有效。
- session	
  - 同一个 HTTP Session 共享一个 Bean 实例，不同的 Session 使用不同的 Bean 实例。该作用域仅在当前 HTTP Session 内有效。
- application	
  - 同一个 Web 应用共享一个 Bean 实例，该作用域在当前 ServletContext 内有效。 
  - 与 singleton 类似，但 singleton 表示每个 IoC 容器中仅有一个 Bean 实例，而一个 Web 应用中可能会存在多个 IoC 容器，但一个 Web 应用只会有一个 ServletContext，也可以说 `application 才是 Web 应用中货真价实的单例模式`。
- websocket	
  - websocket 的作用域是 WebSocket ，即在整个 WebSocket 中有效。

### singleton
singleton 是 Spring 容器默认的作用域。当 Bean 的作用域为 singleton 时，Spring IoC 容器中只会存在一个共享的 Bean 实例。这个 Bean 实例将存储在高速缓存中，所有对于这个 Bean 的请求和引用，只要 id 与这个 Bean 定义相匹配，都会返回这个缓存中的对象实例。

如果一个 Bean 定义的作用域为 singleton ，那么这个 Bean 就被称为 singleton bean。在 Spring IoC 容器中，singleton bean 是 Bean 的默认创建方式，可以更好地重用对象，节省重复创建对象的开销。

在 Spring 配置文件中，可以使用 <bean> 元素的 scope 属性，将 Bean 的作用域定义成 singleton，其配置方式如下所示：
```xml
 <bean id="..." class="..." scope="singleton"/>
```

创建一个名为 SingletonBean 的类:
```java
@Data
public class SingletonBean {
    private String str;
}
```

在 src 目录下创建 Spring 配置文件 Beans.xml，配置如下:
```java
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    <bean id="singletonBean" class="net.biancheng.c.SingletonBean" scope="singleton">
        <property name="str" value="C语言中文网"></property>
    </bean>
</beans>
```
MainApp 的类，代码如下:
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
  public static void main(String[] args) {
    //获取 ApplicationContext 容器
    ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
    SingletonBean singletonBean = context.getBean("singletonBean", SingletonBean.class);
    SingletonBean singletonBean2 = context.getBean("singletonBean", SingletonBean.class);
    System.out.println(singletonBean);
    System.out.println(singletonBean2);
  }
}
```

运行 MainApp 类中的 main() 方法，控制台输出如下:
```html
net.biancheng.c.SingletonBean@65e2dbf3
net.biancheng.c.SingletonBean@65e2dbf3
```
从控制台的输出可以看出，两次获得的 Bean 实例的地址完全一样，这说明 IoC 容器只创建了一个 singletonBean 实例。由于 singleton 是 Spring IoC 容器的默认作用域，因此即使省略 scope 属性，控制台的输出结果也一样的。

### prototype
如果一个 Bean 定义的作用域为 prototype，那么这个 Bean 就被称为 prototype bean。对于 prototype bean 来说，Spring 容器会在每次请求该 Bean 时，都创建一个新的 Bean 实例。
> 从某种意义上说，Spring  IoC 容器对于 prototype bean 的作用就相当于 Java 的 new 操作符。它只负责 Bean 的创建，至于后续的生命周期管理则都是由客户端代码完成的，详情请参看《Spring Bean 生命周期》。

在 Spring 配置文件中，可以使用 <bean> 元素的 scope 属性将 Bean 的作用域定义成 prototype，其配置方式如下所示：
纯文本复制
```html
<bean id="..." class="..." scope="prototype"/>
```

创建一个名为 SingletonBean 的类:
```java
@Data
public class PrototypeBean {
    private String str;
}
```

在 src 目录下创建 Spring 配置文件 Beans.xml，配置如下:
```java
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    <!--单例模式 singleton-->
    <bean id="singletonBean" class="net.biancheng.c.SingletonBean" scope="singleton">
        <property name="str" value="C语言中文网"></property>
    </bean>
    <!--原型模式 prototype-->
    <bean id="prototypeBean" class="net.biancheng.c.PrototypeBean" scope="prototype">
        <property name="str" value="c.biancheng.net"></property>
    </bean>
</beans>
```
MainApp 的类，代码如下:
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
  public static void main(String[] args) {
    //获取 ApplicationContext 容器
    ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
    PrototypeBean prototypeBean = context.getBean("prototypeBean", PrototypeBean.class);
    PrototypeBean prototypeBean2 = context.getBean("prototypeBean", PrototypeBean.class);
    System.out.println(prototypeBean);
    System.out.println(prototypeBean2);
  }
}
```

运行 MainApp 类中的 main() 方法，控制台输出如下:
```html
net.biancheng.c.PrototypeBean@61f8bee4
net.biancheng.c.PrototypeBean@7b49cea0
```

从运行结果可以看出，两次输出的内容并不相同，这说明在 prototype 作用域下，Spring 容器创建了两个不同的 prototypeBean 实例。


## Refer to
-  [refer to](https://blog.csdn.net/a745233700/article/details/113840727)
-  [referto - spring ](http://c.biancheng.net/spring/module.html)
