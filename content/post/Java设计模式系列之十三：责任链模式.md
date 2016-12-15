+++
description = ""
keywords = [
]
categories = ["java","design-patterns","behavioral","gang of four"
]
date = "2016-12-15T21:52:19+08:00"
title = "Java设计模式系列之十三：责任链模式"

+++

### 定义

职责链模式(Chain of Responsibility Pattern)：避免请求发送者与接收者耦合在一起，让多个对象都有可能接收请求，将这些对象连接成一条链，并且沿着这条链传递请求，直到有对象处理它为止。职责链模式是一种对象行为型模式。

### 角色

* Handler（抽象处理者）：它定义了一个处理请求的接口，一般设计为抽象类，由于不同的具体处理者处理请求的方式不同，因此在其中定义了抽象请求处理方法。因为每一个处理者的下家还是一个处理者，因此在抽象处理者中定义了一个抽象处理者类型的对象（如结构图中的successor），作为其对下家的引用。通过该引用，处理者可以连成一条链。
* ConcreteHandler（具体处理者）：它是抽象处理者的子类，可以处理用户请求，在具体处理者类中实现了抽象处理者中定义的抽象请求处理方法，在处理请求之前需要进行判断，看是否有相应的处理权限，如果可以处理请求就处理它，否则将请求转发给后继者；在具体处理者中可以访问链中下一个对象，以便请求的转发。

![chain_1.png](/image/chain_1.png)

### 示例代码

* Handler（抽象处理者）

```java
public abstract class RequestHandler {

  private static final Logger LOGGER = LoggerFactory.getLogger(RequestHandler.class);

  private RequestHandler next;

  public RequestHandler(RequestHandler next) {
    this.next = next;
  }

  /**
   * Request handler
   */
  public void handleRequest(Request req) {
    if (next != null) {
      next.handleRequest(req);
    }
  }

  protected void printHandling(Request req) {
    LOGGER.info("{} handling request \"{}\"", this, req);
  }

  @Override
  public abstract String toString();
}
```

* ConcreteHandler（具体处理者）

```java
public class OrcCommander extends RequestHandler {

  public OrcCommander(RequestHandler handler) {
    super(handler);
  }

  @Override
  public void handleRequest(Request req) {
    if (req.getRequestType().equals(RequestType.DEFEND_CASTLE)) {
      printHandling(req);
      req.markHandled();
    } else {
      super.handleRequest(req);
    }
  }

  @Override
  public String toString() {
    return "Orc commander";
  }
}

public class OrcOfficer extends RequestHandler {

  public OrcOfficer(RequestHandler handler) {
    super(handler);
  }

  @Override
  public void handleRequest(Request req) {
    if (req.getRequestType().equals(RequestType.TORTURE_PRISONER)) {
      printHandling(req);
      req.markHandled();
    } else {
      super.handleRequest(req);
    }
  }

  @Override
  public String toString() {
    return "Orc officer";
  }

}

public class OrcSoldier extends RequestHandler {

  public OrcSoldier(RequestHandler handler) {
    super(handler);
  }

  @Override
  public void handleRequest(Request req) {
    if (req.getRequestType().equals(RequestType.COLLECT_TAX)) {
      printHandling(req);
      req.markHandled();
    } else {
      super.handleRequest(req);
    }
  }

  @Override
  public String toString() {
    return "Orc soldier";
  }
}

```

* Request

