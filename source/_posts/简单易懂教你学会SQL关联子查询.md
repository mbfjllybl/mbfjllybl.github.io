---
title: 简单易懂教你学会SQL关联子查询
date: 2021-11-07 22:38:33
updated:
tags: 关联子查询
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

# 简单易懂教你学会SQL关联子查询

初学SQL的人都会觉得SQL的关联子查询难以理解，为什么？这是有原因的。

关联子查询的执行逻辑和通常的SELECT语句的执行逻辑完成不一样。这就是SQL关联子查询难以理解的原因。

我们首先来看看正常情况下SELECT的书写顺序和执行顺序：

**书写顺序：**

SELECT > FROM > WHERE > GROUP BY > HAVE > ORDER BY

**执行顺序：**

FROM > WHERE > GROUP BY > HAVE > SELECT > ORDER BY

以下以Product表为例：

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080004369.png)

执行以下代码说明执行过程：

```sql
SELECT product_type, count(*)

FROM Product

WHERE sale_price > 100

GROUP BY product_type

HAVING count(*) > 1

ORDER BY product_type
```

执行过程如下：

**1、FROM product**

指定从Product表获取数据**。**

**2、WHERE sale_price > 100**

筛选销售价大于100的：

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080006692.png)

**3、GROUP BY product_type**

以product_type列分组：

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080007617.png)

**4、HAVING count(\*) > 1**

筛选出组内数据行大于1的

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080007978.png)

**5、SELECT product_type, count(*)**

筛选出product_type、count(*)列

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080008234.png)

**6、ORDER BY product_type**

以 product_type列排序

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080008730.png)

**上面我们说明了SELECT语句的书写和执行过程。我们再来看看以下“选取出各商品种类中高于该商品种类的平均销售单价的商品。” 的关联子查询SELECT语句：**

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080009207.png)

**按照我们正常的select语句执行逻辑，应该从WHERE字句中的括号内的子查询开始，子查询也是一个SELECT语句，应该这样开始执行：**

1. **FROM Product AS P2 ：指定从Product获取数据。**
2. **WHERE P1.product_type = P2.product_type**

**这里的P1和P2都是指向Product表，都是它的别名，所以P1.product_type与P2.product_type必然是相等的，所以这个WHERE字句是废话。**

**既然是废话，我们删除这句废话看看：**

```sql
SELECT product_type , product_name, sale_price

FROM Product AS P1

WHERE sale_price > (

SELECT AVG(sale_price)

FROM Product AS P2

GROUP BY product_type);
```

**这个语句是错误的，原因是sale_price是一行数据，子查询的结果是3行数据，无法进行比较。更无法得到我们想要的“选取出各商品种类中高于该商品种类的平均销售单价的商品。”这个结果。**

**实际上，‘WHERE P1.product_type = P2.product_type’这个语句是关联子查询，关联子查询的执行逻辑和正常的SELECT语句执行逻辑完全不同，这就是它的诡异之处了（不知道规定它的人是怎么想的，搞那么复杂的逻辑）。**

**下面，我们来说明一下上面的关联子查询代码的执行过程：**

**记住，关联子查询和正常的SELECT语句完全不同。**

**1、先执行主查询**

**SELECT product _type , product_name, sale_price**

**FROM Product AS P1**

**结果：**

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080014505.png)

**2、从主查询的product _type先取第一个值=‘衣服’，通过WHERE P1.product_type = P2.product_type传入子查询，子查询变成：**

**SELECT AVG(sale_price)**

**FROM Product AS P2**

**WHERE P2.product_type = ‘衣服’**

**GROUP BY product_type);**

> 这个group by 子句删除也是对的，写了也不会报错

**第一次子查询结果：**

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080016945.png)

**从子查询得到的结果AVG(sale_price)=2500，返回主查询：**

**SELECT product_type , product_name, sale_price**

**FROM Product AS P1**

**WHERE sale_price > 2500 AND product_type = ‘衣服’**

**第一次整个语句的结果：**

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080018659.png)

**3、然后，product _type取第二个值，得到整个语句的第二结果，依次类推，把product _type全取值一遍，就得到了整个语句的结果集。结果如下：**

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111080018424.png)

**事实上，所有关联子查询的执行过程都和上面的过程一样。**

**总结：**

**1、关联子查询的执行逻辑完全不同于正常的SELECT语句。**

**2、关联子查询执行逻辑如下：**

**（1）先从主查询的Product表中product _type列取出第一个值，进入子查询中，得到子查询结果，然后返回父查询，判断父查询的where子句条件，则返回整个语句的第1条结果。**

**（2）重复上述操作，直到所有主查询中的Product表中product _type列记录取完为止。得出整个语句的结果集，就是最后的答案。**

> [link](https://zhuanlan.zhihu.com/p/41844742)