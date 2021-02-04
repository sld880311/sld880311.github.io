---
title: Java系统化学习之反射
tags:
  - Java
  - 反射
categories:
  - Java
date: 2021-02-04 17:18:34
---
## 概述

### 知识点

<div align=center>

![反射知识点](Java系统化学习之反射/1587523711269.png)

</div>

<!--more-->

### 动态语言

动态语言，是指程序在运行时可以改变其结构：新的函数可以引进，已有的函数可以被删除等结构上的变化。比如常见的 JavaScript 就是动态语言，除此之外 Ruby,Python 等也属于动态语言，而 C、C++则不属于动态语言。从反射角度说 JAVA 属于半动态语言。在Java中如果想获取到运行中对象的结构则需要引入反射的概念。

### 定义

在 Java 中的反射机制是指在运行状态中，对于任意一个类都能够知道这个类所有的属性和方法；并且对于任意一个对象，都能够调用它的任意一个方法；这种动态获取信息以及动态调用对象方法的功能成为 Java 语言的反射机制。

#### 场景

在java中包括两种时期：编译期和运行期，对应的类型就是编译时类型和运行时类型。编译时类型由声明时的对象决定，运行时类型则由实际的对象类型决定（**主要表现为行为，对于成员变量则编译时确认**）。比如：

```java
//其中编译时类型为 Person，运行时类型为 Man。  
Person p=new Man();
```

由于编译时类型无法获取具体方法且程序运行过程中可能会接收外部传入的对象该对象的编译时类型为 Object,但是程序有需要调用该对象的运行时类型的方法。为了解决这些问题，程序需要在运行时发现对象和类的真实信息。然而，如果编译时根本无法预知该对象和类属于哪些类，程序只能依靠运行时信息来发现该对象和类的真实信息，此时就必须使用到反射了。

## 使用

### API

反射API用来生成JVM中的类、接口或则对象的信息。  

#### Class

反射的核心类，可以获取类的属性，方法等信息。class的生成已经加载过程会在JVM章节中进行详细说明。

##### RTTI（Run-Time Type Identification）运行时类型识别

<font color="red">RTTI（Run-Time Type Identification）运行时类型识别</font>，其作用是在运行时识别一个对象的类型和类的信息，这里分两种：传统的”RRTI”,它假定我们在编译期已知道了所有类型(在没有反射机制创建和使用类对象时，一般都是编译期已确定其类型，如new对象时该类必须已定义好)，另外一种是反射机制，它允许我们在运行时发现和使用类型的信息。在Java中用来表示运行时类型信息的对应类就是Class类，Class类也是一个实实在在的类，存在于JDK的java.lang包中.

##### Class的特点

- 在java中万事万物都是对象的概念，使用Class类表示类的对象信息
- 通过关键字class标识的类，在内存中都会有一个与之对应的Class对象，用来描述具体的类型信息
- Class类的构造函数是私有的，因此对应Class对象只能有JVM创建和加载
- Class类的对象作用是运行时提供或获得某个对象的类型信息（反射的来源）
- 类加载器在类被第一次静态调用(比如一个静态方法，一个静态代码块或者new关键字调用构造器，注意contructors其实都是静态的)时会把那个对应的Class对象加载到内存中。

##### 类与对象的关系

<div align=center>

![类与对象的关系](Java系统化学习之反射/1587536833908.png)

</div>

#### Field

Java.lang.reflec 包中的类，表示类的成员变量，可以用来获取和设置类之中的属性值。

##### class中获取field

| 方法返回值 | 方法名称                      | 方法说明                                                                    |
|------------|-------------------------------|-----------------------------------------------------------------------------|
| Field      | getDeclaredField(String name) | 获取指定name名称的(包含private修饰的)字段，不包括继承的字段                 |
| Field[]    | getDeclaredField()            | 获取Class对象所表示的类或接口的所有(包含private修饰的)字段,不包括继承的字段 |
| Field      | getField(String name)         | 获取指定name名称、具有public修饰的字段，包含继承字段                        |
| Field[]    | getField()                    | 获取修饰符为public的字段，包含继承字段                                      |

