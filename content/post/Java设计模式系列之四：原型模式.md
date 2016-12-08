+++
title = "Java设计模式系列之四：原型模式"
description = ""
keywords = [
]
categories = ["java","design-patterns","creational","gang of four"
]
date = "2016-12-06T01:43:55+08:00"

+++

### 定义

使用原型实例指定要创建的对象的类型，并通过复制此原型来创建新对象。

### 角色

在原型模式结构图中包含如下几个角色：

* Prototype（抽象原型类）：它是声明克隆方法的接口，是所有具体原型类的公共父类，可以是抽象类也可以是接口，甚至还可以是具体实现类。
* ConcretePrototype（具体原型类）：它实现在抽象原型类中声明的克隆方法，在克隆方法中返回自己的一个克隆对象。
* Client（客户类）：让一个原型对象克隆自身从而创建一个新的对象，在客户类中只需要直接实例化或通过工厂方法等方式创建一个原型对象，再通过调用该对象的克隆方法即可得到多个相同的对象。由于客户类针对抽象原型类Prototype编程，因此用户可以根据需要选择具体原型类，系统具有较好的可扩展性，增加或更换具体原型类都很方便。

![prototype](/image/prototype_1.png)

### 示例代码

```
interface Prototype {
  Object clone();
  String getName();
}
// 1. The clone() contract
interface Command {
  void execute();
}

class PrototypesModule {
  // 2. "registry" of prototypical objs
  private static Prototype[] prototypes = new Prototype[9];
  private static int total = 0;

  // Adds a feature to the Prototype attribute of the PrototypesModule class
  // obj  The feature to be added to the Prototype attribute
  public static void addPrototype( Prototype obj ) {
    prototypes[total++] = obj;
  }

  public static Object findAndClone( String name ) {
    // 4. The "virtual ctor"
    for ( int i = 0; i < total; i++ ) {
      if ( prototypes[i].getName().equals( name ) ) {
        return prototypes[i].clone();
      }
    }
    System.out.println( name + " not found" );
    return null;
  }
}

// 5. Sign-up for the clone() contract.
// Each class calls "new" on itself FOR the client.
class This implements Prototype, Command {
  public Object clone() {
    return new This();
  }
  public String getName() {
    return "This";
  }
  public void execute() {
    System.out.println( "This: execute" );
  }
}

class That implements Prototype, Command {
  public Object clone() {
    return new That();
  }
  public String getName() {
    return "That";
  }
  public void execute() {
    System.out.println( "That: execute" );
  }
}

class TheOther implements Prototype, Command {
  public Object clone() {
    return new TheOther();
  }
  public String getName() {
    return "TheOther";
  }
  public void execute() {
    System.out.println( "TheOther: execute" );
  }
}


public class PrototypeDemo {
  // 3. Populate the "registry"
  public static void initializePrototypes() {
    PrototypesModule.addPrototype( new This() );
    PrototypesModule.addPrototype( new That() );
    PrototypesModule.addPrototype( new TheOther() );
  }
  public static void main( String[] args ) {
    initializePrototypes();
    Object[] objects = new Object[9];
    int total = 0;

    // 6. Client does not use "new"
    for (int i=0; i < args.length; i++) {
      objects[total] = PrototypesModule.findAndClone( args[i] );
      if (objects[total] != null) total++;
    }
    for (int i=0; i < total; i++) {
      ((Command)objects[i]).execute();
    }
  }
}
```

### 适用场景

1. 创建新对象成本较大（如初始化需要占用较长的时间，占用太多的CPU资源或网络资源），新的对象可以通过原型模式对已有对象进行复制来获得，如果是相似对象，则可以对其成员变量稍作修改。
2. 如果系统要保存对象的状态，而对象的状态变化很小，或者对象本身占用内存较少时，可以使用原型模式配合备忘录模式来实现。
3. 需要避免使用分层次的工厂类来创建分层次的对象，并且类的实例对象只有一个或很少的几个组合状态，通过复制原型对象得到新实例可能比使用构造函数创建一个新实例更加方便。

### 优点

1. 当创建新的对象实例较为复杂时，使用原型模式可以简化对象的创建过程，通过复制一个已有实例可以提高新实例的创建效率。
2. 扩展性较好，由于在原型模式中提供了抽象原型类，在客户端可以针对抽象原型类进行编程，而将具体原型类写在配置文件中，增加或减少产品类对原有系统都没有任何影响。
3. 原型模式提供了简化的创建结构，工厂方法模式常常需要有一个与产品类等级结构相同的工厂等级结构，而原型模式就不需要这样，原型模式中产品的复制是通过封装在原型类中的克隆方法实现的，无须专门的工厂类来创建产品。
4. 可以使用深克隆的方式保存对象的状态，使用原型模式将对象复制一份并将其状态保存起来，以便在需要的时候使用（如恢复到某一历史状态），可辅助实现撤销操作。

### 缺点

1. 需要为每一个类配备一个克隆方法，而且该克隆方法位于一个类的内部，当对已有的类进行改造时，需要修改源代码，违背了“开闭原则”。
2. 在实现深克隆时需要编写较为复杂的代码，而且当对象之间存在多重的嵌套引用时，为了实现深克隆，每一层对象对应的类都必须支持深克隆，实现起来可能会比较麻烦。

### 应用实例

* [java.lang.Object#clone()](http://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#clone%28%29)
