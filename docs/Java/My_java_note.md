# Java学习笔记

## 语言基础

java中动态绑定是默认行为，不像C++需要virtual关键字指定。也就是说基类定义的函数到底的实现代码如何是由子类的定义确定，这是一种默认的行为。

java中所有的对象均需要new显示创建，标识符是对对象的引用，而基本类型的变量是存储堆栈中，更高效，new创建的对象是存储在堆中，所以在创建小的简单的变量时，用基本类型更高效。

Java有一些基本的数据类型，这些类型也有相应的包装器类型，

| 基本类型 | 大小 | 包装器类型 |
| boolean | - | Boolean |
| char | 16 bits | Character |
| byte | 8 bits | Byte |
| short | 16 bits | Short |
| int | 32 bits | Integer |
| long | 64 bits | Long |
| float | 32 bits | Float |
| double | 64 bits | Double |
| void | - | Void |

所有的基本类型对象定义时也要进行初始化，否则会报**编译**错误。对象可以不被初始化，这种情况下对象的引用是一个null。

Java的变量定义是有作用域的，基本类型的作用域在{}之间，但是不同的作用域之间的变量不允许重名。

``` java
{ 
    int x = 12;
    {
        int x = 96; //非法
    }
}
```

但是对于对象类型，其标识符在其作用域之外已经是不可用了，但是对象的内存空间会存在着，由Java的垃圾回收机制确定何时真正释放这些内存空间。**永远不要显示销毁对象**。

static关键字与C++类似，用于指定一个类的属性或方法不依赖于对象的，其引用也可以用对象引用也可以用类名直接引用。

``` java
class StaticTest {
    static int i = 47;
}
StaticTest st1 = new StaticTest();
StaticTest st2 = new StaticTest();

```
上面的st1.i和st2.i是指向同一存储空间。

如果创建一个独立运行的程序，那么文件中必须存在某个类与该文件同名，否则编译器会报错，且那个类必须包含一个static main()方法。一个最简单的程序如下：
``` java
// HelloDate.java
import java.util.*;

public class HelloDate {
    public static void main(String[] args) {
        System.out.println("Hello, it's: ");
        System.out.println(new Date());
    }
}
```

适用于对象类型的操作符只有=、==和!=，并且对象的赋值实际上是引用，而不是对值的复制。String还可以有+和+=两个操作。

所以对于函数的参数中引入了对象的时候也是对其引用，因此在函数体内部对参数对象的修改会引起外部对象的变更，而不仅仅是值的传递。

## 数组

数组在定义的时候不能指定长度，但可以用一堆常数进行初始化，如果没有初始化可以用new来生成一个数组对象。

``` java
int[] a1 = {1, 2, 3, 4, 5};
int[] a2; // 与int a2[];等效
a2 = new int[5];

```

## 函数的可变参数列表

func(Object... args)形式表
定义，Object是所有类的基类，实际上是传入了一个Object数组。当然Object也可以是别的类型，只不过这样可变参数列表的内容只能是特定类型的数。

``` java
import java.util.*;
class A {}
public class NewVarArgs {
    static void printArray(Object... args){
        for(Object obj : args)
            System.out.print(obj + " ");
        System.out.println();
    }

    public static void main(String[] args){
        printArray(new Integer(47));
        printArray(new Float(11.11));

        printArray(49, 2.31, "hello");
        printArray(new A(), new A());
    }
}
------

47 
11.11 
49 2.31 hello 
A@2a139a55 A@15db9742
```

## 程序组织

每个编译单元必须有一个后缀名为.java的文件，而在编译单元内有且仅有一个public类，这个类的名称与.java的文件名相同，其他的类不能声明为public的，只能在此文件中提供支持，不能被外部引用。

java把一些编译完后的.class打包成.jar。这些.class文件靠package进行区分，package语句是指明这个.java文件属于哪一个包，必须是程序中除注释外的第一条语句。

``` java
package deerlux.mypackage;
public class MyClass {
 //// 
}

```

## final和static

### final 

在java中，声明类、变量和方法时，可使用关键字final来修饰，final修饰的数据具有终态的特征，表示最终的意思。

* final修饰的类不能被继承。
* final修饰的方法不能被子类重写。
* final修饰的变量（成员变量或局部变量）即为常量，只能被赋值一次。
* final修饰的成员变量必须在声明的同时赋值，如果在声明的时候没有赋值，那么只有一次赋值的机会，而且只能在构造方法中显式赋值，然后才能使用。
* final修饰的局部变量可以只声明不赋值，然后再进行一次性赋值。

由于java的方法设计默认的情况下是支持多态性，也就是说在运行时决定一个方法绑定的是哪一个子类的方法，如果声明一个方法是final的，则此方法不支持动态绑定，即在编译期间就决定了绑定哪个类的实现，因此finalr方法的实现更具有效率。

### static

* static作用于成员变量时，是指类变量，对于不同的实例共享存储空间。
* static作用于方法时，是指类方法，不依赖于类的具体实例，其中不能引用非static的方法或变量。
* static导入，import static之后可以直接引用类的静态成员而不必再写类的名字引用。

## 抽象类和接口

### 抽象类

抽象类是指不能被实例化的类，抽象方法也只能声明而不能实现，只能被其子类实例化，可以通过abstract关键字来指定，如果一个类有一个或多个抽象方法则此类即为抽象类。抽象类不一定要包含抽象方法，但是包含抽象方法的类一定是抽象类。

``` java
import static java.lang.System.*;

public final class DemoAbstract{
    public static void main(String[] args) {
        Teacher t = new Teacher();
        t.setName("王明");
        t.work();

        Driver d = new Driver();
        d.setName("小陈");
        d.work();
    }
}

// 定义一个抽象类，不能被实例化
abstract class People {
    private String name;

    public void setName(String name){
        this.name = name;
    }

    public String getName(){
        return this.name;
    }

    public abstract void work();
}

class Teacher extends People{
    public void work() {
        out.println("我的名字叫" + this.getName());
    }

}

class Driver extends People {
    public void work(){
        out.println("我的名字叫" + this.getName());
    }
}
```
### 接口
接口则比抽象类更为抽象，接口中只能定义public static常量以及public abstract的方法，不能直接被实例化。接口的实现是用关键字implements而不是extends。
``` java
import static java.lang.System.*;

public class Demo{
  public static void main(String[] args) {
      SataHdd sh1=new SeagateHdd(); //初始化希捷硬盘
      SataHdd sh2=new SamsungHdd(); //初始化三星硬盘
  }
}

//串行硬盘接口
interface SataHdd{
    //连接线的数量
    public static final int CONNECT_LINE=4;
    //写数据
    public void writeData(String data);
    //读数据
    public String readData();
}

// 维修硬盘接口
interface fixHdd{
    // 维修地址
    String address = "北京市海淀区";
    // 开始维修
    boolean doFix();
}

//希捷硬盘
class SeagateHdd implements SataHdd, fixHdd{
    //希捷硬盘读取数据
    public String readData(){
        return "数据";
    }
    //希捷硬盘写入数据
    public void writeData(String data) {
        out.println("写入成功");
    }
    // 维修希捷硬盘
    public boolean doFix(){
        return true;
    }
}
//三星硬盘
class SamsungHdd implements SataHdd{
    //三星硬盘读取数据
    public String readData(){
        return "数据";
    }
    //三星硬盘写入数据
    public void writeData(String data){
        out.println("写入成功");
    }
}
//某劣质硬盘，不能写数据
abstract class XXHdd implements SataHdd{
    //硬盘读取数据
    public String readData() {
        return "数据";
    }
}


```