##### field中常用方法

| 方法返回值 | 方法名称                      | 方法说明                                                               |
|------------|-------------------------------|------------------------------------------------------------------------|
| void       | set(Object obj, Object value) | 将指定对象变量上此 Field 对象表示的字段设置为指定的新值。              |
| Object     | get(Object obj)               | 返回指定对象上此 Field 表示的字段的值                                  |
| Class<?>   | getType()                     | 返回一个 Class 对象，它标识了此Field 对象所表示字段的声明类型。        |
| boolean    | isEnumConstant()              | 如果此字段表示枚举类型的元素则返回 true；否则返回 false                |
| String     | toGenericString()             | 返回一个描述此 Field（包括其一般类型）的字符串                         |
| String     | getName()                     | 返回此 Field 对象表示的字段的名称                                      |
| Class<?>   | getDeclaringClass()           | 返回表示类或接口的 Class 对象，该类或接口声明由此 Field 对象表示的字段 |
| void       | setAccessible(boolean flag)   | 将此对象的 accessible 标志设置为指示的布尔值,即设置其可访问性          |

#### Method

Java.lang.reflec 包中的类，表示类的方法，它可以用来获取类中的方法信息或者执行方法。

##### class中的method方法

| 方法返回值 | 方法名称                                                   | 方法说明                                                                                                                                                              |
|------------|------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Method     | getDeclaredMethod(String name, Class<?>... parameterTypes) | 返回一个指定参数的Method对象，该对象反映此 Class 对象所表示的类或接口的指定已声明方法。                                                                               |
| Method[]   | getDeclaredMethod()                                        | 返回 Method 对象的一个数组，这些对象反映此 Class 对象表示的类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。                     |
| Method     | getMethod(String name, Class<?>... parameterTypes)         | 返回一个 Method 对象，它反映此 Class 对象所表示的类或接口的指定公共成员方法。                                                                                         |
| Method[]   | getMethods()                                               | 返回一个包含某些 Method 对象的数组，这些对象反映此 Class 对象所表示的类或接口（包括那些由该类或接口声明的以及从超类和超接口继承的那些的类或接口）的公共 member 方法。 |

##### method常用的方法

| 方法返回值 | 方法名称                           | 方法说明                                                                                                              |
|------------|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| Object     | invoke(Object obj, Object... args) | 对带有指定参数的指定对象调用由此 Method 对象表示的底层方法。                                                          |
| Class<?>   | getReturnType()                    | 返回一个 Class 对象，该对象描述了此 Method 对象所表示的方法的正式返回类型,即方法的返回类型                            |
| Type       | getGenericReturnType()             | 返回表示由此 Method 对象所表示方法的正式返回类型的 Type 对象，也是方法的返回类型。                                    |
| Class<?>[] | getParameterTypes()                | 按照声明顺序返回 Class 对象的数组，这些对象描述了此 Method 对象所表示的方法的形参类型。即返回方法的参数类型组成的数组 |
| Type[]     | getGenericParameterTypes()         | 按照声明顺序返回 Type 对象的数组，这些对象描述了此 Method 对象所表示的方法的形参类型的，也是返回方法的参数类型        |
| String     | getName()                          | 以 String 形式返回此 Method 对象表示的方法名称，即返回方法的名称                                                      |
| boolean    | isVarArgs()                        | 判断方法是否带可变参数，如果将此方法声明为带有可变数量的参数，则返回 true；否则，返回 false。                         |
| String     | toGenericString()                  | 返回描述此 Method 的字符串，包括类型参数。                                                                            |

#### Constructor

Java.lang.reflec 包中的类，表示类的构造方法。

##### class中获取Constructor

