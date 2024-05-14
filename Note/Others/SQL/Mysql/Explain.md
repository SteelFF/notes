
Explain关键字一般放置在sql语句之前用于查看sql语句加载表的执行顺序，为开发者提供sql语句的优化思路。

Explain查询返回结果有以下12个字段：

 ```
 - id ( type: number )
 - select_type ( type: string )
 - table ( type: string )
 - partitions ( type: string )
 - possible_keys ( type: string )
 - key ( type: string )
 - key_len ( type: string )
 - ref ( type: string )
 - rows ( type: number )
 - filtered ( type: number )
 - Extra ( type: string )
```

### id

id 用于标识表的加载顺序，相同id的表加载顺序为自上向下；不同id的表加载顺序由id大的往id小的加载。

### select_type

用于区别加载表数据用于操作的类型。

枚举类型如下：
 ```
 - SIMPLE: 简单的select查询， 查询当中不包含子查询或者UNION
 - PRIMARY: 查询中包含任何复杂的子部分，最外层查询则被标记为PRIMARY
 - SUBQUERY: 在SELECT或WHERE列表当中包含了子查询
 - DERIVERD: 在FROM列表中包含的子查询被标记为DERIVED(衍生)，MySQL会递归地执行这些子查询，把结果放到临时表里。
 - UNION: 若第二个SELECT出现在UNION之后，则被标记为UNION；若UNION包含在FROM字句的子查询中，外层SELECT将被标记为：DERIVED
 - UNION RESULT: 从UNION表获取结果的SELECT
 ```

### table

当前的数据是来源于哪张表的

### type

区分当前查询的执行类型，比如是全表扫描，还是依据索引查询（优化的一个大点）。
请依据表的大小分辨哪种查询最适合业务逻辑。

常见的枚举类型如下：
```
- ALL: 全表扫描
- index: Full Index Scan, index与ALL区别为index类型只遍历索引树。这通常比ALL快，因为索引文件通常比数据文件小。(ALL与index都读取全表，只是index是从索引当中读取的，而ALL是直接从硬盘当中读取的)
- range: 只检索给定范围的行，使用一个索引来选择行。key列显示使用了哪个索引，一般就是在where语句当中出现了between、<、>、in等的查询，这种范围的扫描索引扫描比全表扫描要好，因为它只需要开始于索引的某一点，而结束语句另一点，不用扫描全部索引。
- ref: 非唯一性索引扫描，返回匹配某个单独值的所有行，本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，它可能会找到多个符合条件的行，所以他应该属于查找与扫描的混合体。
- eq_ref: 唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配，常见于主键或唯一索引扫描
- const: 表通过索引一次就找到了, const表用于比较primary key或者unique索引。因为只匹配一行数据, 所以很快将主键置于where表当中, MySQL就能将改查询转换为一个常量。
- system: 表只有一条记录（相当于系统表），是const的特例，平时业务当中不会出现，可以忽略不计
- NULL
```

其中，从最好到最差的依次是:
 system>const>eq_ref>ref>range>index>ALL


### possible_keys

显示可能应用在这张表中的索引，一个或者多个。
查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询实际使用。


### key

实际用到的表的索引。如果没有使用索引，则值为NULL。
<b style = 'color: blue'>查询中若使用了覆盖索引，则该索引仅出现在key列表当中</b>

`SELECT *` 会取全部的数据，对于覆盖索引不能正确调用，会全表扫描，而指定SELECT列和SELECT列的顺序能够使MYSQL引擎找到正确的覆盖索引，从而使用索引扫描

### key_len

表示索引中使用到的字节数，可通过该列计算查询中使用的索引的长度，在不损失精确性的情况下，长度越短越好。
key_len显示的值为索引字段最大可能长度，<b style='color: red'>而非实际使用长度</b>，即key_len是根据表定义计算而得，不是通过表内检索出的。

<b>同样查询精度的情况下，key_len越小越好</b>

### ref

显示索引的哪一列被使用了，如果可能的话，是一个常数。哪些列或常量被用于查找索引列上的值


### rows

根据表统计信息及索引选用情况，大致估算出找到所需记录所需要读取的行数 (越少越好)

### filtered 

按表条件过滤的行的百分比

### Extra

附加信息 - 包含不适合在其他列出现，但是十分重要的信息

1. <b style='color: red'>Using filesort</b>: 说明MYSQL会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取。MYSQL中无法利用索引完成的排序操作被称为文件排序。(出现这个标识，<b>排序效率会较低</b>)
2. <b style='color: red'>Using temporary</b>: <b>最差的标识</b>。使用了临时表保存中间结果，MYSQL在对查询结果排序时使用临时表。常见于排序order by 和分组查询 group by。
3. <b style='color: red'>USING index</b>: 表示相应的select操作中使用了覆盖索引(Covering Index)，避免访问了表的数据行，<b>效率不错</b>。如果同时出现<b>using where</b>，表明索引被用来执行索引键值的查找；如果没有同时出现**using where**，表明索引用来读取数据而非执行查找动作。
4. **using join buffer**: 使用了连接缓存
5. **impossible where**: where字句的值总是false，不能用来获取任何元祖
6. **select tables optimized away**: 在没有GROUP BY子句的情况下，基于索引优化MIN/MAX操作或者对于MyISAM存储引擎优化COUNT(`*`)操作，不必等到执行阶段再进行计算，查询执行计划生成的阶段即完成优化。
7. **distinct**: 优化distinct操作，在找到第一匹配元祖后即停止找同样值的动作