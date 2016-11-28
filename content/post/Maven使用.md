+++
date = "2016-11-29T00:58:44+08:00"
title = "Maven简单使用"
description = ""
keywords = [
]
categories = [
]

+++

1. Maven创建项目

```
mvn archetype:generate
```

2. Maven编译项目

```
mnv compile
``` 

3. Maven运行Main方法

```
mvn exec:java -Dexec.mainClass="org.evil.test.App"
```