| 方法返回值       | 方法名称                                           | 方法说明                                                  |
|------------------|----------------------------------------------------|-----------------------------------------------------------|
| static Class<?>  | forName(String className)                          | 返回与带有给定字符串名的类或接口相关联的 Class 对象。     |
| Constructor<T>   | getConstructor(Class<?>... parameterTypes)         | 返回指定参数类型、具有public访问权限的构造函数对象        |
| Constructor<?>[] | getConstructors()                                  | 返回所有具有public访问权限的构造函数的Constructor对象数组 |
| Constructor<T>   | getDeclaredConstructor(Class<?>... parameterTypes) | 返回指定参数类型、所有声明的（包括private）构造函数对象   |
| Constructor<?>[] | getDeclaredConstructor()                           | 返回所有声明的（包括private）构造函数对象                 |
| T                | newInstance()                                      | 创建此 Class 对象所表示的类的一个新实例。                 |

##### Constructor中的常用方法

| 方法返回值 | 方法名称                        | 方法说明                                                                                                  |
|------------|---------------------------------|-----------------------------------------------------------------------------------------------------------|
| Class<T>   | getDeclaringClass()             | 返回 Class 对象，该对象表示声明由此 Constructor 对象表示的构造方法的类,其实就是返回真实类型（不包含参数） |
| Type[]     | getGenericParameterTypes()      | 按照声明顺序返回一组 Type 对象，返回的就是 Constructor对象构造函数的形参类型。                            |
| String     | getName()                       | 以字符串形式返回此构造方法的名称。                                                                        |
| Class<?>[] | getParameterTypes()             | 按照声明顺序返回一组 Class 对象，即返回Constructor 对象所表示构造方法的形参类型                           |
| T          | newInstance(Object... initargs) | 使用此 Constructor对象表示的构造函数来创建新实例                                                          |
| String     | toGenericString()               | 返回描述此 Constructor 的字符串，其中包括类型参数。                                                       |

#### Annotation

