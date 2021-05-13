# 对Java（Reflection）反射机制的理解

## Definition

JAVA反射机制是在**运行状态**中，对于**任意一个类**，都能够知道这个类的所有属性和方法；对于**任意一个对象**，都能够调用它的任意一个方法和属性。
这种动**态获取的信息**以及**动态调用对象**的方法的功能称为java语言的反射机制。

我们面向对象通常是通过类实例化创建对象，但是反射机制能使得我们去使用一个实例对象，反过来得到它的类的信息。（当然，这个过程也是Class运行时类实例化的过程）

类加载过程：javac.exe进行代码编译讲.java 文件编译成.class文件；运行java.exe进行加载（在JVM类加载器中完成）.class文件到内存，此.class文件成为一个运行时类，是Class类的一个实例对象，存储在缓存区。

## 类加载过程

* 类的加载（类加载器将类的.class文件读入内存，并为它创建一个java.lang.Class的对象）
* 类的链接（将类的二进制数据合并到JRE中）
* 类的初始化（JVM负责对类进行初始化）

**类加载器作用**：.getResourceAsStream()获取输入流，代替了FileInputStream 与Properties配置文件类公用，读取配置数据

## Java动态性

* 反射机制
* 动态编译
* 动态执行javascript代码
* 动态字节码操作

## 获取Class对象

* 通过**Class静态方法**获取 Class.forName(className-类的路径)
  * 多用于配置文件，将类名定义在配置文件中。读取文件，加载类
* 调用运行时**类本身**的.class属性
  * 多用于对象作为参数传递的情况
* 通过运行时类的**对象**的获取`<obj>.getClass`   getClass()方法定义在Object类中
  * 多用于对象的获取字节码的方式
* 通过类加载器classloder.loadclass(className)

同一个字节码文件（*.class）在一次程序运行过程中，只会被加载一次，不论通过哪一种方式获取的Class对象都是同一个

## 反射机制的常见作用

* 动态加载类、动态获取类的信息（属性、方法、构造器）(Class对象功能)
  * 获取类名
    * String    getName()//获取包名+类名
    * String    getSimpleName()//获取类名
  * 获得属性
    * Field   getField(String name)//获取public属性
    * Field[] getFields()//获取public属性
    * Field   getDeclaredFields()//所有属性
    * Field[] getDeclaredFields(String name)//获取指定name属性
  * 获取方法(类比于Fields)**方法包括继承自父类的方法**
    * Method    getMethod​(String name, Class<?>... parameterTypes)
    * Method[]  getMethods​()
    * Method    getDeclaredMethod​(String name, Class<?>... parameterTypes)
    * Method[]  getDeclaredMethods​()
  * 获得构造器（同上）
    * Constructor`<T>`    getConstructor​(Class<?>... parameterTypes)
    * Constructor<?>[]    getConstructors​() //Constructor<?>==Constructor `<Object>`
    * Constructor`<T>`    getDeclaredConstructor​(Class<?>... parameterTypes)
    * Constructor<?>[]    getDeclaredConstructors​()

* 动态构造对象
  * Class静态方法直接构造//通过反射API调用构造方法，构造对象
  `T   newInstance​()`

  * 获取指定构造器对象后调用构造
  Constructor `<objName>` constructor= ClassObj.getDeclaredConstructor(parameterType.class...)
  T constructor =  obj.newInstance(parameterType);  

* 动态调用类和对象的任意方法、构造器
  //通过反射API调用普通方法
  Method method = ClassObj.getDeclaredMethod("methodName",className.class)
  T t=method.invoke(classObj,parameter)//=classObj.method()

* 动态调用和处理属性
  //通过反射API操作属性
  Field field  = ClassObj.getDeclaredField("fieldName");
  Object value = field.get(Object obj)//get获取成员变量的值
  field.setAccessible(true);//取消安全检查，直接访问
  field.set(classObj,parameter);//set设置成员变量的值

* 获取泛型信息
Java采用**泛型擦除机制**引入泛型，Java中的泛型仅仅是给编译器javac使用，确保**数据的安全性和免去强制类型转换**的麻烦，编译完成后擦除所有泛型有关的类型。
解决方案：
  * ParameterizedType:表示一种参数化的类型，比如Collection`<String>`
  * GenericArrayType:表示一种元素类型是参数化类型或类型变量的数组类型
  * TypeVariable:是各种类型的公共父接口
  * WildcardType:代表一种通配符类型表达式，比如？  ？extends Number   ? super Integer

* 处理注解

## 反射机制的案例实现

* 目标：写一个“框架”，在不能改变该类的任何代码的前提下，可以帮我们创建任意类的对象，并且执行其中的任意方法。
* 实现依靠的技术：
  1. 配置文件
  2. 反射
* 步骤
  1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件中
  2. 在程序中加载读取配置文件
  3. 使用反射技术来加载类文件进内存
  4. 创建对象
  5. 执行方法

```java
//1.加载配置文件
    //1.1创建Properties对象
    Properties pro = new Properties();
    //1.2加载配置文件，转换为一个集合
    //1.2.1获取class目录下的配置文件
    ClassLoader classLoader = ReflectTest.class.getClassLoader();
    InputStream is = classLoader.getResourceAsStream("pro.properties");
    pro.load(is);

//2.获取配置文件中定义的数据
String className = pro.getProperty("className");
String methodName = pro.getProperty("methodName");


//3.加载该类进内存
Class cls = Class.forName(className);
//4.创建对象
Object obj = cls.newInstance();
//5.获取方法对象
Method method = cls.getMethod(methodName);
//6.执行方法
method.invoke(obj);
```

## 反射机制性能问题

setAccessible：启用和禁用安全检查的开关。禁止安全检查，可以提高反射的运行速度。
反射比普通运行效率慢30倍数，关闭安全检查可以令反射提高4倍效率
Class对象的Field可以直接获取，获取具体对象Field部分对应的值需要忽略访问权限修饰符的安全检查----暴力反射
field.setAccessible(True);
