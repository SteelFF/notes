对于如下的sql语句
``` sql
select * from A where flag in (select flag from B where B_id<100 ); 

select * from A where exists (select * from B where B_id<100 and A.flag=B.flag);
```
将 '()' 成为内表， '()' 外的表称为外表

**IN原理**

在in()的执行中，先执行内表得到结果集，再执行外表。外表会对所有的内表结果集匹配，也就是说：如果外表有100，内表有10000，就会执行100*10000次。所以在内表比较大的时候，不合适用in()方法，效率比较低。

**EXISTS原理**

exists()的执行过程中，并没有对每一条内表的数据都进行查询，而是存在该条数据的时候会将结果集存起来，到最后的时候同一输出结果集。


**# 总结 #**

1、IN查询在内部表和外部表上都可以使用到索引；

2、EXISTS查询仅内部表上可以使用到索引，外表会全表扫描；当子查询结果集很大，而外部表较小的时候，EXISTS的Block Nested Loop(Block 嵌套循环)的作用开始显现，查询效率会优于IN；

3、当子查询结果集较小，而外部表很大的时候，EXISTS的Block嵌套循环优化效果不明显，IN 的外表索引优势占主要作用，此时IN的查询效率会优于EXISTS。

子查询结果集越大用EXISTS，子查询结果集越小用IN。