参考《[JAVA注解]([book/java-annotation.md](https://www.sunliaodong.cn/2021/02/04/Java%E7%B3%BB%E7%BB%9F%E5%8C%96%E5%AD%A6%E4%B9%A0%E4%B9%8B%E6%B3%A8%E8%A7%A3/))》

#### Array

##### class中的array

| 方法返回值 | 方法名称           | 方法说明                                   |
|------------|--------------------|--------------------------------------------|
| Class<?>   | getComponentType() | 返回表示数组元素类型的 Class，即数组的类型 |
| boolean    | isArray()          | 判定此 Class 对象是否表示一个数组类。      |

##### array中常用的方法

| 方法返回值    | 方法名称                                               | 方法说明                                       |
|---------------|--------------------------------------------------------|------------------------------------------------|
| static Object | set(Object array, int index)                           | 返回指定数组对象中索引组件的值。               |
| static int    | getLength(Object array)                                | 以 int 形式返回指定数组对象的长度              |
| static object | newInstance(Class<?> componentType, int... dimensions) | 创建一个具有指定类型和维度的新数组。           |
| static Object | newInstance(Class<?> componentType, int length)        | 创建一个具有指定的组件类型和长度的新数组。     |
| static void   | set(Object array, int index, Object value)             | 将指定数组对象中索引组件的值设置为指定的新值。 |

### 使用步骤：获取Class对象、调用对象方法

1. 获取想要操作的类的 Class 对象，他是反射的核心，通过 Class 对象我们可以任意调用类的方法。可以通过以下方式获取：
   - 调用对象的getClass()：(new Man()).getClass()
   - 调用类的class属性：Man.class
   - 使用Class.forName调用类的全路径（**最安全，性能最好**）：Class.forName("com.sunld.Man")
2. 调用 Class 类中的方法，既就是反射的使用阶段。
3. 使用反射 API 来操作这些信息。
   
   ```java
   package com.sunld;
    public class ReflectionTest {
        public static void main(String[] args) {
            // 获取 Man 类的 Class 对象
            Class<?> clazz = Class.forName("com.sunld.Man");
            // 获取 Man 类的所有方法信息
            Method[] method = clazz.getDeclaredMethods();
            for (Method m : method) {
                System.out.println(m.toString());
            }
            // 获取 Man 类的所有成员属性信息
            Field[] field = clazz.getDeclaredFields();
            for (Field f : field) {
                System.out.println(f.toString());
            }
            // 获取 Man 类的所有构造方法信息
            Constructor[] constructor = clazz.getDeclaredConstructors();
            for (Constructor c : constructor) {
                System.out.println(c.toString());
            }
        }
    }
   ```

### 构建对象

#### Class对象的newInstance()

使用 Class 对象的 newInstance()方法来创建该 Class 对象对应类的实例，但是这种方法要求该 Class 对象对应的类有默认的空构造器。

```java
//获取 Man 类的 Class 对象
Class<?> clazz = Class.forName("com.sunld.Man");
//使用.newInstane 方法创建对象
Man p=(Man) clazz.newInstance();
```

#### 调用Constructor对象的newInstance()

先使用 Class 对象获取指定的 Constructor 对象，再调用 Constructor 对象的 newInstance() 方法来创建 Class 对象对应类的实例,通过这种方法可以选定构造方法创建实例。

```java
Class<?> clazz=Class.forName("com.sunld.Man");  
//获取构造方法并创建对象
Constructor c=clazz.getDeclaredConstructor(String.class);
//创建对象并设置属性
Man m=(Man) c.newInstance("男人");
```

## 其他

### 安全性

##### Java的安全模型

java运行在jvm中，不与外部直接联系，java的安全模型包括：字节码验证器、类加载器、安全管理器、访问控制器等一系列的组件。java通过反射可以处理private方法和属性，说明它绕过了访问控制器。它其实是Java本身为了某种目的而留下的类似于“后门”的东西，它的原理其实是关闭访问安全检查。

##### Java中访问控制的实现

Field、Method和Constructor类，它们都有一个共同的父类AccessibleObject 。AccessibleObject 有一个公共方法：void setAccessible(boolean flag)。正是这个方法，让我们可以改变动态的打开或者关闭访问安全检查，从而访问到原本是private的方法或域。另外，访问安全检查是一件比较耗时的操作，关闭它反射的性能也会有较大提升。

##### Java中的作用域（访问控制）

<div align=center>

![Java中的作用域](Java系统化学习之反射/1587540118382.png)

</div>

### ClassUtils

在spring的源码中提供了Class反射使用到的常用封装，可以参考修改或者直接使用。

### 自定义ClassUtils

[ClassUtils代码地址](https://github.com/sld880311/Architect-Growth-Manual/tree/master/docs/book/source/ClassUtils.java)

### 内省Introspector

一种用于处理javabean的API，提高Java反射的效率

#### 内省Introspector类结构

<div align=center>

![内省Introspector类结构](Java系统化学习之反射/1589936907736.png)

</div>

1. Introspector：获取JavaBean的BeanInfo
2. BeanInfo：通过getPropertyDescriptors 方法和 getMethodDescriptors 方法可以拿到 javaBean 的字段信息列表和 getter 和 setter 方法信息列表
3. PropertyDescriptors 可以根据字段直接获得该字段的 getter 和 setter 方法。
4. PropertyDescriptor类表示JavaBean类通过存储器导出一个属性。主要方法：
   - getPropertyType()，获得属性的Class对象
   - getReadMethod()，获得用于读取属性值的方法；getWriteMethod()，获得用于写入属性值的方法;
   - hashCode()，获取对象的哈希值;
   - setReadMethod(Method readMethod)，设置用于读取属性值的方法;
   - setWriteMethod(Method writeMethod)，设置用于写入属性值的方法。
5. MethodDescriptors 可以获得方法的元信息，比如方法名，参数个数，参数字段类型等。

#### 简单示例

```java
package com.sunld;

import java.beans.*;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class TestIntrospector {
    class User{
        private String userName;
        private int age;

        public String getUserName() {
            return userName;
        }

        public void setUserName(String userName) {
            this.userName = userName;
        }

        public int getAge() {
            return age;
        }

        public void setAge(int age) {
            this.age = age;
        }

        public String findUser(){
            return "findUser:zhangs : 100age";
        }

        private String findUser2(){
            return "findUser2:zhangs : 100age";
        }

        String findUser3(){
            return "findUser3:zhangs : 100age";
        }

        protected String findUser4(){
            return "findUser4:zhangs : 100age";
        }
    }

    public static void main(String[] args) throws IntrospectionException {
        TestIntrospector t = new TestIntrospector();
        User u = t.new User();
        u.setAge(20);
        u.setUserName("dfafda");
        // 获取整个Bean的信息
        // 在Object类时候停止检索，可以选择在任意一个父类停止
        BeanInfo beanInfo = null;
        try {
            // 获取某个对象的BeanInfo信息，并且可以指定上限
            beanInfo = Introspector.getBeanInfo(User.class, Object.class);
            System.out.println(beanInfo);
            System.out.println("所有属性描述：");
            // 获取所有的属性描述
            PropertyDescriptor[] pds = beanInfo.getPropertyDescriptors();
            for (PropertyDescriptor propertyDescriptor : pds) {
                System.out.println(propertyDescriptor.getName());
            }
            System.out.println("所有方法描述：");
            for (MethodDescriptor methodDescriptor : beanInfo.getMethodDescriptors()) {
                System.out.println(methodDescriptor.getName());
                if(methodDescriptor.getName().startsWith("get")){
                    Method method = methodDescriptor.getMethod();
                    System.out.println(method.invoke(u));
                }
            }
            /**
             * 自定义
             */
            String propertyName = "userName";
            PropertyDescriptor namePd = new PropertyDescriptor(propertyName, User.class);

            System.out.println("名字：" + namePd.getReadMethod().invoke(u));
            namePd.getWriteMethod().invoke(u, "tom");
            System.out.println("名字：" + namePd.getReadMethod().invoke(u));
        } catch (IntrospectionException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
    }
}
```

### org.springframework.beans.BeanUtils

```java
	/**
	 * Copy the property values of the given source bean into the given target bean.
	 * <p>Note: The source and target classes do not have to match or even be derived
	 * from each other, as long as the properties match. Any bean properties that the
	 * source bean exposes but the target bean does not will silently be ignored.
	 * @param source the source bean
	 * @param target the target bean
	 * @param editable the class (or interface) to restrict property setting to
	 * @param ignoreProperties array of property names to ignore
	 * @throws BeansException if the copying failed
	 * @see BeanWrapper
	 */
	private static void copyProperties(Object source, Object target, @Nullable Class<?> editable,
			@Nullable String... ignoreProperties) throws BeansException {

		Assert.notNull(source, "Source must not be null");
		Assert.notNull(target, "Target must not be null");

		/**
		 * 获取目标对象的class
		 */
		Class<?> actualEditable = target.getClass();
		if (editable != null) {
			if (!editable.isInstance(target)) {
				throw new IllegalArgumentException("Target class [" + target.getClass().getName() +
						"] not assignable to Editable class [" + editable.getName() + "]");
			}
			actualEditable = editable;
		}
		/**
		 * 获取目标对象的属性信息
		 */
		PropertyDescriptor[] targetPds = getPropertyDescriptors(actualEditable);
		/**
		 * 处理忽略属性
		 */
		List<String> ignoreList = (ignoreProperties != null ? Arrays.asList(ignoreProperties) : null);

		for (PropertyDescriptor targetPd : targetPds) {
			Method writeMethod = targetPd.getWriteMethod();
			if (writeMethod != null && (ignoreList == null || !ignoreList.contains(targetPd.getName()))) {
				PropertyDescriptor sourcePd = getPropertyDescriptor(source.getClass(), targetPd.getName());
				if (sourcePd != null) {
					Method readMethod = sourcePd.getReadMethod();
					if (readMethod != null &&
							ClassUtils.isAssignable(writeMethod.getParameterTypes()[0], readMethod.getReturnType())) {
						try {
							if (!Modifier.isPublic(readMethod.getDeclaringClass().getModifiers())) {
								readMethod.setAccessible(true);
							}
							Object value = readMethod.invoke(source);
							if (!Modifier.isPublic(writeMethod.getDeclaringClass().getModifiers())) {
								writeMethod.setAccessible(true);
							}
							writeMethod.invoke(target, value);
						}
						catch (Throwable ex) {
							throw new FatalBeanException(
									"Could not copy property '" + targetPd.getName() + "' from source to target", ex);
						}
					}
				}
			}
		}
	}
```

### instanceof和isInstance区别

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#bbb;border-spacing:0;}
.tg td{background-color:#E0FFEB;border-color:#bbb;border-style:solid;border-width:1px;color:#594F4F;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#9DE0AD;border-color:#bbb;border-style:solid;border-width:1px;color:#493F3F;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-0pky{border-color:inherit;text-align:left;vertical-align:top}
.tg .tg-0lax{text-align:left;vertical-align:top}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky">用法</th>
    <th class="tg-0pky">功能</th>
    <th class="tg-0pky">对象本身</th>
    <th class="tg-0pky">父类/接口</th>
    <th class="tg-0pky">Object</th>
    <th class="tg-0lax">null</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky">obj instanceof class</td>
    <td class="tg-0pky">判断对象是否是某个类型</td>
    <td class="tg-0pky">true</td>
    <td class="tg-0pky">true</td>
    <td class="tg-0pky">true</td>
    <td class="tg-0lax">false</td>
  </tr>
  <tr>
    <td class="tg-0pky">class.isInstance(obj)</td>
    <td class="tg-0pky">判断对象是否可以转换为这个类</td>
    <td class="tg-0pky">true</td>
    <td class="tg-0pky">true</td>
    <td class="tg-0pky">true</td>
    <td class="tg-0lax">false</td>
  </tr>
</tbody>
</table>

```java
package com.sunld;

public class TestInstanceClass {

    class A {
    }

    class B extends A {
    }

    public static void main(String[] args){
        TestInstanceClass t = new TestInstanceClass();

        B b = t.new B();
        A a = t.new A();
        A ba = t.new B();
        System.out.println("1------------");
        System.out.println(b instanceof B);// true
        System.out.println(b instanceof A);// true
        System.out.println(b instanceof Object);// true
        System.out.println(null instanceof Object);// false
        System.out.println("2------------");
        System.out.println(b.getClass().isInstance(b));// true
        System.out.println(b.getClass().isInstance(a));// false
        System.out.println("3------------");
        System.out.println(a.getClass().isInstance(ba));// true
        System.out.println(b.getClass().isInstance(ba));// true
        System.out.println(b.getClass().isInstance(null));// false
        System.out.println("4------------");
        System.out.println(A.class.isInstance(a));// true
        System.out.println(A.class.isInstance(b));// true
        System.out.println(A.class.isInstance(ba));// true
        System.out.println("5------------");
        System.out.println(B.class.isInstance(a));// false
        System.out.println(B.class.isInstance(b));// true
        System.out.println(B.class.isInstance(ba));// true
        System.out.println("6------------");
        System.out.println(Object.class.isInstance(b));// true
        System.out.println(Object.class.isInstance(null));// false
    }
}
```

## 参考

1. [深入理解Java类型信息(Class对象)与反射机制](https://blog.csdn.net/javazejian/article/details/70768369)
2. 《Java并发编程的艺术》
3. 《深入理解Java虚拟机》
4. [Java中instanceof和isInstance区别详解](https://www.cnblogs.com/greatfish/p/6096038.html)
