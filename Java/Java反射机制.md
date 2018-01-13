## 一、 反射概述

### 1.1 反射机制定义

Java反射机制是在运行状态中，对于任意一个类，都能够知道这个类中的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

### 1.2 反射机制的功能

- 在运行时判断任意一个对象所属的类。
- 在运行时构造任意一个类的对象。
- 在运行时判断任意一个类所具有的成员变量和方法。
- 在运行时调用任意一个对象的方法。
- 生成动态代理。

## 二、 通过Java反射查看类信息

### 2.1 获得Class对象

每个类被加载之后，系统就会为该类生成一个对应的Class对象。通过该Class对象就可以访问到JVM中的这个类。

获取方式：

1. 使用Class类的forName(String clazzName)静态方法。该方法需要传入字符串参数，该字符串参数的值是某个类的全限定名（必须添加完整包名）。

```
Class class1 = Class.forName("com.com.lvqingyang.interview.Person");
```

2. 调用某个类的class属性来获取该类对应的Class对象。

```
class1 = Person.class;
```

3. 调用某个对象的getClass()方法。

```
Person person = new Person();
Class<?> class1 = person.getClass();
```

### 2.2 获取class对象的属性、方法、构造函数等

1. 获取class对象的成员变量

```
Field[] allFields = class1.getDeclaredFields();//获取class对象的所有属性
Field[] publicFields = class1.getFields();//获取class对象的public属性
Field ageField = class1.getDeclaredField("age");//获取class指定属性
Field desField = class1.getField("des");//获取class指定的public属性
```

2. 获取class对象的方法

```
Method[] methods = class1.getDeclaredMethods();//获取class对象的所有声明方法
Method[] allMethods = class1.getMethods();//获取class对象的所有public方法 包括父类的方法
Method method = class1.getMethod("info", String.class);//返回次Class对象对应类的、带指定形参列表的public方法
Method declaredMethod = class1.getDeclaredMethod("info", String.class);//返回次Class对象对应类的、带指定形参列表的方法

```

3. 获取class对象的构造函数

```
Constructor<?>[] allConstructors = class1.getDeclaredConstructors();//获取class对象的所有声明构造函数
Constructor<?>[] publicConstructors = class1.getConstructors();//获取class对象public构造函数
Constructor<?> constructor = class1.getDeclaredConstructor(String.class);//获取指定声明构造函数
Constructor publicConstructor = class1.getConstructor(String.class);//获取指定声明的public构造函数
```



4. 其他方法

```
Annotation[] annotations = (Annotation[]) class1.getAnnotations();//获取class对象的所有注解
Annotation annotation = (Annotation) class1.getAnnotation(Deprecated.class);//获取class对象指定注解
Type genericSuperclass = class1.getGenericSuperclass();//获取class对象的直接超类的 Type
Type[] interfaceTypes = class1.getGenericInterfaces();//获取class对象的所有接口的type集合
```



## 三、通过Java反射生成并操作对象 

### 3.1 生成类的实例对象

- 使用Class对象的newInstance()方法来创建该Class对象对应类的实例。这种方式要求该Class对象的对应类有默认构造器，而执行newInstance()方法时实际上是利用默认构造器来创建该类的实例。

```
Object obj = class1.newInstance();
```

- 先使用Class对象获取指定的Constructor对象，再调用Constructor对象的newInstance()方法来创建该Class对象对应类的实例。通过这种方式可以选择使用指定的构造器来创建实例。

```
Constructor<?> constructor = class1.getDeclaredConstructor(String.class);//获取指定声明构造函数
obj = constructor.newInstance("hello");

```

### 3.2 调用类的方法

调用Method对象中的 Object invoke(Object obj, Object... args) 方法。第一个参数对应调用该方法的实例对象，第二个参数对应该方法的参数。

```
// 生成新的对象：用newInstance()方法
Object obj = class1.newInstance();
//首先需要获得与该方法对应的Method对象
Method method = class1.getDeclaredMethod("setAge", int.class);
//调用指定的函数并传递参数
method.invoke(obj, 28);
```

要调用某个对象的private方法，则要先调用Method对象的`setAccessible(boolean flag)`方法。值为true，指示该Method在使用时应该取消Java语言的访问权限检查；false要实施Java语言的访问权限检查

### 3.3 访问成员变量值

- 读取成员变量值

  基本变量：getXXX(Object obj)  获取obj对象的该成员变量的值，XXX对应8种基本类型。

  引用类型：get(Object obj) 

- 设置成员变量值

  基本变量：setXXX(Object obj, XXX val)  设置obj对象的该成员变量的值，XXX对应8种基本类型。

  引用类型：setXXX(Object obj, Object val)
```
//生成新的对象：用newInstance()方法
Object obj = class1.newInstance();
//获取age成员变量
Field field = class1.getField("age");
//将obj对象的age的值设置为10
field.setInt(obj, 10);
//获取obj对象的age的值
field.getInt(obj);
```

  ​

