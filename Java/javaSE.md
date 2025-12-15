[TOC]

# 参考

[API文档（中文）](https://www.matools.com/api/java8)
[JavaSE API 参考手册（英文）](https://docs.oracle.com/javase/8/docs/api/) 



# 基础小知识

## 项目结构

idea 里的项目结构从大到小依次是**Project、Module、Package、Class**。

在不同的Package中的类 是相互独立的，即使它们同名.

可以使用**`import 包名.类名;`**来进行跨Package的使用.如  `import com.fengye.test.constructor.Player;`.

---

## IDEA 快捷键

`ctrl+alt+L`格式化代码.
`ctrl+Insert`快捷创建javabean .
创建空参构造可以点两下`Tab.`
创建全参构造可以`shift + ⬇️`或者`Ctrl+A`全选.
使用全参构造方法时不知道参数是什么？按下`Ctrl+P`就能显示.

`Ctrl+Alt+V`能自动生成变量定义部分.
`Ctrl+Alt+T`能对于选择的代码部分选择语句进行包裹.
`Ctrl+D`向下复制一行.
`Ctrl+N`全局搜索类.
`Ctrl+F12`类内部搜索成员.
`Shift+Alt+⬇️`移动到下一行.
`Shift + F6`批量修改变量名称.
`Alt + Enter`列出报错所有解决方法.



## for循环 语法糖

`列表.fori` 即为列表长度的for循环

````java
char[] ch = {'a','b','c'};
//ch.fori
for (int i = 0; i < ch.length; i++) {
    }
````

`number.fori`即为几次的for循环

``````java	
//3.fori
for (int i = 0; i < 3; i++) {
    
		}
``````

类似的,`xx.forr`为倒序for循环。



## Scanner

``````java
public class Test {
    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);
		
        //第一种，只能录入特定的数据类型
        //且不能录入空格,\t，\n
        String sc1 = sc.next();
        System.out.println(sc1);

        String sc2 = sc.next();
        System.out.println(sc2);
		
        //第二种读入整行,类比c中的getline()
        String sc3 = sc.nextLine();
        System.out.println(sc3);

        String sc4 = sc.nextLine();
        System.out.println(sc4);

    }
}
``````



# 面向对象

拿对应的东西解决相应的问题.使用已有对象（Scanner,Random）或者自己设计对象.

## 设计类和对象

*eg:*

````java
public class Phone{
    //1.成员变量
 	String brand;
    double price;
    
    //2.成员方法
    public void call()
    {
        System.out.println("calling!");
    }
}
````

> 类名首字母大写，驼峰命名.


> 一个java文件中可以定义多个class，但是用public修饰的只能有一个，而且类名必须成为代码文件名.



用来描述一类事物的类叫做**==javabean==**类.

javabean类中不含main方法.





## 封装（Encapsulation）

正确设计一个类.

对象代表什么，就得将对象相关的数据以及数据对应的行为封装.

>  例如，人画圆.需要将draw()方法封装在Circle类中而非Person类中



**private**修饰的成员只能在本类中访问.

封装常使用getter/setter方法访问类中的成员.



## this关键字

为了区分局部变量和成员变量.

````java
public class Player {
    private int age =10;

    public void method1(){
        int age = 18;
        System.out.println(age);
    }
    
    //就近原则，输出18

    public void method2(){
        System.out.println(this.age);
    }
}
	//输出10
````



## 构造方法 constructor

创建对象的时候进行初始化.

- 方法名与类名相同
- 没有返回值类型.连void都没.
- 没有返回值，不能由return带回返回结果.



主要有**无参构造**和**带参构造**.

``````````java
package com.fengye.test.constructor;

public class Player {
    private int age;
    private String name;

    //空参构造
    public Player()
    {
        age = 20;
        name = "Paul";
    }

    //带参构造
    public Player(int age,String name)
    {
        this.age = age;
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public String getName() {
        return name;
    }
}

``````````

> 默认存在无参构造.但若手动定义了有参构造，那么默认的无参构造就没了，需要手写.



## 标准javabean类

1. 类名清晰，驼峰命名.

2. 所有成员变量使用**private**修饰.

3. 提供至少2个构造方法.

   - **无参构造**
   - **带参构造**

4. 带有公共的 **getter/setter** 方法

**额外规范**：

5. 重写需要在类内重新实现的方法，比如toString(),equals()等.
*eg:*
`````java
@Override
public String toString() {
    return "User{name='" + name + "', age=" + age + ", isStudent=" + isStudent + "}";
}
`````



**生成标准javabean的快捷键：**

`**alt + insert**` 



还有更快捷 ，一个叫做PTG的 plugins.右键单击.

![image-20251112201041812](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251112201041812.png)

> 我草，这太爽了也



## 对象内存图

![image-20251112201810069](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251112201810069.png)

![image-20251112201833899](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251112201833899.png)

### 一个对象的内存图

<iframe src="//player.bilibili.com/player.html?isOutside=true&aid=298622138&bvid=BV17F411T7Ao&cid=585599445&p=87&autoplay=0" style="width: 100%; max-width: 1000px; height: 560px;" scrolling="no" border="0" frameborder="no" framespacing="0"  allowfullscreen="true"></iframe>

看不懂图了滚回去看视频. 

![image-20251112202952861](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251112202952861.png)



### 两个对象的内存图

与上类似.



### 两个引用指向同一个对象

实际上赋值时只是进行了地址的传递.

在此例子中，stu1和stu2实际上都指向在堆内存中的存储空间的地址.只有**new**关键字会在堆内存中开辟新的存储空间.

![image-20251113123911325](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251113123911325.png)



`stu1 = null`这一语句只是清除stu1中存储的地址，相当于断开了stu1和堆内存开辟的存储空间的连接，但堆内存中的存储空间依然存在，因为stu2还指向它.

当执行完`stu2 = null`之后，没有指针指向堆内存中的存储空间，它便被回收了.

![image-20251113124235613](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251113124235613.png)



## 基本数据类型和引用数据类型

基本数据类型仅是在栈内存中开辟一小块存储空间，而引用数据类型会在堆内存中开辟存储空间，在栈内存中存储其地址.

