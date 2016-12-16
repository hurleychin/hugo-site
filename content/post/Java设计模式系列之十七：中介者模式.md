+++
title = "Java设计模式系列之十七：中介者模式"
description = ""
keywords = [
]
categories = ["java","design-patterns","behavioral","gang of four"
]
date = "2016-12-16T20:26:50+08:00"

+++

### 定义

用一个中介对象（中介者）来封装一系列的对象交互，中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。中介者模式又称为调停者模式，它是一种对象行为型模式。

### 角色

* Mediator（抽象中介者）：它定义一个接口，该接口用于与各同事对象之间进行通信。
* ConcreteMediator（具体中介者）：它是抽象中介者的子类，通过协调各个同事对象来实现协作行为，它维持了对各个同事对象的引用。
* Colleague（抽象同事类）：它定义各个同事类公有的方法，并声明了一些抽象方法来供子类实现，同时它维持了一个对抽象中介者类的引用，其子类可以通过该引用来与中介者通信。
* ConcreteColleague（具体同事类）：它是抽象同事类的子类；每一个同事对象在需要和其他同事对象通信时，先与中介者通信，通过中介者来间接完成与其他同事类的通信；在具体同事类中实现了在抽象同事类中声明的抽象方法。

中介者模式的核心在于中介者类的引入，在中介者模式中，中介者类承担了两方面的职责：

1. 中转作用（结构性）：通过中介者提供的中转作用，各个同事对象就不再需要显式引用其他同事，当需要和其他同事进行通信时，可通过中介者来实现间接调用。该中转作用属于中介者在结构上的支持。 

2. 协调作用（行为性）：中介者可以更进一步的对同事之间的关系进行封装，同事可以一致的和中介者进行交互，而不需要指明中介者需要具体怎么做，中介者根据封装在自身内部的协调逻辑，对同事的请求进行进一步处理，将同事成员之间的关系行为进行分离和封装。该协调作用属于中介者在行为上的支持。

![mediator_1.png](/image/mediator_1.png)

### 示例代码

* Mediator（抽象中介者）

```java
public interface Party {

  void addMember(PartyMember member);

  void act(PartyMember actor, Action action);

}
```

* ConcreteMediator（具体中介者）

```java
public class PartyImpl implements Party {

  private final List<PartyMember> members;

  public PartyImpl() {
    members = new ArrayList<>();
  }

  @Override
  public void act(PartyMember actor, Action action) {
    for (PartyMember member : members) {
      if (!member.equals(actor)) {
        member.partyAction(action);
      }
    }
  }

  @Override
  public void addMember(PartyMember member) {
    members.add(member);
    member.joinedParty(this);
  }
}
```

* Colleague（抽象同事类）

```java
public interface PartyMember {

  void joinedParty(Party party);

  void partyAction(Action action);

  void act(Action action);
}

public abstract class PartyMemberBase implements PartyMember {

  private static final Logger LOGGER = LoggerFactory.getLogger(PartyMemberBase.class);

  protected Party party;

  @Override
  public void joinedParty(Party party) {
    LOGGER.info("{} joins the party", this);
    this.party = party;
  }

  @Override
  public void partyAction(Action action) {
    LOGGER.info("{} {}", this, action.getDescription());
  }

  @Override
  public void act(Action action) {
    if (party != null) {
      LOGGER.info("{} {}", this, action);
      party.act(this, action);
    }
  }

  @Override
  public abstract String toString();

}
```

* ConcreteColleague（具体同事类）

```java
public class Wizard extends PartyMemberBase {

  @Override
  public String toString() {
    return "Wizard";
  }

}

public class Hobbit extends PartyMemberBase {

  @Override
  public String toString() {
    return "Hobbit";
  }

}

//other party member ……
```

* App

```java
public class App {

  /**
   * Program entry point
   * 
   * @param args command line args
   */
  public static void main(String[] args) {

    // create party and members
    Party party = new PartyImpl();
    Hobbit hobbit = new Hobbit();
    Wizard wizard = new Wizard();
    Rogue rogue = new Rogue();
    Hunter hunter = new Hunter();

    // add party members
    party.addMember(hobbit);
    party.addMember(wizard);
    party.addMember(rogue);
    party.addMember(hunter);

    // perform actions -> the other party members
    // are notified by the party
    hobbit.act(Action.ENEMY);
    wizard.act(Action.TALE);
    rogue.act(Action.GOLD);
    hunter.act(Action.HUNT);
  }
}

public enum Action {

  HUNT("hunted a rabbit", "arrives for dinner"), TALE("tells a tale", "comes to listen"), GOLD(
      "found gold", "takes his share of the gold"), ENEMY("spotted enemies", "runs for cover"), NONE(
      "", "");

  private String title;
  private String description;

  Action(String title, String description) {
    this.title = title;
    this.description = description;
  }

  public String getDescription() {
    return description;
  }

  public String toString() {
    return title;
  }
}

```

### 优点

* 中介者模式简化了对象之间的交互，它用中介者和同事的一对多交互代替了原来同事之间的多对多交互，一对多关系更容易理解、维护和扩展，将原本难以理解的网状结构转换成相对简单的星型结构。
* 中介者模式可将各同事对象解耦。中介者有利于各同事之间的松耦合，我们可以独立的改变和复用每一个同事和中介者，增加新的中介者和新的同事类都比较方便，更好地符合“开闭原则”。
* 可以减少子类生成，中介者将原本分布于多个对象间的行为集中在一起，改变这些行为只需生成新的中介者子类即可，这使各个同事类可被重用，无须对同事类进行扩展。

### 缺点

在具体中介者类中包含了大量同事之间的交互细节，可能会导致具体中介者类非常复杂，使得系统难以维护。

### 适用场景

* 系统中对象之间存在复杂的引用关系，系统结构混乱且难以理解。
* 一个对象由于引用了其他很多对象并且直接和这些对象通信，导致难以复用该对象。
* 想通过一个中间类来封装多个类中的行为，而又不想生成太多的子类。可以通过引入中介者类来实现，在中介者中定义对象交互的公共行为，如果需要改变行为则可以增加新的具体中介者类。

### 应用实例

* All scheduleXXX() methods of [java.util.Timer](http://docs.oracle.com/javase/8/docs/api/java/util/Timer.html)
* [java.util.concurrent.Executor#execute()](http://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html#execute-java.lang.Runnable-)
* submit() and invokeXXX() methods of [java.util.concurrent.ExecutorService](http://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ExecutorService.html)
* scheduleXXX() methods of [java.util.concurrent.ScheduledExecutorService](http://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ScheduledExecutorService.html)
* [java.lang.reflect.Method#invoke()](http://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Method.html#invoke-java.lang.Object-java.lang.Object...-)