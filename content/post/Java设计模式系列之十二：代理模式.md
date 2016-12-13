+++
keywords = [
]
categories = ["java","design-patterns","structural","gang of four"
]
date = "2016-12-14T01:50:18+08:00"
title = "Java设计模式系列之十二：代理模式"
description = ""

+++

### 定义

给某一个对象提供一个代理或占位符，并由代理对象来控制对原对象的访问。
Provide a surrogate or placeholder for another object to control access to it.

### 角色

* Subject（抽象主题角色）：它声明了真实主题和代理主题的共同接口，这样一来在任何使用真实主题的地方都可以使用代理主题，客户端通常需要针对抽象主题角色进行编程。
* Proxy（代理主题角色）：它包含了对真实主题的引用，从而可以在任何时候操作真实主题对象；在代理主题角色中提供一个与真实主题角色相同的接口，以便在任何时候都可以替代真实主题；代理主题角色还可以控制对真实主题的使用，负责在需要的时候创建和删除真实主题对象，并对真实主题对象的使用加以约束。通常，在代理主题角色中，客户端在调用所引用的真实主题操作之前或之后还需要执行其他操作，而不仅仅是单纯调用真实主题对象中的操作。
* RealSubject（真实主题角色）：它定义了代理角色所代表的真实对象，在真实主题角色中实现了真实的业务操作，客户端可以通过代理主题角色间接调用真实主题角色中定义的操作。

![proxy.png](/image/proxy.png)

### 示例代码

* Subject（抽象主题角色）

```java
public interface WizardTower {

  void enter(Wizard wizard);
}
```

* Proxy（代理主题角色）

```java
public class WizardTowerProxy implements WizardTower {

  private static final Logger LOGGER = LoggerFactory.getLogger(WizardTowerProxy.class);

  private static final int NUM_WIZARDS_ALLOWED = 3;

  private int numWizards;

  private final WizardTower tower;

  public WizardTowerProxy(WizardTower tower) {
    this.tower = tower;
  }

  @Override
  public void enter(Wizard wizard) {
    if (numWizards < NUM_WIZARDS_ALLOWED) {
      tower.enter(wizard);
      numWizards++;
    } else {
      LOGGER.info("{} is not allowed to enter!", wizard);
    }
  }
}

public class Wizard {

  private final String name;

  public Wizard(String name) {
    this.name = name;
  }

  @Override
  public String toString() {
    return name;
  }

}
```

* RealSubject（真实主题角色）

```java
public class IvoryTower implements WizardTower {

  private static final Logger LOGGER = LoggerFactory.getLogger(IvoryTower.class);

  public void enter(Wizard wizard) {
    LOGGER.info("{} enters the tower.", wizard);
  }

}

```

* App

```java
public class App {

  /**
   * Program entry point
   */
  public static void main(String[] args) {

    WizardTowerProxy proxy = new WizardTowerProxy(new IvoryTower());
    proxy.enter(new Wizard("Red wizard"));
    proxy.enter(new Wizard("White wizard"));
    proxy.enter(new Wizard("Black wizard"));
    proxy.enter(new Wizard("Green wizard"));
    proxy.enter(new Wizard("Brown wizard"));

  }
}
```

out print:
```
00:54:24.023 [main] INFO com.iluwatar.proxy.IvoryTower - Red wizard enters the tower.
00:54:24.062 [main] INFO com.iluwatar.proxy.IvoryTower - White wizard enters the tower.
00:54:24.063 [main] INFO com.iluwatar.proxy.IvoryTower - Black wizard enters the tower.
00:54:24.063 [main] INFO com.iluwatar.proxy.WizardTowerProxy - Green wizard is not allowed to enter!
00:54:24.063 [main] INFO com.iluwatar.proxy.WizardTowerProxy - Brown wizard is not allowed to enter!
```

### 优点

代理模式的共同优点如下：

* 能够协调调用者和被调用者，在一定程度上降低了系统的耦合度。
* 客户端可以针对抽象主题角色进行编程，增加和更换代理类无须修改源代码，符合开闭原则，系统具有较好的灵活性和可扩展性。

此外，不同类型的代理模式也具有独特的优点，例如：

* 远程代理为位于两个不同地址空间对象的访问提供了一种实现机制，可以将一些消耗资源较多的对象和操作移至性能更好的计算机上，提高系统的整体运行效率。
* 虚拟代理通过一个消耗资源较少的对象来代表一个消耗资源较多的对象，可以在一定程度上节省系统的运行开销。
* 缓冲代理为某一个操作的结果提供临时的缓存存储空间，以便在后续使用中能够共享这些结果，优化系统性能，缩短执行时间。
* 保护代理可以控制对一个对象的访问权限，为不同用户提供不同级别的使用权限。

### 缺点

* 由于在客户端和真实主题之间增加了代理对象，因此有些类型的代理模式可能会造成请求的处理速度变慢，例如保护代理。
* 实现代理模式需要额外的工作，而且有些代理模式的实现过程较为复杂，例如远程代理。


### 适用场景

* 当客户端对象需要访问远程主机中的对象时可以使用远程代理。
* 当需要用一个消耗资源较少的对象来代表一个消耗资源较多的对象，从而降低系统开销、缩短运行时间时可以使用虚拟代理，例如一个对象需要很长时间才能完成加载时。
* 当需要为某一个被频繁访问的操作结果提供一个临时存储空间，以供多个客户端共享访问这些结果时可以使用缓冲代理。通过使用缓冲代理，系统无须在客户端每一次访问时都重新执行操作，只需直接从临时缓冲区获取操作结果即可。
* 当需要控制对一个对象的访问，为不同用户提供不同级别的访问权限时可以使用保护代理。
* 当需要为一个对象的访问（引用）提供一些额外的操作时可以使用智能引用代理。

### 应用实例

* [java.lang.reflect.Proxy](http://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Proxy.html)
* [Apache Commons Proxy](https://commons.apache.org/proper/commons-proxy/)
* Mocking frameworks Mockito, Powermock, EasyMock