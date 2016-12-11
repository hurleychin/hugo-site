+++
categories = ["java","design-patterns","structural","gang of four"
]
title = "Java设计模式系列之九：装饰模式"
description = ""
date = "2016-12-12T00:24:30+08:00"
keywords = [
]

+++

### 定义

动态地给一个对象增加一些额外的职责，就增加对象功能来说，装饰模式比生成子类实现更为灵活。

### 角色

* Component（抽象构件）：它是具体构件和抽象装饰类的共同父类，声明了在具体构件中实现的业务方法，它的引入可以使客户端以一致的方式处理未被装饰的对象以及装饰之后的对象，实现客户端的透明操作。
* ConcreteComponent（具体构件）：它是抽象构件类的子类，用于定义具体的构件对象，实现了在抽象构件中声明的方法，装饰器可以给它增加额外的职责（方法）。
* Decorator（抽象装饰类）：它也是抽象构件类的子类，用于给具体构件增加职责，但是具体职责在其子类中实现。它维护一个指向抽象构件对象的引用，通过该引用可以调用装饰之前构件对象的方法，并通过其子类扩展该方法，以达到装饰的目的。
* ConcreteDecorator（具体装饰类）：它是抽象装饰类的子类，负责向构件添加新的职责。每一个具体装饰类都定义了一些新的行为，它可以调用在抽象装饰类中定义的方法，并可以增加新的方法用以扩充对象的行为。

![decorator.png](/image/decorator.png)

### 示例代码

* Component（抽象构件）

```java
public interface Troll {

  void attack();

  int getAttackPower();

  void fleeBattle();

}
```

* ConcreteComponent（具体构件）

```java
public class SimpleTroll implements Troll {

  private static final Logger LOGGER = LoggerFactory.getLogger(SimpleTroll.class);

  @Override
  public void attack() {
    LOGGER.info("The troll tries to grab you!");
  }

  @Override
  public int getAttackPower() {
    return 10;
  }

  @Override
  public void fleeBattle() {
    LOGGER.info("The troll shrieks in horror and runs away!");
  }
}
```

* Decorator（抽象装饰类）

```java
public class TrollDecorator implements Troll {

  private Troll decorated;

  public TrollDecorator(Troll decorated) {
    this.decorated = decorated;
  }

  @Override
  public void attack() {
    decorated.attack();
  }

  @Override
  public int getAttackPower() {
    return decorated.getAttackPower();
  }

  @Override
  public void fleeBattle() {
    decorated.fleeBattle();
  }
}
```

* ConcreteDecorator（具体装饰类）

```java
public class ClubbedTroll extends TrollDecorator {

  private static final Logger LOGGER = LoggerFactory.getLogger(ClubbedTroll.class);

  public ClubbedTroll(Troll decorated) {
    super(decorated);
  }

  @Override
  public void attack() {
    super.attack();
    LOGGER.info("The troll swings at you with a club!");
  }

  @Override
  public int getAttackPower() {
    return super.getAttackPower() + 10;
  }
}
```

* App

```java
public class App {

  private static final Logger LOGGER = LoggerFactory.getLogger(App.class);

  /**
   * Program entry point
   * 
   * @param args command line args
   */
  public static void main(String[] args) {

    // simple troll
    LOGGER.info("A simple looking troll approaches.");
    Troll troll = new SimpleTroll();
    troll.attack();
    troll.fleeBattle();
    LOGGER.info("Simple troll power {}.\n", troll.getAttackPower());

    // change the behavior of the simple troll by adding a decorator
    LOGGER.info("A troll with huge club surprises you.");
    Troll clubbed = new ClubbedTroll(troll);
    clubbed.attack();
    clubbed.fleeBattle();
    LOGGER.info("Clubbed troll power {}.\n", clubbed.getAttackPower());
  }
}
```
输出结果：
```
00:29:05.834 [main] INFO com.iluwatar.decorator.App - A simple looking troll approaches.
00:29:05.847 [main] INFO com.iluwatar.decorator.SimpleTroll - The troll tries to grab you!
00:29:05.850 [main] INFO com.iluwatar.decorator.SimpleTroll - The troll shrieks in horror and runs away!
00:29:05.850 [main] INFO com.iluwatar.decorator.App - Simple troll power 10.

00:29:05.851 [main] INFO com.iluwatar.decorator.App - A troll with huge club surprises you.
00:29:05.852 [main] INFO com.iluwatar.decorator.SimpleTroll - The troll tries to grab you!
00:29:05.852 [main] INFO com.iluwatar.decorator.ClubbedTroll - The troll swings at you with a club!
00:29:05.852 [main] INFO com.iluwatar.decorator.SimpleTroll - The troll shrieks in horror and runs away!
00:29:05.852 [main] INFO com.iluwatar.decorator.App - Clubbed troll power 20.
```

### 适用场景

* 在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责。
* 当不能采用继承的方式对系统进行扩展或者采用继承不利于系统扩展和维护时可以使用装饰模式。不能采用继承的情况主要有两类：第一类是系统中存在大量独立的扩展，为支持每一种扩展或者扩展之间的组合将产生大量的子类，使得子类数目呈爆炸性增长；第二类是因为类已定义为不能被继承（如Java语言中的final类）。

### 优点

* 对于扩展一个对象的功能，装饰模式比继承更加灵活性，不会导致类的个数急剧增加。
* 可以通过一种动态的方式来扩展一个对象的功能，通过配置文件可以在运行时选择不同的具体装饰类，从而实现不同的行为。
* 可以对一个对象进行多次装饰，通过使用不同的具体装饰类以及这些装饰类的排列组合，可以创造出很多不同行为的组合，得到功能更为强大的对象。
* 具体构件类与具体装饰类可以独立变化，用户可以根据需要增加新的具体构件类和具体装饰类，原有类库代码无须改变，符合“开闭原则”。

### 缺点

* 使用装饰模式进行系统设计时将产生很多小对象，这些对象的区别在于它们之间相互连接的方式有所不同，而不是它们的类或者属性值有所不同，大量小对象的产生势必会占用更多的系统资源，在一定程序上影响程序的性能。
* 装饰模式提供了一种比继承更加灵活机动的解决方案，但同时也意味着比继承更加易于出错，排错也很困难，对于多次装饰的对象，调试时寻找错误可能需要逐级排查，较为繁琐。

### 应用实例

* [java.io.InputStream](http://docs.oracle.com/javase/8/docs/api/java/io/InputStream.html), [java.io.OutputStream](http://docs.oracle.com/javase/8/docs/api/java/io/OutputStream.html),
* [java.io.Reader](http://docs.oracle.com/javase/8/docs/api/java/io/Reader.html) and [java.io.Writer](http://docs.oracle.com/javase/8/docs/api/java/io/Writer.html)
* [java.util.Collections#synchronizedXXX()](http://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedCollection-java.util.Collection-)
* [java.util.Collections#unmodifiableXXX()](http://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#unmodifiableCollection-java.util.Collection-)
* [java.util.Collections#checkedXXX()](http://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedCollection-java.util.Collection-java.lang.Class-)