
选自CSDN博主原创文章。[原文链接](https://blog.csdn.net/qq_22996201/article/details/97950839)

之所以写这篇文章，是因为在一次项目中后台的一条长sql执行的非常慢，通过explain sql发现join时一张表没有走索引，所以为了解决这个问题研究了join的原理和join索引的使用规则。
  
Mysql中Join本质上是Nested-Loop Join（嵌套循环链接），但他有很多种变种，能够帮助Mysql更高效的执行JOIN操作。

## Simple Nested-Loop Join
![[Pasted image 20230621095053.png]]
这是在两张表都没有索引的情况下使用的join算法。从驱动表中依次拿出列来和另一张表进行匹配。首先驱动表拿出r1和s表的所有列进行比对匹配，以此类推，这种算法要对s表进行rn次访问，开销极大。

## Index Nested-Loop Join
![[Pasted image 20230621095110.png]]
这种算法就是有索引的情况，无论是A去join B还是B去join A，驱动表的选择都不是我们自身，而是由mysql优化器自主选择的。哪一个表作为驱动表开销小，那么mysql就会把那一张表就作为驱动表。有了索引，那么比较的时候就不再一条一条的进行比较，可以根据索引来快速定位减少比较，为了能让mysql自主选好哪一张表作为驱动表开销最小，则在on的后面关联的字段都必须是索引字段。
  
如果还想提高性能，那么非驱动表的关联键设置为主键效果更佳。之所以说选择对了驱动表会减少开销，是因为：首先，where条件可以减少A表和B表的扫描条数，如果没有where条件，那么如果A表有100行，B表有50行，on后面都走了A表和B表的索引，但是驱动表一定会全表扫描，如果A表作为驱动表，则最终匹配条数是100n（B表索引条数），如果B表作为驱动表，那么最终匹配条数50n（A表索引条数）。

在项目中遇到的问题： 对于走索引的问题，假如A表有10行，B表有10000行。我们去join，一定是把行数最小的作为主表，然后去join行数多的，这样对于索引而言扫描的行数会少很多。在join之后On的条件，类型不同是无法走索引的，也就是说如果on A.id = B.id，虽然A表和B表的id都设置了索引，但是A表的id是Int，而B表的id是varchar，则无法走索引。其次，字符编码也会导致无法走索引。字符编码常见的是utf8和utf8mb4，utf8mb4是可以兼容utf8的，也就是说如果A表是utf8mb4，B表是utf8，则on A.uinstanceid = B. uinstanceid是可以走索引的，但是如果把B表当作主表，让B去join A on B.uinstanceid = A. uinstanceid则无法走索引。
  
所以，如果我们A表是utf8，B表是utf8mb4，则必须拿B表作为主表去join才走索引，那么至少扫描的就是10000行，因此我们尽量让行数少的作为主表，这样扫描的行数就少很多。
  
可以通过explain sql来查看该sql中查询的表索引使用的字节数，特别是在联合嵌套、组合索引时，可以根据explain sql查看key_len看这条sql中哪些表用了多少索引。
  
而explain sql中rows是该sql中查询表走的行数，就可以根据rows来判断为什么该sql跑的很慢的原因。最终这条sql走的条数是所有rows的乘积。

## Block Nested-Loop Join
![[Pasted image 20230621095121.png]]

在有索引的情况下，MySQL会尝试去使用Index Nested-Loop Join算法，在有些情况下，可能Join的列就是没有索引，那么这时MySQL的选择绝对不会是最先介绍的Simple Nested-Loop Join算法，而是会优先使用Block Nested-Loop Join的算法。

Block Nested-Loop Join对比Simple Nested-Loop Join多了一个中间处理的过程，也就是join buffer，使用join buffer将驱动表的查询JOIN相关列都给缓冲到了JOIN BUFFER当中，然后批量与非驱动表进行比较，这样可以将多次比较合并到一次，降低了非驱动表的访问频率。

在MySQL当中，我们可以通过参数join_buffer_size来设置join buffer的值，然后再进行操作。默认情况下join_buffer_size=256K，在查找的时候MySQL会将所有的需要的列缓存到join buffer当中，包括select的列，而不是仅仅只缓存关联列。


# 总结

传统关系型数据库有索引，可以通过给两张表建立索引的方法进行优化，注意两张表的索引字段的类型、长度、编码格式必须一致，否则优化不会生效。(join字段设置索引，一般都是主键)

**（此外，编码格式有向下兼容的，一定要注意join的顺序，两张表join位置互换效果是不一样的，因此应当是有向下兼容编码格式的索引的表作为驱动表放join左边。有索引情况下优化方向是小表join大表，因为大表作为非驱动表有索引，而小表作为驱动表需全表扫描，部分数据库会自动进行优化）**
