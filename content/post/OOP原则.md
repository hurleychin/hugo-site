+++
draft = true
keywords = [
]
categories = [
]
title = "OOP原则"
description = ""
date = "2016-12-12T00:51:51+08:00"

+++

[http://stackoverflow.com/questions/399656/are-there-any-rules-for-oop](http://stackoverflow.com/questions/399656/are-there-any-rules-for-oop)

SRP The Single Responsibility Principle A class should have only one reason to change.

OCP The Open-Closed Principle Software entities (classes, packages, methods, etc.) should be open for extension, but closed for modification.

LSP The Liskov Substition Principle Subtypes must be substitutable for their base types.

DIP The Dependency Inversion Principle Abstractions should not depend upon details. Details should depend upons abstractions.

ISP The Interface Segregation Principle Clients shold not be forced to depend upon methods that they do not use. Interfaces belong to clients, not to hierarchies.

REP The Release-Reuse Equivalency Principle The granule of reuse is the granule of release.

CCP The Common Closure Principle The classes in a package should be closed together against the same kinds of changes. A change that affects a closed package affects all the classes in that package and no other packages.

CRP The Common Reuse Principle The classes in a package are reused together. If you reuse one of the classes in a package, you reuse them all.

ADP The Acylcic Dependencies Principle Allow no cycles in the dependency graph.

SDP The Stable Dependencies Principle Depend in the direction of stability.

SAP The Stable Abstractions Principle A package should be as abstract as it is stable.