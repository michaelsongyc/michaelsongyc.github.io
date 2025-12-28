# Java随笔

## Java基础概括
### 大图
{{< figure src="/image/tech/java/architecture/JavaBaseArchitecture.png" title="" >}}
### Java
{{< figure src="/image/tech/java/architecture/JavaBaseArchitecture_java.png" title="" >}}
### 设计模式 && 算法
{{< figure src="/image/tech/java/architecture/JavaBaseArchitecture_design.png" title="" >}}
### 框架 && spring
{{< figure src="/image/tech/java/architecture/JavaBaseArchitecture_11.png" title="" >}}
### 中间件 && 其他
{{< figure src="/image/tech/java/architecture/JavaBaseArchitecture_2.png" title="" >}}

## Java

### Document 
- [Oracle Java tutorial](https://docs.oracle.com/javase/tutorial/java/index.html)

### List

```java
public class ArrayList<E> extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable{}
```
```java
public class LinkedList<E> extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable{}
```
```java
public class CopyOnWriteArrayList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {}
```

>      List list = Collections.synchronizedList(new ArrayList(...));


### Java equals() and hashCode() 
- [refer](https://www.digitalocean.com/community/tutorials/java-equals-hashcode)
- [Java HashMap](https://www.digitalocean.com/community/tutorials/java-hashmap#how-hashmap-works-in-java)

#### equals()
Object class defined equals() method like this:

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```
According to java documentation of equals() method, any implementation should adhere to following principles.
- For any object x, x.equals(x) should return true.
- For any two object x and y, x.equals(y) should return true if and only if y.equals(x) returns true.
- For multiple objects x, y, and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) should return true.
- Multiple invocations of x.equals(y) should return same result, unless any of the object properties is modified that is being used in the equals() method implementation.
- Object class equals() method implementation returns true only when both the references are pointing to same object.

#### hashCode()
Java Object hashCode() is a native method and returns the integer hash code value of the object. The general contract of hashCode() method is:
- Multiple invocations of hashCode() should return the same integer value, unless the object property is modified that is being used in the equals() method.
- `An object hash code value can change in multiple executions of the same application.`
- `If two objects are equal according to equals() method, then their hash code must be same.`
- `If two objects are unequal according to equals() method, their hash code are not required to be different.` Their hash code value may or may-not be equal.

#### Importance of equals() and hashCode() method
Java hashCode() and equals() method are used in Hash table based implementations in java for storing and retrieving data. I have explained it in detail at `How HashMap works in java?` The implementation of equals() and hashCode() should follow these rules.
- If o1.equals(o2), then o1.hashCode() == o2.hashCode() should always be true.
- If o1.hashCode() == o2.hashCode is true, it doesn’t mean that o1.equals(o2) will be true.

#### When to override equals() and hashCode() methods?
`When we override equals() method`, it’s almost necessary to override the hashCode() method too so that their contract is not violated by our implementation. Note that your program will not throw any exceptions if the equals() and hashCode() contract is violated, if you are not planning to use the class as Hash table key, then it will not create any problem. If you are planning to use a class as Hash table key, then it’s must to override both equals() and hashCode() methods. Let’s see what happens when we rely on default implementation of equals() and hashCode() methods and use a custom class as HashMap key.

```java
public class DataKey {
    private String name;
    private int id;

    // getter and setter methods
  
    @Override
    public String toString() {
        return "DataKey [name=" + name + ", id=" + id + "]";
    }
}
```

```java
public class HashingTest {

    public static void main(String[] args) {
        Map<DataKey, Integer> hm = getAllData();

        DataKey dk = new DataKey();
        dk.setId(1);
        dk.setName("Pankaj");
        log.info("{}", dk.hashCode());

        Integer value = hm.get(dk);
        log.info("{}", value);
    }

    private static Map<DataKey, Integer> getAllData() {
        Map<DataKey, Integer> hm = new HashMap<>();

        DataKey dk = new DataKey();
        dk.setId(1);
        dk.setName("Pankaj");

        log.info("{}", dk.hashCode());
        hm.put(dk, 10);
        return hm;
    }
}
```
When we run above program, it will print null. It’s because Object hashCode() method is used to find the bucket to look for the key. Since we don’t have access to the HashMap keys and we are creating the key again to retrieve the data, you will notice that hash code values of both the objects are different and hence value is not found.

We can also use Project Lombok to auto generate equals and hashCode method implementations.
```java
import lombok.Data;

@Data
public class DataKey {
    private String name;
    private int id;

    @Override
    public String toString() {
        return "DataKey [name=" + name + ", id=" + id + "]";
    }
}
```
#### What is Hash Collision ??

In very simple terms, Java Hash table implementations uses following logic for get and put operations.

1. First identify the “Bucket” to use using the “key” hash code.
2. If there are no objects present in the bucket with same hash code, then add the object for put operation and return null for get operation.
3. If there are other objects in the bucket with same hash code, then “key” equals method comes into play. 
   - If equals() return true and it’s a put operation, then object value is overridden.
   - If equals() return false and it’s a put operation, then new entry is added to the bucket.
   - If equals() return true and it’s a get operation, then object value is returned.
   - If equals() return false and it’s a get operation, then null is returned.

Below image shows a bucket items of HashMap and how their equals() and hashCode() are related.

{{< figure src="/image/tech/java/base/hashCode.png" title="desc" >}}

The phenomenon when two keys have same hash code is called hash collision. If hashCode() method is not implemented properly, there will be higher number of hash collision and map entries will not be properly distributed causing slowness in the get and put operations. This is the reason for prime number usage in generating hash code so that map entries are properly distributed across all the buckets.

**What if we don’t implement both hashCode() and equals()?**

We have already seen above that if hashCode() is not implemented, we won’t be able to retrieve the value because HashMap use hash code to find the bucket to look for the entry. If we only use hashCode() and don’t implement equals() then also value will be not retrieved because equals() method will return false.

**Best Practices for implementing equals() and hashCode() method**
- Use same properties in both equals() and hashCode() method implementations, so that their contract doesn’t violate when any properties is updated.
- It’s better to use immutable objects as Hash table key so that we can cache the hash code rather than calculating it on every call. That’s why String is a good candidate for Hash table key because it’s immutable and cache the hash code value.
- Implement hashCode() method so that least number of hash collision occurs and entries are evenly distributed across all the buckets.


### Project Lombok
- [refer](https://www.digitalocean.com/community/tutorials/java-project-lombok)

### Serialization and Deserialization

`Serialization` is the conversion of the state of an object into a byte stream;`deserialization` does the opposite. Stated differently, serialization is the conversion of a Java object into a static stream (sequence) of bytes, which we can then save to a database or transfer over a network.

- When should I implement Serializable interface?
  - implement the Serializable interface when you need to store a copy of the object, send them to another process which runs on the same system or over the network.

- Why do we do that?
  - Because you want to store or send an object.

- Does it give any advantages or security?
  - It makes storing and sending objects easy. It has nothing to do with security.

**What to do instead**

Instead, use a serialization scheme that you can explicitly control. Such as Protocol Buffers, JSON, XML, or your own custom scheme.

[refer](https://www.baeldung.com/java-serialization)
[refer2](https://zhuanlan.zhihu.com/p/66210653)
### Java 泛型（generics）
> Java Genrics is one of the most important features introduced in Java 5.

What does the <E> syntax mean in Java?

Here `<E>` denotes the type parameter of Node class .The type parameter defines that it can refer to any type (like String, Integer, Employee etc.). Java generics have type parameter naming conventions like following:

Java Generic Type Naming convention helps us understanding code easily and having a naming convention is one of the best practices of Java programming language. So generics also comes with its own naming conventions. Usually, type parameter names are single, uppercase letters to make it easily distinguishable from java variables. The most commonly used type parameter names are:
```html
E - Element (used extensively by the Java Collections Framework, for example ArrayList, Set etc.)
K - Key (Used in Map)
N - Number
T - Type
V - Value (Used in Map)
S,U,V etc. - 2nd, 3rd, 4th types
？- 通配符泛型
```

For example take the following scenerio

```java
public class Node<E>{
    E elem;
    Node<E> next, previous;
}
class Test{
    Node<String> obj = new Node<>();
}
```

You can use generics with Interface,method and constructor too.

For more about generics visit these: 
- [https://www.javatpoint.com/generics-in-java](https://www.javatpoint.com/generics-in-java)


1. Generics in Java
 
   Generics was added in Java 5 to provide `compile-time type checking` and removing risk of `ClassCastException` that was common while working with collection classes. The whole collection framework was re-written to use generics for type-safety. Let’s see how generics help us using collection classes safely.

```java
List list = new ArrayList();
list.add("abc");
list.add(new Integer(5)); //OK

for(Object obj : list){
   //type casting leading to ClassCastException at runtime
   String str=(String) obj;
}
```
Above code compiles fine but throws ClassCastException at runtime because we are trying to cast Object in the list to String whereas one of the element is of type Integer. After Java 5, we use collection classes like below.

```java
List<String> list1 = new ArrayList<String>(); // java 7 ? List<String> list1 = new ArrayList<>();
list1.add("abc");
//list1.add(new Integer(5)); //compiler error

for(String str : list1){
    //no type casting needed, avoids ClassCastException
}
```
Notice that at the time of list creation, we have specified that the type of elements in the list will be String. So if we try to add any other type of object in the list, the program will throw compile-time error. Also notice that in for loop, we don’t need typecasting of the element in the list, hence removing the ClassCastException at runtime.
 
- [https://www.journaldev.com/1663/java-generics-example-method-class-interface](https://www.journaldev.com/1663/java-generics-example-method-class-interface)

1) Why do we use Generics in Java?
Generics provide strong compile-time type checking and reduces risk of ClassCastException and explicit casting of objects.

2) What is T in Generics?
We use <T> to create a generic class, interface, and method. The T is replaced with the actual type when we use it.

3) How does Generics work in Java?
Generic code ensures type safety. The compiler uses type-erasure to remove all type parameters at the compile time to reduce the overload at runtime.

- [refer](https://docs.oracle.com/javase/tutorial/java/generics/why.html)
- [reter 2](https://www.runoob.com/java/java-generics.html)
- [泛型通配符](https://juejin.cn/post/6844903917835419661)

### Java Reflection
- [refer](https://www.digitalocean.com/community/tutorials/java-reflection-example-tutorial)

Reflection in Java is a very powerful concept and it’s of little use in normal programming but it’s the backbone for most of the Java, J2EE frameworks. Some of the frameworks that use java reflection are:

- JUnit - uses reflection to parse @Test annotation to get the test methods and then invoke it.
- Spring - dependency injection, read more at Spring Dependency Injection
- Tomcat web container to forward the request to correct module by parsing their web.xml files and request URI.

The list is endless and they all use java reflection because all these frameworks have no knowledge and access of user defined classes, interfaces, their methods etc. We should not use reflection in normal programming where we already have access to the classes and interfaces because of following drawbacks.

- Poor Performance - Since java reflection resolve the types dynamically, it involves processing like scanning the classpath to find the class to load, causing slow performance.
- Security Restrictions - Reflection requires runtime permissions that might not be available for system running under security manager. This can cause you application to fail at runtime because of security manager.
- Security Issues - Using reflection we can access part of code that we are not supposed to access, for example we can access private fields of a class and change it’s value. This can be a serious security threat and cause your application to behave abnormally.
- High Maintenance - Reflection code is hard to understand and debug, also any issues with the code can’t be found at compile time because the classes might not be available, making it less flexible and hard to maintain.

### Java Annotations
- [refer](https://www.digitalocean.com/community/tutorials/java-annotations)

Java 1.5 introduced annotations and now it’s heavily used in Java EE frameworks like Hibernate, Jersey, and Spring. Java Annotation is metadata about the program embedded in the program itself. It can be parsed by the annotation parsing tool or by the compiler. We can also specify annotation availability to either compile time only or till runtime.

```java
import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Documented
@Target(ElementType.METHOD)
@Inherited
@Retention(RetentionPolicy.RUNTIME)
public @interface MethodInfo{
	String author() default "Pankaj";
	String date();
	int revision() default 1;
	String comments();
}

``` 

Meta annotations in java

There are five types of meta annotations:

- @Documented 
  - indicates that elements using this annotation should be documented by javadoc and similar tools. This type should be used to annotate the declarations of types whose annotations affect the use of annotated elements by their clients. If a type declaration is annotated with Documented, its annotations become part of the public API of the annotated elements.
- `@Target` 
  - indicates the kinds of program element to which an annotation type is applicable. Some possible values are TYPE, METHOD, CONSTRUCTOR, FIELD etc. If Target meta-annotation is not present, then annotation can be used on any program element.
- @Inherited 
  - indicates that an annotation type is automatically inherited. If user queries the annotation type on a class declaration, and the class declaration has no annotation for this type, then the class’s superclass will automatically be queried for the annotation type. This process will be repeated until an annotation for this type is found, or the top of the class hierarchy (Object) is reached.
- `@Retention` 
  - indicates how long annotations with the annotated type are to be retained. It takes RetentionPolicy argument whose Possible values are SOURCE, CLASS and RUNTIME
- @Repeatable
  - used to indicate that the annotation type whose declaration it annotates is repeatable.

#### Java Annotations Example
Let’s see a java example showing the use of built-in annotations in java as well as the use of custom annotation created by us in the above example.
```java
import java.io.FileNotFoundException;
import java.util.ArrayList;
import java.util.List;

public class AnnotationExample {

	public static void main(String[] args) {
	}

	@Override
	@MethodInfo(author = "Pankaj", comments = "Main method", date = "Nov 17 2012", revision = 1)
	public String toString() {
		return "Overriden toString method";
	}

	@Deprecated
	@MethodInfo(comments = "deprecated method", date = "Nov 17 2012")
	public static void oldMethod() {
		System.out.println("old method, don't use it.");
	}

	@SuppressWarnings({ "unchecked", "deprecation" })
	@MethodInfo(author = "Pankaj", comments = "Main method", date = "Nov 17 2012", revision = 10)
	public static void genericsTest() throws FileNotFoundException {
		List l = new ArrayList();
		l.add("abc");
		oldMethod();
	}
}
```         
I believe above java annotation example is self-explanatory and showing the use of annotations in different cases.

#### Java Annotations Parsing
We will use Reflection to parse java annotations from a class. Please note that Annotation Retention Policy should be RUNTIME otherwise its information will not be available at runtime and we won’t be able to fetch any data from it.
```java
import java.lang.annotation.Annotation;
import java.lang.reflect.Method;

public class AnnotationParsing {

	public static void main(String[] args) {
		try {
			for (Method method : AnnotationParsing.class.getClassLoader()
					.loadClass(("com.michael.annotations.AnnotationExample")).getMethods()) {
				// checks if MethodInfo annotation is present for the method
				if (method.isAnnotationPresent(com.michael.annotations.MethodInfo.class)) {
					try {
						// iterates all the annotations available in the method
						for (Annotation anno : method.getDeclaredAnnotations()) {
							System.out.println("Annotation in Method '" + method + "' : " + anno);
						}
						MethodInfo methodAnno = method.getAnnotation(MethodInfo.class);
						if (methodAnno.revision() == 1) {
							System.out.println("Method with revision no 1 = " + method);
						}

					} catch (Throwable ex) {
						ex.printStackTrace();
					}
				}
			}
		} catch (SecurityException | ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
}
```
Reflection API is very powerful and used widely in Java, J2EE frameworks like Spring, Hibernate, JUnit, check out Reflection in Java. That’s all for the java annotations example tutorial, I hope you learned something from it. 


### Map
### HashMap
- [refer](https://www.digitalocean.com/community/tutorials/java-hashmap)

Java HashMap is one of the most popular Collection classes in java. Java HashMap is Hash table based implementation. HashMap in java extends AbstractMap class that implements Map interface.
```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {}
```

Some of the important points about HashMap in Java are;

- Java HashMap allows null key and null values.
- HashMap is not an ordered collection. You can iterate over HashMap entries through keys set but they are not guaranteed to be in the order of their addition to the HashMap.
- HashMap is almost similar to Hashtable except that it’s unsynchronized and allows null key and values.
- HashMap uses it’s inner class Node<K,V> for storing map entries.
- HashMap stores entries into multiple singly linked lists, called buckets or bins. Default number of bins is 16 and it’s always power of 2.
- HashMap uses hashCode() and equals() methods on keys for get and put operations. So HashMap key object should provide good implementation of these methods. This is the reason immutable classes are better suitable for keys, for example String and Interger.
- Java HashMap is not thread safe, for multithreaded environment you should use ConcurrentHashMap class or get synchronized map using Collections.synchronizedMap() method.

#### Java HashMap Constructors
Java HashMap provides four constructors.
- public HashMap(): Most commonly used HashMap constructor. This constructor will create an empty HashMap with default initial capacity 16 and load factor 0.75
- public HashMap(int initialCapacity): This HashMap constructor is used to specify the initial capacity and 0.75 load factor. This is useful in avoiding rehashing if you know the number of mappings to be stored in the HashMap.
- public HashMap(int initialCapacity, float loadFactor): This HashMap constructor will create an empty HashMap with specified initial capacity and load factor. You can use this if you know the maximum number of mappings to be stored in HashMap. In common scenarios you should avoid this because load factor 0.75 offers a good tradeoff between space and time cost.
- public HashMap(Map<? extends K, ? extends V> m): Creates a Map having same mappings as the specified map and with load factor 0.75

#### How HashMap works in java?
&emsp;&emsp;HashMap in java use it’s inner class Node<K,V> for storing mappings. HashMap works on hashing algorithm and uses hashCode() and equals() method on key for get and put operations. HashMap use singly linked list to store elements, these are called bins or buckets. 

&emsp;&emsp; When we call put method, hashCode of key is used to determine the bucket that will be used to store the mapping. Once bucket is identified, hashCode is used to check if there is already a key with same hashCode or not. If there is an existing key with same hashCode, then equals() method is used on key. If equals returns true, then value is overwritten, otherwise a new mapping is made to this singly linked list bucket. If there is no key with same hashCode then mapping is inserted into the bucket.

&emsp;&emsp;For HashMap get operation, again key hashCode is used to determine the bucket to look for the value. After bucket is identified, entries are traversed to find out the Entry using hashCode and equals method. If match is found, value is returned otherwise null is returned. There are much more things involved such as hashing algorithm to get the bucket for the key, rehashing of mappings etc. But for our working, just remember that HashMap operations work on Key and good implementation of hashCode and equals method is required to avoid unwanted behaviour.

&emsp;&emsp;Below image shows the explanation of get and put operations.

### java移位运算
- `左乘`、`右除`
  - `左移`1位相当于乘以2的1次方，左移n位就相当于乘以 2的n次方。
  - `右移`1位相当于除以2的1次方，右移n位就相当于除以 2的n次方。
- demo
```java

10 << 1 = 10 * 2 = 20
10 << 3 = 10 * (2 * 2 * 2) = 80
        
6 >> 1 = 6/2 = 3
6 >> 2 = 6/(2 * 2) = 1
6 >> 3 = 6/(2 * 2 * 2) = 0
```

## JVM
### 图例
{{< figure src="/image/tech/java/jvm/JVM-0523.png" title="JVM" >}}

## todo

### java 链表
- [java ListNode 链表 ](https://www.cnblogs.com/easyidea/p/13371863.html)
