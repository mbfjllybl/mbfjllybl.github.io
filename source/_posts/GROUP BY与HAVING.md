---
title: GROUP BY与HAVING
date: 2021-11-07 21:38:33
updated:
tags: GROUP BY
categories: MySQL
keywords: 
description:
top_img:
comments:
cover:
toc:
toc_number:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
mathjax:
katex:
aplayer:
highlight_shrink:
aside:
---

## GROUP BY

GROUP BY语句用来与聚合函数(aggregate functions such as COUNT, SUM, AVG, MIN, or MAX.)联合使用来得到一个或多个列的结果集。

**语法**如下：

```sql
SELECT column1, column2, ... column_n, aggregate_function(expression)           

FROM tables            

WHERE predicates            

GROUP BY column1, column2, ... column_n;
```

**举例**

比如说我们有一个学生表格(student)，包含学号(id)，课程(course)，分数(score)等等多个列，我们想通过查询得到每个学生选了几门课程，此时我们就可以联合使用COUNT函数与GROUP BY语句来得到这一结果

```sql
SELECT id, COUNT(course) as numcourse

FROM student

GROUP BY id
```

因为我们是使用学号来进行分组的，这样COUNT函数就是在以学号分组的前提下来实现的，通过COUNT(course)就可以计算每一个学号对应的课程数。

**注意**

**因为聚合函数通过作用于一组数据而只返回一个单个值，因此，在SELECT语句中出现的元素要么为一个聚合函数的输入值，要么为GROUP BY语句的参数，否则会出错。**

例如，对于上面提到的表格，我们做一个这样的查询：

```sql
SELECT id, COUNT(course) as numcourse, score

FROM student

GROUP BY id
```

此时查询便会出错，错误提示如下：

`Column ‘student.score' is invalid in the select list because it is not contained in either an aggregate function or the GROUP BY clause.`

出现以上错误的原因是因为一个学生id对应多个分数，如果我们简单的在SELECT语句中写上score，则无法判断应该输出哪一个分数。如果想用score作为select语句的参数可以将它用作一个聚合函数的输入值，如下例，我们可以得到每个学生所选的课程门数以及每个学生的平均分数：

```sql
SELECT id, COUNT(course) as numcourse, AVG(score) as avgscore

FROM student

GROUP BY id
```

##  HAVING

HAVING语句通常与GROUP BY语句联合使用，用来过滤由GROUP BY语句返回的记录集。

HAVING语句的存在弥补了WHERE关键字不能与聚合函数联合使用的不足。

```sql
SELECT column1, column2, ... column_n, aggregate_function (expression)
FROM tables
WHERE predicates
GROUP BY column1, column2, ... column_n
HAVING condition1 ... condition_n;
```

同样使用本文中的学生表格，如果想查询平均分高于80分的学生记录可以这样写：

```sql
SELECT id, COUNT(course) as numcourse, AVG(score) as avgscore

FROM student

GROUP BY id

HAVING AVG(score)>=80;
```

在这里，如果用WHERE代替HAVING就会出错