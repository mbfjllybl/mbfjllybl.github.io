---
title: Java Collections中常用的方法
date: 2021-11-17 15:19:33
updated:
tags: Java
categories:
- [Java]
- [技术转载学习]
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

# Java Collections中常用的方法

类Collections是一个包装类。它包含有各种有关集合操作的静态多态方法。此类不能实例化，就像一个工具类，服务于Java的Collection框架。

`java.util.Collections`

---

**sort()排序方法**

函数定义：`public static <T extends Comparable<? super T>> void sort(List<T> list)` 根据元素的

自然顺序对指定列表按升序进行排序。

参数：要排序的列表。

函数定义： `public static <T> void sort(List<T> list,Comparator<? super T> c)`，根据指定比较器产生的顺序对指定列表进行排序。此列表内的所有元素都必须可使用指定比较器相互比较。

参数：list要排序的列表；c确定列表顺序的比较器。

---

**binarySearch()二分查找方法**

函数定义：`public static <T> int binarySearch(List<? extends Comparable<? super T>> list,T key)`

使用二分搜索法搜索指定列表，以获得指定对象，在进行此方法调用前比较要将列表元素按照升序排序，否则结果不确定，此方法会执行O(n)次链接遍历和O(log n)次元素比较。

参数：list要搜索的链表，key要搜索的键。

函数定义： `public static <T> int binarySearch(List<? extends T> list, T key, Comparator<? super T> c)` 根据指定的比较器对列表进行比较。

参数：list要搜索的列表，key要搜索的键，c排序列表的比较器。

---

**reverse()反转方法**

函数定义：`public static void reverse(List<?> list)`，反转指定列表中元素的顺序，此方法以线性时间运行。

参数：list元素要被反转的列表

---

**shuffle()改组方法**

函数定义：`public static void shuffle(List<?> list)`，使用默认随机源对指定列表进行置换，所有置换发生的可能性都是大致相等的。

参数：list要改组的列表

函数定义：`public static void shuffle(List<?> list,Random rnd)`,使用指定的随机源对指定列表进行置换。

参数：list要改组的列表，rnd用来改组列表的随机源。

---

**swap()交换方法**

函数定义：public static void swap(List<?> list,int i，int j)，在指定列表的指定位置处交换元素。

参数：list进行元素交换的列表，i要交换的一个元素的索引，j要交换的另一个元素的索引。

---

**fill()替换方法**

函数定义：`public static <T> void fill(List<? super T> list,T obj)`，使用指定元素替换指定列表中的所有元素，线性时间运行。

参数：list使用指定元素填充的列表，obj用来填充指定列表的元素。

---

**copy()复制方法**

函数定义：`public static <T> void copy(List<? super T> dest,List<? extends T> src)`，将所有元素从一个列表复制到另一个列表。执行此操作后，目标列表中每个已复制元素的索引将等同于源列表中该元素的索引，目标列表的长度至少必须等于源列表。
参数：dest目标列表，src源列表。

---

**min()最小值法**

函数定义：`public static <T extends Object & Comparable<? super T>> T min(Collection<? extends T> coll)`，根据元素的自然顺序返回给定Collection的最小元素，Collection中的所有元素必须实现Comparable接口，此外，collection中的所有元素都必须是可相互比较的。

参数：coll将确定其最小元素的collection。

函数定义：`public static <T> T min(Collection<? extends T> coll,Comparator<? super T> comp)`，根据指定比较器产生的顺序，返回给定collection的最小元素。

参数：coll将确定其最小元素的collection，comp用来确定最小元素的比较器。

---

**max()最大值方法**

函数定义：`public static <T extends Object & Comparable<? super T>> T max(Collection<? extends T> coll)`，根据元素的自然顺序，返回给定collection的最大元素。

参数：coll将确定其最大元素的collection。

函数定义：`public static <T> T max(Collection<?extends T> coll,Comparator<? super T> comp)`，根据指定比较器产生的顺序，返回给定collection的最大元素。

参数：coll-将确定其最大元素的collection，comp-用来确定最大元素的比较器。

---

**rotate()轮换方法**

 函数定义：`public static void rotate(List<?> list，int distance)`，根据指定的距离轮转指定列表中的元素。
参数：list要轮换的列表，distance列表轮换的距离，可以使0、负数或者大于list.size()的数。

---

**replaceAll()替换所有函数**

函数定义：`public static <T> boolean replaceAll(List<T> list,T oldVal,T newVal)`，使用另一个值替换列表总出现的所有的某一指定值。

参数：list在其中进行替换的列表；oldVal将被替换的原值；newVal替换oldVald的新值。

---

> [link](https://tianjunwei.blog.csdn.net/article/details/48022135?spm=1001.2101.3001.6650.16&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-16.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-16.no_search_link)