```java
public class Request {

  /**
   * The type of this request, used by each item in the chain to see if they should or can handle
   * this particular request
   */
  private final RequestType requestType;

  /**
   * A description of the request
   */
  private final String requestDescription;

  /**
   * Indicates if the request is handled or not. A request can only switch state from unhandled to
   * handled, there's no way to 'unhandle' a request
   */
  private boolean handled;

  /**
   * Create a new request of the given type and accompanied description.
   *
   * @param requestType        The type of request
   * @param requestDescription The description of the request
   */
  public Request(final RequestType requestType, final String requestDescription) {
    this.requestType = Objects.requireNonNull(requestType);
    this.requestDescription = Objects.requireNonNull(requestDescription);
  }

  /**
   * Get a description of the request
   *
   * @return A human readable description of the request
   */
  public String getRequestDescription() {
    return requestDescription;
  }

  /**
   * Get the type of this request, used by each person in the chain of command to see if they should
   * or can handle this particular request
   *
   * @return The request type
   */
  public RequestType getRequestType() {
    return requestType;
  }

  /**
   * Mark the request as handled
   */
  public void markHandled() {
    this.handled = true;
  }

  /**
   * Indicates if this request is handled or not
   *
   * @return <tt>true</tt> when the request is handled, <tt>false</tt> if not
   */
  public boolean isHandled() {
    return this.handled;
  }

  @Override
  public String toString() {
    return getRequestDescription();
  }

}

public enum RequestType {

  DEFEND_CASTLE, TORTURE_PRISONER, COLLECT_TAX

}

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

    OrcKing king = new OrcKing();
    king.makeRequest(new Request(RequestType.DEFEND_CASTLE, "defend castle"));
    king.makeRequest(new Request(RequestType.TORTURE_PRISONER, "torture prisoner"));
    king.makeRequest(new Request(RequestType.COLLECT_TAX, "collect tax"));

  }
}

public class OrcKing {

  RequestHandler chain;

  public OrcKing() {
    buildChain();
  }

  private void buildChain() {
    chain = new OrcCommander(new OrcOfficer(new OrcSoldier(null)));
  }

  public void makeRequest(Request req) {
    chain.handleRequest(req);
  }

}
```

### 适用场景

* 有多个对象可以处理同一个请求，具体哪个对象处理该请求待运行时刻再确定，客户端只需将请求提交到链上，而无须关心请求的处理对象是谁以及它是如何处理的。
* 在不明确指定接收者的情况下，向多个对象中的一个提交一个请求。
* 可动态指定一组对象处理请求，客户端可以动态创建职责链来处理请求，还可以改变链中处理者之间的先后次序。

### 优点

* 职责链模式使得一个对象无须知道是其他哪一个对象处理其请求，对象仅需知道该请求会被处理即可，接收者和发送者都没有对方的明确信息，且链中的对象不需要知道链的结构，由客户端负责链的创建，降低了系统的耦合度。
* 请求处理对象仅需维持一个指向其后继者的引用，而不需要维持它对所有的候选处理者的引用，可简化对象的相互连接。
* 在给对象分派职责时，职责链可以给我们更多的灵活性，可以通过在运行时对该链进行动态的增加或修改来增加或改变处理一个请求的职责。
* 在系统中增加一个新的具体请求处理者时无须修改原有系统的代码，只需要在客户端重新建链即可，从这一点来看是符合“开闭原则”的。

### 缺点

* 由于一个请求没有明确的接收者，那么就不能保证它一定会被处理，该请求可能一直到链的末端都得不到处理；一个请求也可能因职责链没有被正确配置而得不到处理。
* 对于比较长的职责链，请求的处理可能涉及到多个处理对象，系统性能将受到一定影响，而且在进行代码调试时不太方便。
* 如果建链不当，可能会造成循环调用，将导致系统陷入死循环。

### 应用实例

* [java.util.logging.Logger#log()](http://docs.oracle.com/javase/8/docs/api/java/util/logging/Logger.html#log%28java.util.logging.Level,%20java.lang.String%29)
* [Apache Commons Chain](https://commons.apache.org/proper/commons-chain/index.html)
* [javax.servlet.Filter#doFilter()](http://docs.oracle.com/javaee/7/api/javax/servlet/Filter.html#doFilter-javax.servlet.ServletRequest-javax.servlet.ServletResponse-javax.servlet.FilterChain-)