 ![image-20251113124845372](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251113124845372.png)

> 这下看懂了.



基本数据类型赋值的时候会赋给其他变量真实的值.

``````java
int a =10;
b = a;  //  b的值是10
``````

但是引用数据类型只会赋给其他变量自己指向的地址.



## this的本质

this的本质是所在方法调用者的地址值.



## static

可以修饰成员方法or成员变量.(静态方法or静态变量).

它可以将将**成员**与 **“类的实例” 解绑**，使其属于 **“类本身”**。

### 静态变量

- 静态变量可以被该类所有对象**共享**.
- 不属于对象,属于**类**.

- 静态变量是随着**类的加载**而加载的,优先对象出现,单独存储在堆内存中的**静态区**.

  

![image-20251117185717443](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251117185717443.png)



可以通过**类名调用**或**对象名调用**.

``````java
public class Student {
    public static String teacherName;
}

//测试类
public class StudentTest {
    public static void main(String[] args) {
        Student.teacherName = "fengye";		//类名调用 推荐
        Student stu1 = new Student();
        Student stu2 = new Student();

        stu1.teacherName = "fengye";		//对象名调用
        System.out.println(stu1.teacherName);
        System.out.println(stu2.teacherName);
    }
}
``````



### 静态方法

- 多用于测试类和[工具类](#utilClass),javabean类中很少用.

#### 工具类 <a id="utilClass"></a>

- 见名知意
- 私有化构造方法
- 方法都为静态

**eg:**

```````java
//工具类
public class ArrayUtil {
    private ArrayUtil() {
    }

    public static String printArr(int[] numArr){
        StringBuilder sb = new StringBuilder();
        sb.append("[");
        for (int i = 0; i < numArr.length; i++) {
            if (i != numArr.length - 1){
                sb.append(numArr[i]);
                sb.append(",");
            }else {
                sb.append(numArr[i]);
            }

        }
        sb.append("]");
        String result = sb.toString();
        return result;
    }
}

//测试类
package com.fengye.day1118.UtilClass;

public class UtilTest {
    public static void main(String[] args) {
        int[] numArr = {1,2,10,55};
		
        //直接使用类名调用
        String result = ArrayUtil.printArr(numArr);
        System.out.println(result);
    }
}
```````

### 注意

- 静态方法中无this关键字.   (类名调用没有实例,肯定没有this)
- 静态方法**只能**访问静态 方法和变量.
- 非静态方法**可以**访问**所有**.

​	(非静态 方法和变量 在每个对象中都不同.)

![image-20251118205450407](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251118205450407.png)



## 继承（Inheritance）

### **定义**

使用`extends`关键字。

````java
public class 子类 extends 父类 (){}
````

能合并相同代码，减少代码冗余，提高代码复用性。



### 特点

- java只支持**单继承**，不支持多继承，但支持**多层继承**。

> **单继承：**一个子类只能继承于一个父类。
>
> **多继承：**一个子类继承于多个父类。
>
> **多层继承**：C继承于B，B继承于A。

- 每一个类都直接或间接地继承于`Object`



### 继承的内容

![image-20251121185022494](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251121185022494.png)

- **构造方法**无法继承。
- **成员变量**均可以继承。
	 - 父类的私有变量不能通过子类直接访问，只能通过父类定义的公有方法等访问。
- **成员方法**
  - 非私有、非静态、非final可以继承。常通过**虚方法表**来调用。

![image-20251121190550944](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251121190550944.png)

### 继承中：成员变量访问特点

==就近原则==：如果没有this或super修饰，先在局部位置找，然后本类成员位置找，然后父类成员位置找。

```java
class Father {
    String name = "Father";
}

class Son extends Father{
    String name = "Son";
    public void sonShow(){
        String name = "SonShow";
        System.out.println(name);       //SonShow	局部位置找
        System.out.println(this.name);  //Son	从本类成员位置找
        System.out.println(super.name); //Father	从父类成员位置找
    }
}
```



### 继承中：成员方法访问特点

类似于成员变量，使用==就近原则==。

#### 方法重写

当在继承汇总,子类出现了和父类一模一样的方法声明,那么子类的方法就是==重写==的方法.

使用`@Override`注解重写的方法.



**要求:**

1. 重写方法 名称、形参**尽量**和父类中的方法一致。

2. 无法进入虚方法表中的方法无法被重写。（final,private,static）



### 继承中：构造方法访问特点

- 父类构造方法无法被子类继承。
- 子类中所有构造方法会==先==访问==父类中==的==无参构造==，再执行自己。 即子类构造方法的第一句默认是`super()`。

子类使用 `super()`来调用父类的构造方法。

要使用父类有参构造的话，必须使用`super(arg1,arg2..)`来调用父类的有参构造。



### this和super

- this可以理解成一个变量，代表当前方法调用者的地址。
- super 代表父类的存储空间。

![image-20251122154851316](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251122154851316.png)

其中this()调用本类构造方法常用于成员变量的==初始化==。

```java
public class Student {
    String name;
    int age;
    String school;
    
    //想要将school默认初始化为“ZSTU”
    
    public Student() {
        //调用本类其他构造方法
        //添加此语句之后JVM不会再添加super();
        this(null,0,"ZSTU");
    }

    public Student(String name, int age, String school) {
        this.name = name;
        this.age = age;
        this.school = school;
    }
}
```



## 多态（Polymorphism）

同种类型对象表现出不同的形式。

格式常如`父类类型 name = 子类对象；`



**多态的条件：**

1. 有==继承== 或实现(接口中) 关系。
2. 父类引用指向子类对象。
3. 有方法重写。



**多态的优点：**

- 在方法中，使用父类类型作参数，可以接收所有子类对象。扩展性、便利性。

```java
package com.fengye.test.PolymorphismDemo1;

public class Test {
    public static void main(String[] args) {
        Student s= new Student();
        Teacher t = new Teacher();
        Admin ad = new Admin();

        printInfo(s);
        printInfo(t);
        printInfo(ad);

    }

    //想要使得这个方法能够同时接收Person类的所有子类
    //能够调用不同子类中重写的show()方法。
    public static void printInfo(Person p){
        p.show();
    }
}
```



### 多态的优势和弊端

编译看左边，运行看右边。

```java
Person p = new Student();
p.show();

//编译看Person类中是否有show()方法。
//运行看Student类中的 show()方法具体实现。
```



**优势：**

- 便于扩展维护。
- 在方法中，使用父类类型作参数，可以接收所有子类对象。扩展性、便利性。

**弊端：**

- 不能调用子类的特有功能。使用强制类型转换为==子类==转换解决。

```java
Person p = new Student();
//person中没有study()方法，报错
p.study();	

//强制类型转换
Student stu1 = (Student) p;
stu1.study();
```

在上述类型转换中，如果转换时没有转换为对应的子类，那么会报错。

可以使用`instanceof`关键字判断类型。

```java
if (p instanceof Student){
    Student stu1 = (Student) p;
    stu1.study();
}else if(p instanceof Teacher){
    Teacher t1 = (Teacher) p;
    t.teach();
}else{
    System.out.println("没有合适类型");
}
```

**新写法：**与上述功能相同。

```java
if (p instanceof Student stu1){
    stu1.study();
}else if(p instanceof Teacher t){
    t.teach();
}else{
    System.out.println("没有合适类型");
}
```



## 包（Package）

**包(package)**就是文件夹，用于便于类的管理、代码。

约定包名为`公司域名+功能`。

```java
package com.fengye.domain
```

`包名+类名`称为**全类名**。

```java
com.fengye.domain.Student
```

使用**其他包**类的时候，需要使用全类名和`import`关键字。（导包）

```java
import com.fengye.domain.Student
```

**注意：**

- 使用同一个包中的类，不需要导包。
- 用**java.lang**包中的类，不需要导包。
- 同时使用两个包中的同名类，需要用**全类名**。
- 其他情况都要导包。



## final关键字

**final修饰：**

- **类**，该类为最终类，不能被继承。
- **方法**，该方法为最终方法，不能被重写。
- **变量**，修饰后成为==常量==，只能被赋值一次。



==常量==常作为系统配置信息，方便维护（改定义即可全改），提高可读性。

**常量命名：**

- 单个单词：全部大写。
- 多个单词：全部大写，单词间用下划线隔开。

**final常量细节：**

- final修饰变量为**基本类型**，变量的==数据值==不能改变。
- final修饰变量为**引用类型**，变量的==地址值==不能改变。



## 权限修饰符

四种。范围由大到小：

==private < 空着不写(缺省\默认) < protected < public==

*详见下图：*

![image-20251122193210361](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251122193210361.png)



实际开发中，一般只使用public和private。

- 成员变量私有。
- 大多数方法公有。多个方法中 抽取共性的方法一般用private修饰。

## 代码块

即被大括号{}包裹的代码。



### 局部代码块（弃用）

提前结束变量生命周期。

```java
{
    int a = 10;
}
```



### **构造代码**（弃用）

写在**成员位置**的代码块，用{}包围。

能够把多个构造方法中**重复**代码中抽取出来。（现在常用[委托构造]()或定义方法解决）

创建对象时，会**先执行**构造代码块中的内容，**再**执行构造方法。

```java
public class Student {
    private String name;
    
    //构造代码块
    {
        System.out.println("Creating a new Student!");
    }
    
    public Student() {
    }

    public Student(String name) {
        this.name = name;
    }
}

```



### 静态代码块

**格式：**`static{}`

**特点：随着类加载而加载，自动触发，只执行一次。

通常在做一些==数据初始化==的时候使用。



## 委托构造

### 定义

同一类中，构造器通过 `this(...)` 调用本类其他构造器（通常是**参数最全**的构造器），复用初始化逻辑，减少冗余。

### 核心语法

- 格式：`this(参数列表)`（调用本类其他构造器）
- 要求：必须放在构造器**第一行**

### 示例

优化前（冗余）

```java
class Person {
    String name;
    int age;
    
    // 多个构造器重复赋值
    public Person() { 
        this.name = "未知"; 
        this.age = 0; 
    }
    
    public Person(String name) {
        this.name = name; 
        this.age = 0; 
    }
}
```

优化后（委托）

```java
class Person {
    String name;
    int age;
    // 主构造器（核心逻辑）
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // 委托构造器
    public Person() { this("null", 0); } // 无参→主构造器
    public Person(String name) { this(name, 0); } // 单参→主构造器
}
```



## 抽象类和抽象方法

### 抽象方法

**定义：**
将**待重写**的方法抽取到父类中，在父类中不能确定具体的方法体，那么该方法可以定义为**抽象方法**。

**特点：**
抽象方法在子类中**必须**被**重写**。

**eg:**   
```java
public abstract void work();
```

*****

### 抽象类

**定义：**
若一个类中存在**抽象方法**，那么该类必须声明为**抽象类**。

**eg：**
```java
public abstract class Employee{}
```



### 注意
- 抽象类**不能**实例化。
- 抽象类中不一定有抽象方法，但有抽象方法的类一定是抽象类。
- 虽然不能实例化，但可以有**构造方法**。(为了在**能够**实例化的子类中调用`super(...)`，实现构造方法)
- 抽象类的子类：
  - 重写抽象类中所有抽象方法。
  - 或者**也是**抽象类。



## 接口（Interface）

### 定义：
接口是一套**规则**。
`interface`（接口）是 Java 中的**抽象类型**（非具体类），本质是一套==「方法签名(抽象方法)、常量、默认行为、静态行为」==的集合，核心作用是**定义规范、解耦实现、支持多实现**，弥补 Java 类单继承的限制。



1. `interface`格式。
```java
public interface name{}
```
2. 接口不能实例化。
3. 接口和类之间是**实现**关系，使用`implements`关键字。
```java
public class className implements interfaceName{}
```
4. 接口的子类（**实现类**）
  -  要么重写接口中所有抽象方法
  - 要么是抽象类


### **注意点：**
1. 接口和类可以单实现，也可以**多实现**。
```java
public class 类名 implements 接口1,接口2 {}
```

2. 实现类可以在继承一个类的时候 实现多个接口。
```java
public class 类名 extends 父类名 implements 接口1,接口2 {}
```

*****



### 接口中成员特点

**成员变量：**
- 只能是常量，默认用`public static final`修饰

**构造方法：**
- 无

**成员方法：**
- 只能是抽象方法，默认用`public abstract`修饰。**（JDK7 前）**

****



### 接口和类之间的关系
- 类X类
  继承关系。只能单继承，**不能**多继承。
- 类X接口
  实现关系。**可以**多实现。也可以继承后多实现。
- 接口X接口
  继承关系，**可以**单继承，也**可以**多继承。
  子接口的实现类需要重写**所有**接口中继承下来的方法。 



### 接口中的新方法
- JDK7 前接口中只能定义抽象方法。
-  JDK8 中能定义带有方法体的方法。（默认方法、静态方法）
-  JDK9 能定义私有方法

这些，都是为了接口升级。（不改变实现类中的内容，扩展接口功能）



#### **默认方法**
可以在interface中定义**默认方法**，能够写方法体，解决接口升级问题。

```java
public default void show(){}
```

**注意：**
- 默认方法不强制被重写。重写时去掉`default`。
- `public`可省略，但是`default`不能省略。
- 如果实现的多个接口中存在**相同名字**的默认方法，实现类必须对该方法重写。

****

#### **静态方法**

可以在接口中定义静态方法，`static`关键字修饰。
```java
public static void show(){}
```

**注意：**
- **不能**被重写。
- 静态方法只能通过**接口名**调用。（接口不能实例化）
- `public`可省略，但是`static` 不能省略。

****

#### **私有方法（JDK9 新增）**

可以写只为接口内部服务而不需要外类访问的方法。
```java
//普通私有方法，为默认方法服务
private void method(){}

//静态私有方法，为静态方法服务
private static void method(){}
```



**********

### 接口的应用(多态)

主要应用了接口的**多态**。
当接口作为方法的参数的时候，那么这个方法可以接收这个接口**所有**实现类的对象。
```java
//接口定义
public interface Inter{...}

//实现类
public class A implements Inter{...}
public class B implements Inter{...}

//接口的多态
public void work(Inter C){
    ...
}

//在传递参数时进行了Inter C = new A/B 的多态操作
```



### 适配器设计模式

**设计模式**(Design pattern)就是解决问题的各种套路。
适配器设计模式解决接口与实现类的冲突。

一个接口中抽象方法过多，但是只要使用一个时可以使用。
- 例如，下面例子中，接口中定义了5个方法，但实现时只需要使用一个方法。
- 此时新建一个Adapter**适配器**类，实现接口。（如果实现类有父类，那么适配器类继承该父类）
- 最后让真正的实现类继承适配器类，并重写需要用的方法。

```java
//Inter接口
public interface Inter {
    public abstract void method1();
    public abstract void method2();
    public abstract void method3();
    public abstract void method4();
    public abstract void method5();
}

//适配器类，将接口中抽象方法重写为空方法
public class InterAdapter implements Inter{
    @Override
    public void method1() {

    }

    @Override
    public void method2() {

    }

    @Override
    public void method3() {

    }

    @Override
    public void method4() {

    }

    @Override
    public void method5() {

    }
}


//真正的实现类，继承于适配器
public class InterImpl extends InterAdapter{
    @Override
    public void method5() {
        System.out.println("Using method 5!");
    }
}
```



## 内部类

在一个类的里面定义的类。
若B类表示的是A类的一部分，且B类单独存在没有意义，那么此时可以把B类定义为A类的内部类。
```java
public class Car {
    private String brand;
    private double length;
    
    //内部类
    class Engine{
        private String engineName;
        private String engineBrand;
    }
}

```



**特点：**

- 内部类可以**直接**访问外部类的成员，包括private。
- 外部类想要访问内部类的成员**必须**创建对象。

***

### 成员内部类

- 写在成员位置，属于外部类的成员。
- 可以被修饰符（private…等）修饰。
- 可以定义静态变量。(JDK16 后)



**创建：**
1. 外部类提供方法，对外提供内部类对象(内部类为`private`常用)。
2. 直接创建(内部类为`public`)。

```java
public class Outer {
    String name;[-]
    private class Inner{}
	
    //1.提供方法
    public Inner getInstance(){
        return new Inner();
    }
}

Outer out = new Outer();
//使用方法创建时，若内部类为private
// 直接使用Outer.Inner = out.getInstance();会报错

//可以使用父类形成多态
Object in = out.getInstance();
//当然也可以不创建对象，直接使用
System.out.println(out.getInstance());


//2.直接创建
Outer.Inner oi = new Outer().new Inner();
```



**题目：**

![image-20251123200348710](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251123200348710.png)

>answer:
>第一个 `Outer.this.a`
>第二个`this.a`
>第三个`a`

***

### 静态内部类

也是成员内部类中的一种。
只能访问外部类中的静态变量和静态方法。若想要想要访问**非静态**的成员需要创建对象。

```java
public class Outer {
    private String aa;
    
    static class Inner{
        private String bb;
    }
}
```

***

### 局部内部类

- 被定义在方法的内部类叫做**局部内部类**。类似于局部变量
- 外界无法使用局部内部类，需要在方法**内部**创建对象使用。
- 可以访问**外部类**的成员，也可以访问方法的局部变量。

```java
public void show(){
   //局部内部类
    class Inner{
        String name;
        int age;
        public void method(){...}
    }
    
    //要使用局部内部类需要创建对象
    Inner i = new Inner();
    String name = i.name;
}
```

***

### 匿名内部类

匿名内部类本质上是隐藏了名字的内部类。可以写在成员位置，也可以写在局部位置。

**格式：**
```java
new ClassName/InterfaceName(){
    @Override
    ...
};
```

**包括了：**
1. 继承/实现关系
2. 方法的重写
3. 创建对象

```java
public interface Swim {
    public abstract void swim();
}

public abstract class Person {
    public abstract void eat();
}

public class Test {
    public static void main(String[] args) {
        
        //实现接口
        new Swim(){
            @Override
            public void swim() {
                System.out.println("swimming.");
            }
        };
        
        //继承类
        new Person(){
            @Override
            public void eat() {
                System.out.println("eating.");
            }
        };
        
    }
}
```

**使用场景：**
- 当方法的**参数**是**接口或类**时。
- 若是**接口**，那么可以传递这个接口的所有**实现类**对象。
- 如果实现类只用一次，可以用匿名内部类简化代码。
***
# String

定义在java.lang包中的类.
每一个字符串都是一个String对象.
字符串内容无法改变.

**创建字符串**
![image-20251114005814819](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251114005814819.png)

```java
/*
        //字符串构造
        1.使用char[] 便于修改.
        2.使用byte[] 通过ascii表进行输出.
*/
        char[] chs = {'a','b','c','d'};
        String s_chs = new String(chs);
        System.out.println(s_chs);

        byte[] bytes = {97,98,99,100};
        String s_bytes = new String(bytes);
        System.out.println(s_bytes);
```
直接赋值方式定义，再次创建 相同的字符串时会复用前一个字符串在堆内存（StringTable串池）中的地址.
而 new 方式创建的， 每 new 一次都会开辟一个新的字符串地址，较为浪费.

## String的比较

" == "在基本数据类型中 比较的是**值**.
在引用数据类型中比较的是**地址**.
```````java	
String a = "aaa";
String b = "aaa";
String c = new String("aaa");
System.out.println(a == b); //true
System.out.println(b == c);	//false
```````
所以，常用equals()方法比较字符串的**内容**是否相等.
## String遍历

- 使用` length()` 方法获取循环次数.
- 使用`charAt(int index)`方法获取特定索引上的字符.
## StringBuilder

能够创建一个可编辑的字符串对象.
```````java	
StringBuilder sb = new StringBuilder("abc");
sb.append(1);
sb.append('v');
sb.append("123");

System.out.println(sb);

//abc1v123
```````

## StringJoiner
````java	
//定义
public StringJoiner(CharSequence delimiter,
                    CharSequence prefix,
                    CharSequence suffix)a,b,c
    
//可以打印形如"[a,b,c]"的字符串
//其中'['和']'分别为prefix和suffix , ','为delimiter
````

## 拼接原理

在String拼接的时候:
- 如果没有变量参与,那么会复用串池中的字符串.
- 如果有变量参与,那么会在内存中创建新的字符串.

```java	
String s1 = "abc";
String s2 = "a" + "b" + "c";
String s3 = "c";
String s4 = "ab" + s3;

System.out.println(s1 == s2); //true
System.out.println(s1 == s4); //false
```
所以,使用StringBuilder拼接字符串能够节省空间.

# 集合

## ArrayList

可以进行CRUD操作的列表.
```java
ArrayList<E> arr= new ArrayList<>();
```
E 为**泛型**,代表了ArrayList中存储的数据类型.
泛型可为**引用**数据类型,不能为**基本**数据类型.
如果要存储基本数据类型,需要使用对应**包装类**.

![image-20251117150311454](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251117150311454.png)

**常用方法**:
````java
//C
boolean	add(E e)

//R
E	get(int index)
int	indexOf(Object o)

//U
E	set(int index, E element)

//D
E	remove(int index)
boolean	remove(Object o)

````

# 常用API
[API文档（中文）](https://www.matools.com/api/java8)
[API文档（英文）]([Java Platform SE 8](https://docs.oracle.com/javase/8/docs/api/))

### **Math** 

工具类，`Math`类包含执行基本数字运算的方法，如基本指数，对数，平方根和三角函数。
常用 `ceil()`,`floor()`,`sqrt()`,`round()`,`random()`

****

### **System**

工具类，包含一些与系统有关的方法。

`exit()`终止当前运行的虚拟机。
```java
public static void exit(int status)
```

`arraycopy()`将指定源数组中的数组从指定位置复制到目标数组的指定位置。
```java
arraycopy(Object src, int srcPos, Object dest, int destPos, int length)
```
将指定源数组中的数组从指定位置复制到目标数组的指定位置。 阵列组件的一个子序列被从通过引用的源阵列复制`src`被引用的目标阵列`dest` 。 复制的组件数量等于`length`参数。 源阵列中位置`srcPos`至`srcPos+length-1`的组件分别复制到目标阵列的位置`destPos`至`destPos+length-1` .

`currentTimeMillis()`返回当前时间（以毫秒为单位）。以1970.1.1 0:00 （UTC +0）为原点。
```java
public static long currentTimeMillis()
```
可以获取程序运行时间。



### Runtime

虚拟机运行环境。

![image-20251125154434503](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251125154434503.png)

```java
public class RuntimeDemo {  
    public static void main(String[] args) throws IOException {  
  
        //1.获取对象  
        Runtime r1 = Runtime.getRuntime();  
        Runtime r2 = Runtime.getRuntime();  
  
        //2.获取线程数  
        int ProcessorCount = Runtime.getRuntime().availableProcessors();  
        System.out.println(ProcessorCount); //20  
  
        //3.JVM总内存大小,单位为byte  
        System.out.println(Runtime.getRuntime().maxMemory() / 1024 / 1024 + "MB");  //4026MB  
  
        //4.JVM已经获取的总内存大小  
        System.out.println(Runtime.getRuntime().totalMemory() / 1024 / 1024 + "MB");  //254MB  
  
        //5.JVM剩余内存大小  
        System.out.println(Runtime.getRuntime().freeMemory() / 1024 / 1024 + "MB"); //248MB  
  
        //6.执行cmd语句  
        System.out.println(Runtime.getRuntime().exec("shutdown -a"));  
  
        //7.停止虚拟机，也是System.exit()的源码  
        Runtime.getRuntime().exit(0);  
    }  
}
```

> cmd命令`shutdown -s -t 3600`意为计算机在3600秒后自动关机。
> `shutdown -a`意为取消关机。

*******

### Object

是java中的顶级父类。
- 所有的类都直接/间接继承于Object
- 所有类都能访问其方法
- 方法常被子类重写。

**常用方法：**
![image-20251125161157993](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251125161157993.png)

******

clone()存在**浅克隆**和**深克隆**。

**浅克隆/浅拷贝:**
基本数据类型直接拷贝**值**，引用数据类型拷贝**地址**。
会存在问题：拷贝的对象中的引用数据类型与原对象中的共用。

![image-20251126152638496](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251126152638496.png)

**深克隆/深拷贝:**
基本数据类型直接拷贝值，**但是**引用数据类型会创建**新**的对象。除了String，其存储在串池中，会**复用**。

![image-20251126153026034](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251126153026034.png) 



*****

*eg:*
```java
public class ObjectDemo {
    public static void main(String[] args) throws CloneNotSupportedException {

//1.toString 默认打印类名@地址值
        //常在类内部进行重写
        Object obj = new Object();
        String str1 = obj.toString();
        System.out.println(str1);//java.lang.Object@f6f4d33

        Student stu = new Student("张三",23);
        String str2 = stu.toString();
        System.out.println(str2);//张三,23

        //println本质也是调用toString方法
        System.out.println(stu);//张三,23

//2.equals方法默认比较地址值,重写后比较成员属性值.
        Student stu2 = new Student("张三", 23);
        System.out.println(stu.equals(stu2));//true

//3.clone方法，对象拷贝。
        Student stu5 = new Student("fengye",20);
        //需要强转
        Student stu6 = (Student) stu5.clone();
        System.out.println(stu5);  //fengye,20
        System.out.println(stu6);  //fengye,20

        //Object中的clone方法是浅拷贝
        stu5.numArr[0] = 0;
        System.out.println(stu6.numArr[0]); //0
        //若想要实现深拷贝,要重写clone()方法
    }
}

```

**student 类：**
```java

//可克隆的类需要实现Cloneable接口
//标记性接口
public class Student implements Cloneable{
    private String name;
    private int age;

    public int[] numArr = {1,2,3,4,5};

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Student() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return name + "," + age;
    }

    //重写后的equals比较的是属性值
    @Override
    public boolean equals(Object o) {
        if (o == null || getClass() != o.getClass()) return false;
        Student student = (Student) o;
        return age == student.age && Objects.equals(name, student.name);
    }
    //重写equals()必须要重写hashCode()
    //遵守 Object 类定义的通用约定，否则会导致依赖哈希表的集合（如 HashMap、HashSet、HashTable）行为异常。
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }


    //调用父类中的clone方法
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

```

### Objects

是一个工具类，提供了一些方法。

![image-20251126155257760](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251126155257760.png)  

其中 `equals()` 方法先进行地址相等判断，然后进行非空判断，最后调用对象中的`equals()`方法。

源码如下：

```java
 public static boolean equals(Object a, Object b) {
        return (a == b) || (a != null && a.equals(b));
    }
```



****

### BigDecimal和BigInteger

在计算机中，数字以二进制存储。而小数的二进制表示位数可能超出float或double类型的存储上限，这导致浮点数存在精度问题。
**BigDecimal**类就是为了解决此问题。

**创建BigDecimal：**
使用构造方法时，使用String来传递参数能够避免误差。
底层其实是使用一个Byte类型的数组存储。
```java
public BigDecimal(String val)
```

```java
//构造方法创建
BigDecimal bd1 = new BigDecimal("0.009");
BigDecimal bd2 = new BigDecimal("0.1");
System.out.println(bd1);
System.out.println(bd2);

BigDecimal bd5 = bd1.add(bd2);
System.out.println(bd5);

//valueof()静态方法创建
BigDecimal bd6 = BigDecimal.valueOf(10);
System.out.println(bd6);
```

**注意：**
- 若表示的数字不大，未超出double表示范围，建议使用`valueof()`静态方法创建。
- 若表示的数字较大，超出double表示范围，使用构造方法创建。
- 使用`valueof()`时，若传入的是0~10之间的整数，那么会直接返回**已创建**的对象，不会new。

****

**BigDecimal常用方法：**

![image-20251126165611616](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251126165611616.png)

其中，最后一个除法定义为
```java
public BigDecimal divide(BigDecimal divisor,
                         int scale,
                         RoundingMode roundingMode)
```

 `RoundingMode`是一个**枚举**类，枚举常量如下：

![image-20251126170214996](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251126170214996.png)

****

### 正则表达式 Regex

[查看语法详解](Regex.md)
java中正则表达式以*String*形式表示。

**作用：**
1. 判断字符串是否符合正则表达式。
```java
public boolean matches(String regex)
//告诉这个字符串是否匹配给定的regular expression 。
```
2. 在文本中查找符合正则表达式的片段。（爬虫）
**格式：**

![正则表达式](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251126174004492.png)


*****
#### 爬虫

根据正则表达式爬取信息。
使用`Pattern`类表示正则表达式。
`Matcher`类是文本匹配器。可以在大串中去找符合匹配规则的字串。
```java
public Matcher matcher(CharSequence input)
```

**简单本地爬虫:**
```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexDemo2 {
    public static void main(String[] args) {

        String content = "欢迎联系我们：support@example.com，技术咨询请发至dev_team@company.org，" +
                "个人邮箱可留：user123@gmail.com 或 lily_zhang@outlook.com，" +
                "商务合作：business-info@enterprise.cn，备用邮箱：backup_2024@yahoo.com，" +
                "请勿发送至无效地址：invalid-email@.com 或 @missing-user.com，" +
                "更多信息请查看：contact_us+subscribe@service.net，" +
                "海外业务：global_support@international.co.uk，" +
                "测试邮箱：test123.test@example.io，临时邮箱：temp-567@demo.name";

        //1.patter 获取正则表达式对象
        Pattern pattern1 = Pattern.compile("\\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Z|a-z]{2,}\\b");

        //2.matcher获取文本匹配器对象
        //matcher使用pattern1匹配content内容
        Matcher matcher1 = pattern1.matcher(content);

        //3.循环 输出
        //find() 返回一个布尔类型，表示是否找到匹配的字符串。下一次执行会找下一个匹配的字符串。
        //group() 输出当前匹配的字符串。
        while (matcher1.find()){
            String result = matcher1.group();
            System.out.println("- " + result);  /*- support@example.com
                                                - dev_team@company.org
                                                - user123@gmail.com
                                                - lily_zhang@outlook.com
                                                - business-info@enterprise.cn
                                                - backup_2024@yahoo.com
                                                - contact_us+subscribe@service.net
                                                -global_support@international.co.uk
                                                - test123.test@example.io
                                                - temp-567@demo.name*/
        }
    }
}

```

##### 带条件的爬取数据
```java

String regex1 ="((?i)Java)(?=8|11|17)"
//表示匹配Java8，Java11，Java17中的一个
//?为占位符，代替Java
//=为后面跟随的数字
//但最终获取只获取括号外的部分
//其中（？i）表示忽略大小写匹配

String regex2 ="((?i)Java)(?:8|11|17)"
//?后加:表示依然匹配条件，但获取时获取整个串

String regex3 ="((?i)Java)(?！8|11|17)"
//？后加！表示匹配除了后面条件之外符合的串
```

##### 贪婪爬取和非贪婪爬取
贪婪爬取：符合正则情况下尽可能多地获取数据。
非贪婪爬取：符合正则的情况下尽可能少地获取数据。
```java
String str = "abbbbbbbb";


String regex1 = "ab+"; //贪婪爬取

String regex2 = "ab+?"; //非贪婪爬取

````

#### 正则表达式常用方法
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/af398ff29cded844d55447c36691b8ea.jpg)

```java
public class RegexDemo3 {  
    public static void main(String[] args) {  
        String str = "小张abbdsd小红adsadasgdf小明";  
  
        String regex1 = "(\\w[^_])+";  
        String replacedStr = str.replaceAll(regex1,",");  
        System.out.println(replacedStr);    //小张,小红,小明  
  
       String splitStr[] = str.split(regex1);  
        for (int i = 0; i < splitStr.length; i++) {  
            System.out.println(splitStr[i]);  
            /*小张  
            小红            
            小明*/  
        }  
    }
```
*****
#### 分组
分组是指使用小括号把正则表达式分开。
##### 捕获分组
捕获分组指后续还需要使用该组的数据。
从左往右，每一个左括号，依次为1,2,3,4组。
在正则表达式内部复用捕获组  的时候使用`\\`,外部使用`$`。
```java
public class RegexDemo4 {  
    public static void main(String[] args) {  
        //1.判断一个String的开头和结尾是否一致(一个字符)  
        // \\1 代表复用第1组  
        //a123a b999b  
        String regex1 = "(.).+\\1";  
        System.out.println("a1231a".matches(regex1));   //true  
  
        //2.判断字符串的开头和结尾是否相等  
        //abc1231abc bbg999bbg  
        String regex2 = "(.+).+\\1";  
        System.out.println("abc1231abc".matches(regex2));   //true  
        System.out.println("bbg999bg".matches(regex2));     //false  
  
  
        //3.判断一个字符串的开始部分和结束部分是否一致。其开头和结尾中每个字符也需要一致。  
        //例如 aaa123aaa bbbbb99923bbbbb        String regex3 = "((.)\\2*).+\\1";  
        System.out.println("aaa123aaa".matches(regex3));    //true  
        System.out.println("bbb99923bbc".matches(regex3));  //false  
//        System.out.println("bbb99923bb".matches(regex3));  //true 出现了问题  
    }  
}
```

##### 非捕获分组
分组之后不需要再使用本组数据，仅仅是将其括起来
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20251208154644_146_74.jpg)
***

### 时间相关类
***
#### **JDK7前**
##### Date类
是一个JDK中的javabean类，用来描述时间，精确到毫秒。
`Date`使用构造函数初始化。
```java
Date(long date)
//分配一个 `Date`对象，并将其初始化为表示自称为“时代”的标准基准时间以后的指定毫秒数，即1970年1月1日00:00:00 GMT。
Date()
//使用委托构造，初始化为现在时间点。
```

##### SimpleDateFormat类
提供**格式化输出日期**和**解析字符串为日期**的方法。
**构造方法**和**常用方法**：
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20251209110020_149_74.jpg)

**pattern**定义如下：
![patterns](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20251209110426_151_74.png)

```java
import java.text.ParseException;  
import java.text.SimpleDateFormat;  
import java.util.Date;  
  
public class SimpleDateFormatt {  
    public static void main(String[] args) throws ParseException {  
//        1.format()方法实现格式化输出  
        Date date = new Date();  
        //空参构造  
        SimpleDateFormat sdf1 = new SimpleDateFormat();  
        String date1 = sdf1.format(date);  
        System.out.println(date1);  //2025/12/9 11:16  
        //带参构造，实现结构化输出  
        SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy年MM月dd日,HH时mm分ss秒,EE");  
        String date2 = sdf2.format(date);  
        System.out.println(date2);  //2025年12月09日,11时19分53秒,周二  
  
  
//        2.parse方法实现字符串解析  
        //需要解析的字符串  
        String strFormat = "2025-12-09 11:23:55";  
        //创建对象的pattern要跟格式完全一致  
        SimpleDateFormat sdf3 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");  
        Date date3 = sdf3.parse(strFormat);  
        System.out.println(date3);  //Tue Dec 09 11:23:55 CST 2025  
    }  
}
```
***
##### Calendar类
`Calendar`是一个抽象类，无法实例化。
通过`getInstance()`方法获取实例对象。
**常用方法：**
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/3bfdbdb68323dcb44f9969e952bb2bcf.jpg)

```java
import java.util.Date;  
  
public class CalendarDemo1 {  
    public static void main(String[] args) {  
        //1.获取实例  
        //会根据系统的不同时区来获取不同的日历对象  
        //把各种信息都放入一个数组中 包括年月日，时分秒  
        //细节1：抽象类不能直接new  
        //细节2：其中获取月份的范围为0-11  
        //细节3：星期日是一周中的第一天。所以1->周日, 2->周一。  
        Calendar cal1 = Calendar.getInstance();  
        System.out.println(cal1);  
        System.out.println();  
  
        //2.修改日期  
        Date date1 = new Date(0L);  
        cal1.setTime(date1);  
        System.out.println(cal1);  
  
        //3.信息操作  
        //3.1获取日期中的某个字段信息  
        //calendar结果中的索引：0:纪元,1:年,2:月,3:年中的第几周,4:一月中的第几周,5:一月中的第几天.....  
        int year = cal1.get(1);  
        int month = cal1.get(2)+1;  
        int day = cal1.get(5);  
        System.out.println(year + "," + month + "," + day);  //1970,1,1  
  
        //java中定义为了常量，可直接使用  
        int year1 = cal1.get(Calendar.YEAR);  
        int month1 = cal1.get(Calendar.MONTH)+1;  
        int day1 = cal1.get(Calendar.DAY_OF_MONTH);  
        System.out.println(year1 + "," + month1 + "," + day1);  //1970,1,1  
  
        //3.2 set()        cal1.set(Calendar.YEAR,2000);  
        System.out.println(cal1.get(Calendar.YEAR));  //2000  
  
        //3.3 add()        cal1.add(Calendar.YEAR,1);  
        System.out.println(cal1.get(Calendar.YEAR)); //2001  
        cal1.add(Calendar.YEAR,-2);  
        System.out.println(cal1.get(Calendar.YEAR)); //1999  
        
  
  
    }  
}
```
***
#### JDK8后
主要有10个类。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/39cbdaf7ced65cef7ceea7c45621f659.jpg)
##### Date类
###### ZoneID 时区
常用方法如下:
多为**静态**方法，直接使用类名调用。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/a410430c993fa6b38c4353f109cbf370.jpg)
```java
import java.time.ZoneId;  
import java.util.Set;  
  
public class JDK8DateDemo {  
    public static void main(String[] args) {  
        //所有时区  
        Set<String> zoneIds = ZoneId.getAvailableZoneIds();  
        System.out.println(zoneIds);  
        System.out.println(zoneIds.size()); //603 (个可用时区)  
  
        //2.获取当前系统的默认时区  
        ZoneId defaultId = ZoneId.systemDefault();  
        System.out.println(defaultId);  // Asia/Shanghai  
  
        //3.获取指定时区  
        ZoneId zoneId2 = ZoneId.of("America/Toronto");  
        System.out.println(zoneId2);  
    }  
}
```


###### Instant 时间戳
常用方法如下：
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/5ce1f6ce8d2bee48a1502e32caf52234.jpg)

```java
import java.time.Instant;  
import java.time.ZoneId;  
import java.time.ZonedDateTime;  
  
public class InstantDemo {  
    public static void main(String[] args) {  
        //1.static方法。now()获取现在时间戳实例  
        Instant nowins1 = Instant.now();  
        System.out.println(nowins1);    //2025-12-09T12:36:05.895891100Z  
  
        //2.static方法。ofEpochMilli/Second()通过传参数获取时间戳实例  
        Instant ins1 = Instant.ofEpochMilli(9999999999L);  
        System.out.println(ins1);   //1970-04-26T17:46:39.999Z  
        Instant ins2 = Instant.ofEpochSecond(1L);  
        System.out.println(ins2);   //1970-01-01T00:00:01Z  
  
        //3.指定时区  
        ZonedDateTime nowins2 =Instant.now().atZone(ZoneId.of("Asia/Shanghai")); 
        System.out.println(nowins2);    //2025-12-09T20:41:25.592551800+08:00[Asia/Shanghai]  
  
        //4.isBefore/After() 进行判断  
        Instant ins3 = Instant.ofEpochSecond(0L);  
        Instant ins4 = Instant.ofEpochSecond(2L);  
        boolean result1 = ins3.isBefore(ins4);  
        boolean result2 = ins3.isAfter(ins4);  
        System.out.println(result1);    //true  
        System.out.println(result2);    //false  
  
        //5.minusxx()/plusxx()修改Instant.  
        Instant ins5 = Instant.ofEpochSecond(3000L);  
        System.out.println(ins5);   //1970-01-01T00:50:00Z  
        //原本的instant数据不变,新建一个instant对象。  
        //minus  
        Instant ins6 = ins5.minusSeconds(1000L);  
        System.out.println(ins6);   //1970-01-01T00:33:20Z  
        //plus        
        Instant ins7 = ins5.plusSeconds(1000L);  
        System.out.println(ins7);   //1970-01-01T01:06:40Z  
      
    }  
}
```


###### ZonedDateTime 带时区的时间
常用方法如下:
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/2a6e04322b3b11b33d7795ea52012723.jpg)

###### DateTimeFormatter 时间的格式化和解析
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/941ca3a146b12c867bbe826c1ad2a693.jpg)
```java
public static void main(String[] args) {  
        //获取时间对象  
        ZonedDateTime zdt1 = Instant.now().atZone(ZoneId.of("Asia/Shanghai"));  
  
        //创建Formatter  
        DateTimeFormatter dtf1 = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss EE a");  
  
        //格式化输出  
        System.out.println(dtf1.format(zdt1));  //2025-12-12 19:20:31 周五 下午  
  
    }  
}
```

***

##### Calendar类
###### LocalDate、LocalTime、LocalDateTime
- LocalDate:年/月/日
- LocalTime:时/分/秒
- LocalDateTime:年/月/日/时/分/秒
**主要方法:**
![MainMethods](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/0e16d964f580af61fa50fbe6a4959e71.jpg)
还可以进行类型转换。

***

##### 日期工具类
###### Duration、Period、ChronoUnit
- Duration:计算两个“时间”间隔 (seconds,nanoseconds)
- Period:计算两个“日期”间隔 (year,month,day)
- **ChronoUnit:计算两个“时间”间隔 (最常用)**

**ChronoUnit实例:**
```java
public static void main(String[] args) {  
    LocalDateTime nowDate = LocalDateTime.now();  
    LocalDateTime birthDaate = LocalDateTime.of(2005, 9, 14, 10, 0, 0);  
  
    System.out.println("相差年数：" + ChronoUnit.YEARS.between(birthDaate, nowDate));  
    System.out.println("相差月数：" + ChronoUnit.MONTHS.between(birthDaate, nowDate));  
    System.out.println("相差日数：" + ChronoUnit.DAYS.between(birthDaate, nowDate));  
    System.out.println("相差周数：" + ChronoUnit.WEEKS.between(birthDaate, nowDate));  
    System.out.println("相差时数：" + ChronoUnit.HOURS.between(birthDaate, nowDate));  
    System.out.println("相差分数：" + ChronoUnit.MINUTES.between(birthDaate, nowDate));  
    System.out.println("相差秒数：" + ChronoUnit.SECONDS.between(birthDaate, nowDate));  
    System.out.println("相差世纪(100年)数：" + ChronoUnit.CENTURIES.between(birthDaate, nowDate));  
    System.out.println("相差千年数：" + ChronoUnit.MILLENNIA.between(birthDaate, nowDate));  
    System.out.println("相差纪元数：" + ChronoUnit.ERAS.between(birthDaate, nowDate));  
  
    //相差年数：20  
    //相差月数：242  
    //相差日数：7394  
    //相差周数：1056  
    //相差时数：177466  
    //相差分数：10647970  
    //相差秒数：638878203  
    //相差世纪(100年)数：0  
    //相差千年数：0  
    //相差纪元数：0  
}
```

*********
### 包装类
本质上创建了一个引用数据类型对象，并把相应类型的数据存入其中。
便于多态、集合中等使用。
**如下：**
![image-20251117150311454](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/image-20251117150311454.png)

#### 以Integer为例
JDK5前通过调用方法进行对象的创建和计算操作。
```java
public static void main(String[] args) {  
    //JDK5前  
    Integer i1 = new Integer(1);  
    Integer i2 = new Integer("2");  
  
    Integer i3 = Integer.valueOf(3);  
    Integer i4 = Integer.valueOf("4");  
  
    //JDK5后,自动装箱/拆箱  
    // Integer和int可视为相同语法  
    Integer i5 = 5;  
    System.out.println(i1); //1  
    System.out.println(i2); //2  
    System.out.println(i3); //3  
    System.out.println(i4); //4  
    System.out.println(i5); //5  
}
```
>小知识：JDK中,-128~127的Integer对象已经被自动创建好了，新建的时候其实是复用此地址值，而非新建对象。

JDK5之后实现了自动装箱和自动拆箱。可以把Integer和int相同对待。

常用**成员方法**：
均为静态方法，类名调用。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/e33a048bf11caced65ec4316a13d3a49.jpg)
>三个类型转换方法均使用String类型作为返回值。因为int、long类型有局限(长度限制，不能以0开头等)。

```java
public static void main(String[] args) {  
    //1.进制转换  
    String num1 = Integer.toBinaryString(255);  
    String num2 = Integer.toOctalString(255);  
    String num3 = Integer.toHexString(255);  
  
    System.out.println(num1);   //11111111  
    System.out.println(num2);   //377  
    System.out.println(num3);   //ff  
  
    //2.类型转换  
    String numStr = "10010";  
    int num4 = Integer.parseInt(numStr);  
    System.out.println(num4);   //10010  
}
```
>**注意**：8种包装类中，除了`Character`，都有对应的静态`parseXxx()`的方法，进行类型转换。
>`parseXxx()`方法常用于对键盘录入内容进行转换。

```java
public static void main(String[] args) {  
    Scanner sc = new Scanner(System.in);  
    //统一使用nextLine()录入  
    String inputStr = sc.nextLine();  
    //2.3
  
    //类型转换  
    double inputNum = Double.parseDouble(inputStr);  
    System.out.println(inputNum + 1);  //3.3
}
```

***
## Arrays
一个操作数组的工具类。
**常用方法**如下：
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/4684d85cb15a0a3ee9a19085fde1c230.jpg)
其中，最后一个方法的**排序规则**是`Comparator<? super T> c`类型。是一个接口。
此时，应传递一个该接口的实现类对象为参数，我们可以使用**匿名内部类**。
```java
public static void main(String[] args) {  
    Integer[] numArr = {11,2,8,6,20,888,0};  
    //匿名内部类作为参数  
    Arrays.sort(numArr, new Comparator<Integer>() {  
        //sort()底层使用插入排序实现。  
        //如果返回值>=0,跟后面的比。  
        //如果返回值<0，跟前面的比。  
        //直到找到正确位置  
  
        @Override  
        //参数：o1为无序序列中遍历得到的第一个数字。  
        //     o2为在有序序列汇总的元素  
        //简单来说返回o1-o2即为升序  
        //          o2-o1 为降序  
        public int compare(Integer o1, Integer o2) {  
            return o1-o2;  
        }  
    });  
    System.out.println(Arrays.toString(numArr));    //[0, 2, 6, 8, 11, 20, 888]  
}

```

#### lambda表达式
函数式编程的思想特点。忽略谁去做，关注**怎么做**。
格式如下：

```java
()->{

}
```

**注意点:**
- Lambda表达式可以用来简化[匿名内部类](#匿名内部类)的书写。

*eg:*
去除图中高亮部分
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20251215112633118.png)
中间加`->`改为:
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20251215112830002.png)



***
# 基本算法
### 递归
**方法直接或间接地调用自身**来解决问题的编程方式。常包括：
- **基础情况（Base Case）**：递归的终止条件，防止无限调用。
- **递归情况（Recursive Case）**：将问题分解为更小的同类子问题，并调用自身求解。
```java
public static void main(String[] args) {  
    int result = recursiveFactorial(5);  
    System.out.println(result); //120  
}  

//递归求阶乘
private static int recursiveFactorial(int num) {  
    //基础情况
    if (num == 2){  
        return 2;  
    }  
    //递归情况
    return num*recursiveFactorial(num-1);  
}
```
## 常见查找算法(7种)
[基本查找](#基本查找/顺序查找 Sequential Search)、二分查找、分块查找、差值查找、斐波那契查找、树表查找、哈希查找。
***
### 基本查找/顺序查找 Sequential Search
最基本的查找算法，依次向后查找。
```java
public class SequentialSearchDemo1 {  
    public static void main(String[] args) {  
        int[] numArr = {1,3,5,7,9,11,11};  
        int pos1 = SequentialSearch(7, numArr);  
        int pos2 = SequentialSearch(8, numArr);  
        ArrayList<Integer> pos3 = SequentialMultipleSearch(11, numArr);  
        System.out.println(pos1);   //3  
        System.out.println(pos2);   //-1  
        System.out.println(pos3);   //[5, 6]  
    }  
  
  
    //1.顺序查找方法 SequentialSearch(int num,int[] arr)    //返回需要查找的值在列表中的位置,未找到返回-1  
    public static int SequentialSearch(int num,int[] arr){  
        for (int i = 0; i < arr.length; i++) {  
            if(num == arr[i]) {  
                return i;  
            }  
        }  
        return -1;  
    }  
  
    //2.顺序查找多个方法  SequentialMultipleSearch(int num, int[] arr)    //返回存储多个存在位置的ArrayList  
    public static ArrayList<Integer> SequentialMultipleSearch(int num, int[] arr){  
        ArrayList<Integer> list = new ArrayList();  
        for (int i = 0; i < arr.length; i++) {  
            if(num == arr[i]) {  
                list.add(i);  
            }  
        }  
        return list;  
    }  
}  }  
}
```
***
### 二分查找 Binary Search
**条件**：数组中的数据必须是**有序**的。
```java
public class BinarySearchDemo {  
    public static void main(String[] args) {  
        int[] numArr = {1,5,6,10,12,15,66,95,108,561,569,880,999,1001,1002};  
        int pos1 = binarySearch(880, numArr);  
        int pos2 = binarySearch(1, numArr);  
        int pos3 = binarySearch(1005, numArr);  
        System.out.println(pos1);   //11  
        System.out.println(pos2);   //0  
        System.out.println(pos3);   //-1  
    }  
  
    public static int binarySearch(int num,int[] arr){  
        int min = 0;  
        int max = arr.length-1;  
        int mid;  
        while (min <= max){  
            mid = (min+max)/2;  
            if (num < arr[mid]){  
                max = mid -1;  
            }else if(num > arr[mid]){  
                min = mid +1;  
            }else{  
                return mid;  
            }  
        }  
        return -1;  
    }  
}
```

### 插值查找 
*基于**插值算法**的二分改进。*
改进了mid的算法，使其在每次插值时都能使`arr[mid]`更加靠近`key`的值。适用于数据**分布均匀**的数组。
$$
\text{mid} = \text{min} + \frac{\text{key} - \text{arr}[\text{min}]}{\text{arr}[\text{max}] - \text{arr}[\text{min}]} \times (\text{max} - \text{min})
$$

```java
public class InterpolationSearchDemo {  
    public static void main(String[] args) {  
        int[] numArr = {1, 5, 6, 10, 12, 15, 66, 95, 108, 561, 569, 880, 999, 1001, 1002};  
        int pos1 = InterpolationSearch(880, numArr);  
        int pos2 = InterpolationSearch(1, numArr);  
        int pos3 = InterpolationSearch(1005, numArr);  
        System.out.println(pos1);   //11  
        System.out.println(pos2);   //0  
        System.out.println(pos3);   //-1  
    }  
  
    public static int InterpolationSearch(int key, int[] arr) {  
        int min = 0;  
        int max = arr.length - 1;  
        int mid;  
        while (min <= max) {  
            //主要改进语句  
            mid = min + (key - arr[min]) / (arr[max] - arr[min]) * (max - min);  
            if (key < arr[mid]) {  
                max = mid - 1;  
            } else if (key > arr[mid]) {  
                min = mid + 1;  
            } else {  
                return mid;  
            }  
        }  
        return -1;  
    }  
}
```

### 斐波那契查找
*基于**黄金分割比**的二分改进。*
基于黄金分割比进行`mid`的计算。
$$
\text{mid} = \text{min} + \text{黄金分割点左半边长度} - 1
$$
```java

```
***
### 分块查找
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/6453d00dfdc2b1ff4e767cc1045abacd.jpg)
**分块原则：**
1. 前一块中**最大**数据，小于后一块中**所有**数据。（块内无序，块间有序）
2. 块的数量一般等于数字的个数开**根号**。*eg:16个数字通常分4块。*c
**核心思路：**
	先确定数据处于哪一块，再在块内查找。
```java
public class BlockSearchDemo {  
    public static void main(String[] args) {  
        int[] arr = {16, 18, 5, 2,  
                20, 25, 30, 33,  
                60, 57, 34, 100};  
  
        //1.进行分块  
        Block block1 = new Block(18, 0, 3);  
        Block block2 = new Block(33, 4, 7);  
        Block block3 = new Block(100, 8, 11);  
  
        //2.定义索引表  
        ArrayList<Block> blockArr = new ArrayList<>();  
        blockArr.add(block1);  
        blockArr.add(block2);  
        blockArr.add(block3);  
  
        //3.调用方法查找  
        int result1 = blockSearch(10, blockArr, arr);  
        System.out.println(result1);  
  
    }  
  
  
    //定义查找方法  
    public static int blockSearch(int key, ArrayList<Block> indexTable, int[] arr) {  
  
        //寻找块的Index  
        int blockIndex = -1;  
        for (int i = 0; i < indexTable.size(); i++) {  
            //寻找第一个max大于key的块  
            int j = indexTable.get(i).getMax();  
            if (j >= key) {  
                blockIndex = i;  
                break;  
            }  
        }  
        //未找到max大于key的块，则key不在数组中，直接返回-1  
        if (blockIndex == -1) {  
            return -1;  
        }  
  
        //块内寻找key的Index  
        int startIndex = indexTable.get(blockIndex).getStartIndex();  
        int endIndex = indexTable.get(blockIndex).getEndIndex();  
        for (int i = startIndex; i <= endIndex; i++) {  
            if (arr[i] == key) {  
                return i;  
            }  
        }  
        return -1;  
    }
}  

//标准JavaBean
class Block {  
    int max;  
    int startIndex;  
    int endIndex;  
  
    public Block() {  
    }  
  
    public Block(int max, int startIndex, int endIndex) {  
        this.max = max;  
        this.startIndex = startIndex;  
        this.endIndex = endIndex;  
    }  
  
    /**  
     * 获取  
     *  
     * @return max  
     */    public int getMax() {  
        return max;  
    }  
  
    /**  
     * 设置  
     *  
     * @param max  
     */  
    public void setMax(int max) {  
        this.max = max;  
    }  
  
    /**  
     * 获取  
     *  
     * @return startIndex  
     */    public int getStartIndex() {  
        return startIndex;  
    }  
  
    /**  
     * 设置  
     *  
     * @param startIndex  
     */  
    public void setStartIndex(int startIndex) {  
        this.startIndex = startIndex;  
    }  
  
    /**  
     * 获取  
     *  
     * @return endIndex  
     */    public int getEndIndex() {  
        return endIndex;  
    }  
  
    /**  
     * 设置  
     *  
     * @param endIndex  
     */  
    public void setEndIndex(int endIndex) {  
        this.endIndex = endIndex;  
    }  
  
    public String toString() {  
        return "Block{max = " + max + ", startIndex = " + startIndex + ", endIndex = " + endIndex + "}";  
    }  
}
```
### 哈希查找
*分块查找扩展。*
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/0cb7c42491b42fc78a3c884176685930.jpg)
***
## 常见排序算法(10种)
主要算法包括：冒泡排序，选择排序，插入排序，快速排序。
### 冒泡排序
*最熟悉的一集。*
相邻两个数字比较，大数/小数+上浮/下沉 。
```java
public class BubbleSortDemo {  
    public static void main(String[] args) {  
        ArrayList<Integer> numArr = new ArrayList<>(Arrays.asList(2, 3, 5, 4, 1, 10, 0, -6, 33, 156, 8, 9999));  
  
        bubbleSort(numArr);  
        System.out.println(numArr);  
    }  
  
    public static void bubbleSort(ArrayList<Integer> numArr) {  
        //外层遍历  
        for (int j = 0; j < numArr.size() - 1; j++) {  
            //第二层遍历  
            for (int i = 0; i < numArr.size() - j - 1; i++) {  
                if (numArr.get(i) > numArr.get(i + 1)) {  
                    swap(numArr, i, i + 1);  
                }  
            }  
        }  
    }  
  
    public static void swap(ArrayList<Integer> arr, int firstIndex, int secondIndex) {  
        int temp;  
        temp = arr.get(firstIndex);  
        arr.set(firstIndex, arr.get(secondIndex));  
        arr.set(secondIndex, temp);  
    }  
}
```

### 选择排序
每次排序都选择**无序部分**的**最小/最大值**，将其与待排序索引位置交换。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/6d39098fe54a08c914b9759bd2b5a54c.jpg)
```java
public static void main(String[] args) {  
    ArrayList<Integer> numArr = new ArrayList<>(Arrays.asList(2, 3, 5, 4, 1, 10, 0, -6, 33, 156, 8, 9999));  
  
    selectionSort(numArr);  
    System.out.println(numArr); //[-6, 2, 3, 0, 1, 8, 4, 5, 10, 33, 156, 9999]  
}  
  
public static void selectionSort(ArrayList<Integer> numArr){  
    for (int i = 0; i < numArr.size()-1; i++) {  
        int minIndex = i;  
  
        //遍历无序部分  
        for (int j = i+1; j < numArr.size(); j++) {  
            if(numArr.get(i) > numArr.get(j)){  
                minIndex = j;  
                //每轮只交换一次  
            }  
        }  
  
        if(minIndex != i){  
            swap(numArr,i,minIndex);  
        }  
    }  
}  
```

### 插入排序
```java
public class InsertionSortDemo {  
    public static void main(String[] args) {  
        int[] numArr = {2, 3, 5, 4, 1, 10, 0, -6, 33, 156, 8, 9999};  
        insertionSort(numArr);  
        printArr(numArr);  
    }  
  
    public static void insertionSort(int[] arr) {  
        // 1. 从第二个元素开始，依次处理无序部分  
        for (int i = 1; i < arr.length; i++) {  
            int key = arr[i];  // 当前要插入的元素  
            int j = i - 1;  
  
            // 2. 在有序部分从右向左查找插入位置，同时后移元素  
            while (j >= 0 && arr[j] > key) {  
                arr[j + 1] = arr[j];  
                j--;  
            }  
  
            // 3. 插入到正确位置  
            arr[j + 1] = key;  
        }  
    }  
      
    public static void printArr(int[] arr){  
        StringJoiner sj1 = new StringJoiner(",","[","]");  
        for (int i = 0; i < arr.length; i++) {  
            sj1.add(Integer.toString(arr[i]));  
        }  
        System.out.println(sj1);  
    }  
}
```
### 快速排序
背景知识：[递归](#递归)。
使用分区函数`partition()`将数组整理为`[<pivot..,pivot,>pivot..]`格式。并且**递归**调用。最终实现快速排序。
```java
public static void main(String[] args) {  
    int[] numArr = {2, 3, 5, 4, 1, 10, 0, -6, 33, 156, 8, 9999};  
    quickSort(numArr);  
    System.out.println(Arrays.toString(numArr));    //[-6, 0, 1, 2, 3, 4, 5, 8, 10, 33, 156, 9999]  
}

public static void quickSort(int[] arr) {
    if (arr == null || arr.length <= 1) return;
    quickSort(arr, 0, arr.length - 1);
}

// 递归快排：对 [left, right] 区间排序
private static void quickSort(int[] arr, int left, int right) {
    if (left >= right) return;  // 基础情况：区间无效或只有一个元素

    // 1. 分区：选取基准（这里用最右边元素），并调整数组
    int pivotIndex = partition(arr, left, right);

    // 2. 递归排序基准左边
    quickSort(arr, left, pivotIndex - 1);

    // 3. 递归排序基准右边
    quickSort(arr, pivotIndex + 1, right);
}

// 分区函数：将小于基准的放左边，大于的放右边，返回基准最终位置
private static int partition(int[] arr, int left, int right) {
    int pivot = arr[right];  // 选最右元素为基准
    int i = left - 1;        // i 是<=pivot区的右边界

    // 遍历 [left, right - 1]
    for (int j = left; j < right; j++) {
        if (arr[j] <= pivot) {
            i++;
            // 交换 arr[i] 和 arr[j]
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }

    // 将基准放到正确位置（i + 1）
    arr[right] = arr[i + 1];
    arr[i + 1] = pivot;

    return i + 1;  // 返回基准最终索引
}
```
***

# 综合练习


## (一)多态

…..

## (二)拼图小游戏(JavaFX)


## (三)包装类小练习
