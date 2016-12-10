+++
description = ""
keywords = [
]
categories = ["java","design-patterns","structural","gang of four"
]
date = "2016-12-10T15:33:14+08:00"
title = "Java设计模式系列之八：组合模式"

+++

### 定义

组合多个对象形成树形结构以表示具有“整体—部分”关系的层次结构。组合模式对单个对象（即叶子对象）和组合对象（即容器对象）的使用具有一致性，组合模式又可以称为“整体—部分”(Part-Whole)模式，它是一种对象结构型模式。

### 角色

* Component（抽象构件）：它可以是接口或抽象类，为叶子构件和容器构件对象声明接口，在该角色中可以包含所有子类共有行为的声明和实现。在抽象构件中定义了访问及管理它的子构件的方法，如增加子构件、删除子构件、获取子构件等。
* Leaf（叶子构件）：它在组合结构中表示叶子节点对象，叶子节点没有子节点，它实现了在抽象构件中定义的行为。对于那些访问及管理子构件的方法，可以通过异常等方式进行处理。
* Composite（容器构件）：它在组合结构中表示容器节点对象，容器节点包含子节点，其子节点可以是叶子节点，也可以是容器节点，它提供一个集合用于存储子节点，实现了在抽象构件中定义的行为，包括那些访问及管理子构件的方法，在其业务方法中可以递归调用其子节点的业务方法。

![composite_1.png](/image/composite_1.png)

### 示例代码

* Component（抽象构件）

``` java
public abstract class LetterComposite {

  private List<LetterComposite> children = new ArrayList<>();

  public void add(LetterComposite letter) {
    children.add(letter);
  }

  public int count() {
    return children.size();
  }

  protected abstract void printThisBefore();

  protected abstract void printThisAfter();

  /**
   * Print
   */
  public void print() {
    printThisBefore();
    for (LetterComposite letter : children) {
      letter.print();
    }
    printThisAfter();
  }
}
```

* Leaf（叶子构件）

``` java
public class Letter extends LetterComposite {

  private char c;

  public Letter(char c) {
    this.c = c;
  }

  @Override
  protected void printThisBefore() {
    System.out.print(c);
  }

  @Override
  protected void printThisAfter() {
    // nop
  }
}

```

* Composite（容器构件）

``` java

public class Word extends LetterComposite {

  /**
   * Constructor
   */
  public Word(List<Letter> letters) {
    for (Letter l : letters) {
      this.add(l);
    }
  }

  @Override
  protected void printThisBefore() {
    System.out.print(" ");
  }

  @Override
  protected void printThisAfter() {
    // nop
  }
}

public class Sentence extends LetterComposite {

  /**
   * Constructor
   */
  public Sentence(List<Word> words) {
    for (Word w : words) {
      this.add(w);
    }
  }

  @Override
  protected void printThisBefore() {
    // nop
  }

  @Override
  protected void printThisAfter() {
    System.out.print(".");
  }
}
```

### 优点

* 组合模式可以清楚地定义分层次的复杂对象，表示对象的全部或部分层次，它让客户端忽略了层次的差异，方便对整个层次结构进行控制。
* 客户端可以一致地使用一个组合结构或其中单个对象，不必关心处理的是单个对象还是整个组合结构，简化了客户端代码。
* 在组合模式中增加新的容器构件和叶子构件都很方便，无须对现有类库进行任何修改，符合“开闭原则”。
* 组合模式为树形结构的面向对象实现提供了一种灵活的解决方案，通过叶子对象和容器对象的递归组合，可以形成复杂的树形结构，但对树形结构的控制却非常简单。

### 缺点

在增加新构件时很难对容器中的构件类型进行限制。有时候我们希望一个容器中只能有某些特定类型的对象，例如在某个文件夹中只能包含文本文件，使用组合模式时，不能依赖类型系统来施加这些约束，因为它们都来自于相同的抽象层，在这种情况下，必须通过在运行时进行类型检查来实现，这个实现过程较为复杂。

### 适用场景

* 在具有整体和部分的层次结构中，希望通过一种方式忽略整体与部分的差异，客户端可以一致地对待它们。
* 在一个使用面向对象语言开发的系统中需要处理一个树形结构。
* 在一个系统中能够分离出叶子对象和容器对象，而且它们的类型不固定，需要增加一些新的类型。

### 应用实例

* [java.awt.Container](http://docs.oracle.com/javase/8/docs/api/java/awt/Container.html)
* [java.awt.Component](http://docs.oracle.com/javase/8/docs/api/java/awt/Component.html)
