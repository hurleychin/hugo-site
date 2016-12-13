+++
keywords = [
]
categories = ["java","design-patterns","structural","gang of four"
]
date = "2016-12-13T21:10:18+08:00"
title = "Java设计模式系列之十一：享元模式"
description = ""

+++

### 定义

运用共享技术有效地支持大量细粒度对象的复用。系统只使用少量的对象，而这些对象都很相似，状态变化很小，可以实现对象的多次复用。由于享元模式要求能够共享的对象必须是细粒度对象，因此它又称为轻量级模式，它是一种对象结构型模式。

### 角色

* Flyweight（抽象享元类）：通常是一个接口或抽象类，在抽象享元类中声明了具体享元类公共的方法，这些方法可以向外界提供享元对象的内部数据（内部状态），同时也可以通过这些方法来设置外部数据（外部状态）。
* ConcreteFlyweight（具体享元类）：它实现了抽象享元类，其实例称为享元对象；在具体享元类中为内部状态提供了存储空间。通常我们可以结合单例模式来设计具体享元类，为每一个具体享元类提供唯一的享元对象。
* UnsharedConcreteFlyweight（非共享具体享元类）：并不是所有的抽象享元类的子类都需要被共享，不能被共享的子类可设计为非共享具体享元类；当需要一个非共享具体享元类的对象时可以直接通过实例化创建。
* FlyweightFactory（享元工厂类）：享元工厂类用于创建并管理享元对象，它针对抽象享元类编程，将各种类型的具体享元对象存储在一个享元池中，享元池一般设计为一个存储“键值对”的集合（也可以是其他类型的集合），可以结合工厂模式进行设计；当用户请求一个具体享元对象时，享元工厂提供一个存储在享元池中已创建的实例或者创建一个新的实例（如果不存在的话），返回新创建的实例并将其存储在享元池中。

![flyweight.jpg](/image/flyweight.jpg)

### 示例代码

* Flyweight（抽象享元类）

```java
//围棋棋子类：抽象享元类  
abstract class IgoChessman {  
    public abstract String getColor();  

    public void display(Coordinates coord){  
        System.out.println("棋子颜色：" + this.getColor() + "，棋子位置：" + coord.getX() + "，" + coord.getY() );    
    }  
}
```

* ConcreteFlyweight（具体享元类）

```java
//黑色棋子类：具体享元类  
class BlackIgoChessman extends IgoChessman {  
    public String getColor() {  
        return "黑色";  
    }     
}  

//白色棋子类：具体享元类  
class WhiteIgoChessman extends IgoChessman {  
    public String getColor() {  
        return "白色";  
    }  
}  
```

* UnsharedConcreteFlyweight（非共享具体享元类）

```java
//坐标类：外部状态类  
class Coordinates {  
    private int x;  
    private int y;  

    public Coordinates(int x,int y) {  
        this.x = x;  
        this.y = y;  
    }  

    public int getX() {  
        return this.x;  
    }  

    public int getY() {  
        return this.y;  
    }  

}   

```

* FlyweightFactory（享元工厂类）

```java
//围棋棋子工厂类：享元工厂类，使用单例模式进行设计  
class IgoChessmanFactory {  
    private static IgoChessmanFactory instance = new IgoChessmanFactory();  
    private static Hashtable ht; //使用Hashtable来存储享元对象，充当享元池  

    private IgoChessmanFactory() {  
        ht = new Hashtable();  
        IgoChessman black,white;  
        black = new BlackIgoChessman();  
        ht.put("b",black);  
        white = new WhiteIgoChessman();  
        ht.put("w",white);  
    }  

    //返回享元工厂类的唯一实例  
    public static IgoChessmanFactory getInstance() {  
        return instance;  
    }  

    //通过key来获取存储在Hashtable中的享元对象  
    public static IgoChessman getIgoChessman(String color) {  
        return (IgoChessman)ht.get(color);    
    }  
}
```

* App

```java
class App {  
    public static void main(String args[]) {  
        IgoChessman black1,black2,black3,white1,white2;  
        IgoChessmanFactory factory;  

        //获取享元工厂对象  
        factory = IgoChessmanFactory.getInstance();  

        //通过享元工厂获取三颗黑子  
        black1 = factory.getIgoChessman("b");  
        black2 = factory.getIgoChessman("b");  
        black3 = factory.getIgoChessman("b");  
        System.out.println("判断两颗黑子是否相同：" + (black1==black2));  

        //通过享元工厂获取两颗白子  
        white1 = factory.getIgoChessman("w");  
        white2 = factory.getIgoChessman("w");  
        System.out.println("判断两颗白子是否相同：" + (white1==white2));  

        //显示棋子，同时设置棋子的坐标位置  
        black1.display(new Coordinates(1,2));  
        black2.display(new Coordinates(3,4));  
        black3.display(new Coordinates(1,3));  
        white1.display(new Coordinates(2,5));  
        white2.display(new Coordinates(2,4));  
    }  
}
```

### 适用场景

* 一个系统有大量相同或者相似的对象，造成内存的大量耗费。
* 对象的大部分状态都可以外部化，可以将这些外部状态传入对象中。
* 在使用享元模式时需要维护一个存储享元对象的享元池，而这需要耗费一定的系统资源，因此，应当在需要多次重复使用享元对象时才值得使用享元模式。

### 优点

* 可以极大减少内存中对象的数量，使得相同或相似对象在内存中只保存一份，从而可以节约系统资源，提高系统性能。
* 享元模式的外部状态相对独立，而且不会影响其内部状态，从而使得享元对象可以在不同的环境中被共享。

### 缺点

* 享元模式使得系统变得复杂，需要分离出内部状态和外部状态，这使得程序的逻辑复杂化。
* 为了使对象可以共享，享元模式需要将享元对象的部分状态外部化，而读取外部状态将使得运行时间变长。 

### 应用实例

* [java.lang.Integer#valueOf(int)](http://docs.oracle.com/javase/8/docs/api/java/lang/Integer.html#valueOf%28int%29) and similarly for Byte, Character and other wrapped types.