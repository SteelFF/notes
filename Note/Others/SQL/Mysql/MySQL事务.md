### 事务

对于关系型数据库而言，事务满足4个条件（ACID）：原子性(Atomicity，或称不可分割性)、一致性(Consistency)、隔离性(Isolation，又称独立性)、持久性(Durability)。
- **原子性：** 一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
    
- **一致性：** 在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
    
- **隔离性：** 数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
    
- **持久性：** 事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

### 事务控制语句：

- <strong>BEGIN</strong> 或 <strong>START TRANSACTION</strong> 显式地开启一个事务；
    
- <strong>COMMIT</strong> 也可以使用 <strong>COMMIT WORK</strong>，不过二者是等价的。COMMIT 会提交事务，并使已对数据库进行的所有修改成为永久性的；
    
- <strong>ROLLBACK</strong> 也可以使用 <strong>ROLLBACK WORK</strong>，不过二者是等价的。回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；
    
- <strong>SAVEPOINT identifier</strong>，<strong>SAVEPOINT</strong> 允许在事务中创建一个保存点，一个事务中可以有多个 SAVEPOINT；
    
- <strong>RELEASE SAVEPOINT identifier</strong> 删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常；
    
- <strong>ROLLBACK TO identifier</strong> 把事务回滚到标记点；
    
- <strong>SET TRANSACTION</strong> 用来设置事务的隔离级别。InnoDB 存储引擎提供事务的隔离级别有<strong>READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ 和 SERIALIZABLE</strong>。
    

![](https://www.runoob.com/wp-content/uploads/2014/03/python_mysql_transction_management-e1530354220769.webp)

### 如何在MySQL当中启用事务

MySQL 事务主要用于处理操作量大，复杂度高的数据。比如说，在人员管理系统中，你删除一个人员，你既需要删除人员的基本资料，也要删除和该人员相关的信息，如信箱，文章等等，这样，这些数据库操作语句就构成一个事务！

在 MySQL 中，事务是一组SQL语句的执行，它们被视为一个单独的工作单元。

- 在 MySQL 中只有使用了 Innodb 数据库引擎的数据库或表才支持事务。
- 事务处理可以用来维护数据库的完整性，保证成批的 SQL 语句要么全部执行，要么全部不执行。
- 事务用来管理 **insert、update、delete** 语句


#### MYSQL 事务处理主要有两种方法：

1、用 BEGIN, ROLLBACK, COMMIT 来实现

- **BEGIN 或 START TRANSACTION**：开用于开始一个事务。
- **ROLLBACK** 事务回滚，取消之前的更改。
- **COMMIT**：事务确认，提交事务，使更改永久生效。

2、直接用 SET 来改变 MySQL 的自动提交模式:

- **SET AUTOCOMMIT=0** 禁止自动提交
- **SET AUTOCOMMIT=1** 开启自动提交

<strong> BEGIN 或 START TRANSACTION -- 用于开始一个事务：</strong>
``` sql
BEGIN; -- 或者使用 START TRANSACTION;
```

<strong>COMMIT -- 用于提交事务，将所有的修改永久保存到数据库：</strong>
``` sql
COMMIT;
```

<strong>ROLLBACK -- 用于回滚事务，撤销自上次提交以来所做的所有更改：</strong>
``` sql
ROLLBACK;
```

<strong>SAVEPOINT -- 用于在事务中设置保存点，以便稍后能够回滚到该点：</strong>
``` sql
SAVEPOINT savepoint_name;
```

<strong>ROLLBACK TO SAVEPOINT -- 用于回滚到之前设置的保存点：</strong>
``` sql
ROLLBACK TO SAVEPOINT savepoint_name;
```


下面是一个简单的 MySQL 事务的例子：
#### 实例 1
``` sql
-- 开始事务  
START TRANSACTION;  
  
-- 执行一些SQL语句  
UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;  
UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;  
  
-- 判断是否要提交还是回滚  
IF (条件) THEN  
    COMMIT; -- 提交事务  
ELSE  
    ROLLBACK; -- 回滚事务  
END IF;
```

#### 实例 2

一个简单的事务实例：

``` mysql console
mysql> use RUNOOB;
Database changed
mysql> CREATE TABLE runoob_transaction_test( id int(5)) engine=innodb;  # 创建数据表
Query OK, 0 rows affected (0.04 sec)
 
mysql> select * from runoob_transaction_test;
Empty set (0.01 sec)
 
mysql> begin;  # 开始事务
Query OK, 0 rows affected (0.00 sec)
 
mysql> insert into runoob_transaction_test value(5);
Query OK, 1 rows affected (0.01 sec)
 
mysql> insert into runoob_transaction_test value(6);
Query OK, 1 rows affected (0.00 sec)
 
mysql> commit; # 提交事务
Query OK, 0 rows affected (0.01 sec)
 
mysql>  select * from runoob_transaction_test;
+------+
| id   |
+------+
| 5    |
| 6    |
+------+
2 rows in set (0.01 sec)
 
mysql> begin;    # 开始事务
Query OK, 0 rows affected (0.00 sec)
 
mysql>  insert into runoob_transaction_test values(7);
Query OK, 1 rows affected (0.00 sec)
 
mysql> rollback;   # 回滚
Query OK, 0 rows affected (0.00 sec)
 
mysql>   select * from runoob_transaction_test;   # 因为回滚所以数据没有插入
+------+
| id   |
+------+
| 5    |
| 6    |
+------+
2 rows in set (0.01 sec)
 
mysql>
```