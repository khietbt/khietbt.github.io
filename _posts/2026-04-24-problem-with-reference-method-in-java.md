---
layout: post
title: Problem with Reference Method in Java
description: "Using reference methods can cause OutOfMemoryException "
date: 2026-04-24T21:22:00.000+07:00
image: https://i0.wp.com/javaconceptoftheday.com/wp-content/uploads/2019/03/Java8MethodReferences.png
categories:
  - java
tags:
  - java
  - OutOfMemoryException
  - computeIfAbsent
---
This afternoon, using reference method gave me an OutOfMemoryException with Map::computeIfAbsent

```
cached.computeIfAbsent(n, ArrayList::new).add(i);
```

It was solved by switching to lambda:

```
cached.computeIfAbsent(n, k -> new ArrayList<>()).add(i);
```
[More details can be found here.](https://medium.com/@mgautham1995/one-thing-to-remember-while-using-method-references-2d283bce96ac)
