---
layout: post
title: "Zero Date in MySQL"
date: 2023-01-27 16:43:01 +0700
categories: MySQL Database
---

Last week, a guy accidentally inserted a record with a wrong value into a datetime column. It was turned into 0000-00-00
00:00:00, and broke an API with a message:

```
java.sql.SQLException: Zero date value prohibited
```

Internet advices told us to bypass this by adding zeroDateTimeBehavior=convertToNull to the connection string. While the
superman came with a NO_ZERO_DATE mode.

Have fun.

References:

* https://stackoverflow.com/questions/66991542/mysql-how-to-avoid-block-insert-date-with-zero-value-0000-00-00
* https://www.taogenjia.com/2021/05/26/JDBC-Error-Java-sql-SQLException-Zero-Date-value-Prohibited/