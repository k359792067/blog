# Java 反射

标签（空格分隔）： Java 反射

---

### Class类简介
Java程序在运行时，Java运行时系统一直对所有的对象进行所谓的运行时类型标识。这项信息纪录了每个对象所属的类。虚拟机通常使用运行时类型信息选准正确方法去执行，用来保存这些类型信息的类是Class类。Class类封装一个对象和接口运行时的状态，当装载类时，Class类型的对象自动创建。

------

#### 如何获取Class

##### 1.调用Object类的getClass()方法来得到Class对象，这也是最常见的产生Class对象的方法。例如：
```java
MyObject x;
Class c1 = x.getClass();
```
##### 2.使用Class类的中静态forName()方法获得与字符串对应的Class对象。例如：
```java
Class c2=Class.forName("MyObject");
```
##### 3.获取Class类型对象的第三个方法非常简单。如果T是一个Java类型，那么T.class就代表了匹配的类对象。例如：
```java
Class cl1 = Manager.class;
Class cl2 = int.class;
Class cl3 = Double[].class;
```
------

#### Class类的常用方法

##### 1. ***getName()***
一个Class对象描述了一个特定类的属性，Class类中最常用的方法getName以 String 的形式返回此 Class 对象所表示的实体（类、接口、数组类、基本类型或 void）名称。
##### 2.***newInstance()***
Class还有一个有用的方法可以为类创建一个实例，这个方法叫做newInstance()。例如：
```java
x.getClass.newInstance()
```
创建了一个同x一样类型的新实例。newInstance()方法调用默认构造器（无参数构造器）初始化新建对象。
##### 3.***getClassLoader()***
返回该类的类加载器。
##### 4.***getComponentType()***
返回表示数组组件类型的 Class。
##### 5.***getSuperclass()***
返回表示此 Class 所表示的实体（类、接口、基本类型或 void）的超类的 Class。
##### 5.***isArray()***
判定此 Class 对象是否表示一个数组类。

------

#### Class.forName()方法
***Class.forName***是一个静态方法，同样可以用来加载类。该方法有两种形式：***Class.forName(String name, boolean initialize, ClassLoader loader)***和 ***Class.forName(String className)***。第一种形式的参数 `name`表示的是类的全名；`initialize`表示是否初始化类；`loader`表示加载时使用的类加载器。第二种形式则相当于设置了参数`initialize`的值为true，`loader`的值为当前类的类加载器。

------

#### ***newInstance()***方法和***new***关键字区别
从JVM的角度看，我们使用关键字new创建一个类的时候，这个类可以没有被加载。  但是使用newInstance()方法的时候，就必须保证：
1、这个类已经加载；
2、这个类已经连接了。
而完成上面两个步骤的正是Class的静态方法forName()所完成的，这个静态方法调用了启动类加载器，即加载 Java API的那个加载器。  
现在可以看出，newInstance()实际上是把new这个方式分解为两步，即首先调用Class加载方法加载某个类，然后实例化。这样分步的好处是显而易见的。我们可以在调用class的静态加载方法forName时获得更好的灵活性，提供给了一种降耦的手段。

------

 - newInstance: 弱类型。低效率。只能调用无参构造。  
 - new: 强类型。相对高效。能调用任何public构造。

------





