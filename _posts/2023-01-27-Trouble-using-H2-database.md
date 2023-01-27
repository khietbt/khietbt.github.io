---
layout: post
title:  "Trouble using H2 database"
date:   2023-01-27 16:43:01 +0700
categories: h2 java SpringBoot
---

H2 is a very light relational database, and I tried it for [tomato-config-service](https://github.com/khietbt/tomato-config-service) - a demo of Spring Config Server.

Well, everything looks good, but the properties table has 2 columns: key, value which share the same name with preserved identifiers and cause this error:

```sql
SELECT key, value FROM properties AS p WHERE p.application = ? AND p.profile = ? AND p.label = ?

Syntax error in SQL statement "SELECT [*]key, value FROM properties AS p WHERE p.application = ? AND p.profile = ? AND p.label = ?"; expected "DISTINCT, ALL, *, INTERSECTS (, NOT, EXISTS, UNIQUE, INTERSECTS"; SQL statement:
SELECT key, value FROM properties AS p WHERE p.application = ? AND p.profile = ? AND p.label = ? [42001-214] 42001/42001
```

[Internet advices](https://stackoverflow.com/questions/19758467/h2-database-column-name-group-is-a-reserved-word) did not work, but I found the way of liquibase:

```sql
SELECT p."key", p."value" FROM properties AS p WHERE p.application = ? AND p.profile = ? AND p.label = ?
```

However, MySQL does not accept this syntax. These columns are finally renamed to k, v xD.

```sql
SELECT p.k AS "key", p.v AS "value" FROM properties AS p WHERE p.application = ? AND p.profile = ? AND p.label = ?
```