+++
description = ""
keywords = [
]
categories = ["java","design-patterns","creational","gang of four"
]
date = "2016-12-03T00:04:10+08:00"
title = "Java设计模式系列之一：抽象工厂模式"

+++

### 定义

抽象工厂模式(Abstract Factory Pattern)：提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。抽象工厂模式又称为Kit模式，属于对象创建型模式。

### 动机

- 在工厂方法模式中具体工厂负责生产具体的产品，每一个具体工厂对应一种具体产品，工厂方法也具有唯一性，一般情况下，一个具体工厂中只有一个工厂方法或者一组重载的工厂方法。但是有时候我们需要一个工厂可以提供多个产品对象，而不是单一的产品对象。

    为了更清晰地理解工厂方法模式，需要先引入两个概念：

    * **产品等级结构** ：产品等级结构即产品的继承结构，如一个抽象类是电视机，其子类有海尔电视机、海信电视机、TCL电视机，则抽象电视机与具体品牌的电视机之间构成了一个产品等级结构，抽象电视机是父类，而具体品牌的电视机是其子类。
    * **产品族** ：在抽象工厂模式中，产品族是指由同一个工厂生产的，位于不同产品等级结构中的一组产品，如海尔电器工厂生产的海尔电视机、海尔电冰箱，海尔电视机位于电视机产品等级结构中，海尔电冰箱位于电冰箱产品等级结构中。
![abstract-factory](/image/abstract-factory_2.png)

- 当系统所提供的工厂所需生产的具体产品并不是一个简单的对象，而是多个位于不同产品等级结构中属于不同类型的具体产品时需要使用抽象工厂模式。
- 抽象工厂模式是所有形式的工厂模式中最为抽象和最具一般性的一种形态。
- 抽象工厂模式与工厂方法模式最大的区别在于，工厂方法模式针对的是一个产品等级结构，而抽象工厂模式则需要面对多个产品等级结构，一个工厂等级结构可以负责多个不同产品等级结构中的产品对象的创建 。当一个工厂等级结构可以创建出分属于不同产品等级结构的一个产品族中的所有对象时，抽象工厂模式比工厂方法模式更为简单、有效率。



### 角色

抽象工厂模式包含如下角色：

- AbstractFactory(抽象工厂):它声明了一组用于创建一族产品的方法，每一个方法对应一种产品。

- ConcreteFactory(具体工厂):它实现了在抽象工厂中声明的创建产品的方法，生成一组具体产品，这些产品构成了一个产品族，每一个产品都位于某个产品等级结构中。

- AbstractProduct(抽象产品):它为每种产品声明接口，在抽象产品中声明了产品所具有的业务方法。

- ConcreteProduct(具体产品):它定义具体工厂生产的具体产品对象，实现抽象产品接口中声明的业务方法。


![abstract-factory](/image/abstract-factory_1.png)

### 示例代码

- AbstractFactory：抽象工厂

```
public interface KingdomFactory {

  Castle createCastle();

  King createKing();

  Army createArmy();
}
```

- ConcreteFactory：具体工厂

```
public class HumanKingdomFactory implements KingdomFactory {

    @Override
    public Castle createCastle() {
        return new HumanCastle();
    }

    @Override
    public King createKing() {
        return new HumanKing();
    }

    @Override
    public Army createArmy() {
        return new HumanArmy();
    }
}
```

- AbstractProduct：抽象产品

```
public interface Castle {

  String getDescription();
}

public interface King {

  String getDescription();
}

public interface Castle {

  String getDescription();
}
```

- ConcreteProduct：具体产品

```
public class HumanCastle implements Castle {
    @Override
    public String getDescription() {
        return "This is Human castle!";
    }
}

public class HumanKing implements King {

    @Override
    public String getDescription() {
        return "This is Human king!";
    }
}

public class HumanArmy implements Army {
    @Override
    public String getDescription() {
        return "This is Human armay!";
    }
}
```

- Application：客户端

```
public class App {

      public static void main(String args[]){
        KingdomFactory factory=new HumanKingdomFactory();
        King king=factory.createKing();
        Castle castle=factory.createCastle();
        Army army=factory.createArmy();
        System.out.println(king.getDescription());
        System.out.println(castle.getDescription());
        System.out.println(army.getDescription());
    }

}
```

### 适用场景

* 一个系统不应当依赖于产品类实例如何被创建、组合和表达的细节，这对于所有类型的工厂模式都是很重要的，用户无须关心对象的创建过程，将对象的创建和使用解耦。
* 系统中有多于一个的产品族，而每次只使用其中某一产品族。可以通过配置文件等方式来使得用户可以动态改变产品族，也可以很方便地增加新的产品族。
* 属于同一个产品族的产品将在一起使用，这一约束必须在系统的设计中体现出来。同一个产品族中的产品可以是没有任何关系的对象，但是它们都具有一些共同的约束，如同一操作系统下的按钮和文本框，按钮与文本框之间没有直接关系，但它们都是属于某一操作系统的，此时具有一个共同的约束条件：操作系统的类型。
* 产品等级结构稳定，设计完成之后，不会向系统中增加新的产品等级结构或者删除已有的产品等级结构。


### 优点

增加新的具体工厂和产品族很方便，无须修改已有系统，符合“开闭原则”。

### 缺点

增加新的工厂和产品族容易，增加新的产品等级结构麻烦，“开闭原则”具有倾斜性。

### 应用实例

* [javax.xml.parsers.DocumentBuilderFactory](http://docs.oracle.com/javase/8/docs/api/javax/xml/parsers/DocumentBuilderFactory.html)
* [javax.xml.transform.TransformerFactory](http://docs.oracle.com/javase/8/docs/api/javax/xml/transform/TransformerFactory.html#newInstance--)
* [javax.xml.xpath.XPathFactory](http://docs.oracle.com/javase/8/docs/api/javax/xml/xpath/XPathFactory.html#newInstance--)



