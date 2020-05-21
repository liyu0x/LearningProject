## 引擎

### 引擎对比
|功能 | MyISAM |MEMORY|InnoDB| 
| --- | --- |--- |--- |
|存储限制|256TB|RAM|64TB|
|支持事务|<font style="color:red">N</font>|<font style="color:red">N</font>|<font style="color:green">Y</font>|
|支持全文索引|<font style="color:green">Y</font>|<font style="color:red">N</font>|<font style="color:red">N</font>|
|支持B树索引|<font style="color:green">Y</font>|<font style="color:green">Y</font>|<font style="color:green">Y</font>|
|支持哈希索引|<font style="color:red">N</font>|<font style="color:green">Y</font>|<font style="color:red">N</font>|
|支持集群索引|<font style="color:red">N</font>|<font style="color:red">N</font>|<font style="color:green">Y</font>|
|支持数据索引|<font style="color:red">N</font>|<font style="color:green">Y</font>|<font style="color:green">Y</font>|
|支持数据压缩|<font style="color:green">Y</font>|<font style="color:red">N</font>|<font style="color:red">N</font>|
|空间使用率|<font style="color:red">低</font>|<font style="color:red">NAN</font>|<font style="color:green">高</font>|
|支持外键|<font style="color:red">N</font>|<font style="color:red">N</font>|<font style="color:green">Y</font>|

### 引擎

#### MyISAM

1. 此引擎在使用时会在磁盘上生成三个文件:
   1. frm文件:存储表的定义数据
   2. MYD文件:存放表具体记录的数据
   3. MYI文件:存储索引，仅保存记录所在页的指针。数据结果为B+ Tree。
2. frm和MYI可以存放在不同的目录下。
3. 通常查询是通过MYI查询关键词所在的记录页，再根据记录页查找记录。
4. 支持的Table类型:
   1. 静态固定长度表: 这种方式的优点在于存储速度非常快，容易发生缓存，而且表发生损坏后也容易修复。缺点是占空间。这也是默认的存储格式。
   2. 动态可变长度表: 优点是节省空间，但是一旦出错恢复起来比较麻烦。
   3. 压缩表: 上面说到支持数据压缩，说明肯定也支持这个格式。在数据文件发生错误时候，可以使用check table工具来检查，而且还可以使用repair table工具来恢复。
5. 使用场景: 由于不支持事务，所以存储数据很快，追求快速存储的业务，允许错误数据的出现。

#### InnoDB

1. Mysql默认存储引擎
2. 特点如下:
   1. auto_increment 自动增长列
   2. 支持事务，默认为可重复读。采用MVCC(并发版本控制)来实现的。
   3. 使用的锁粒度为行级锁，可以支持更好的并发。
   4. 支持外键约束；外键约束会降低表的查询速度，但是增加了表之间的耦合性。
   5. 支持在线热备份。
   6. 维护着一个缓冲池，通过缓冲池，将索引和数据都缓存起来，加快查询速度。
   7. 对于InnoDB类型的表，其数据的物理组织形式是聚簇表。所有的数据按照主键来组织。数据和索引放在一块，都位于B+数的叶子节点上；
   8. InnoDB存储表和索引也有如下两种方式:
      1. 使用共享表空间存储：所有的表和索引存放在同一个表空间中。
      2. 使用多表空间存储：表结构放在frm文件，数据和索引放在IBD文件中。分区表的话，每个分区对应单独的IBD文件，使用分区表的好处在于提升查询效率。
3. 使用场景:由于支持事务，可以使用在对数据完整度要求较高的场景，但也因此会降低工作效率。

#### Memory

1. 数据保存在内存中，每一张表与对应一个硬盘中的frm文件。
2. 支持的数据类型有限:
   1. 不支持Text和Blob
   2. 对于String类型，只支持固定长度
   3. varchar会自动存储为char类型
3. 由于数据是放在内存中，出现故障会导致数据丢失
4. 查询时如果使用临时表，临时表中包含Blob或Text，这个临时表就会转换为MyISAM表，性能大幅降低
5. 默认使用Hash索引。
6. 如果一个内部表很大，则会转换为磁盘表

## 并发

### 锁

#### 1.锁类型

1. 读锁(S锁、共享锁): 若事务T对数据对象A加上S锁，则事务T可以读A但不能修改A，其他事务只能再对A加S锁，而不能加X锁，直到T释放A上的S 锁。这保证了其他事务可以读A，但在T释放A上的S锁之前不能对A做任何修改。
2. 写锁(X锁、排他锁): 若事务T对数据对象A加上X锁，事务T可以读A也可以修改A，其他事务不能再对A加任何锁，直到T释放A上的锁。这保证了其他事务在T释1放A上的锁之前不能再读取和修改A。

#### 2.锁范围

1. 表锁: 操作对象是数据表。Mysql大多数锁策略都支持(常见mysql innodb)，是系统开销最低但并发性最低的一个锁策略。事务t对整个表加读锁，则其他事务可读不可写，若加写锁，则其他事务增删改都不行。
2. 行级锁: 操作对象是数据表中的一行。是MVCC技术用的比较多的，但在MYISAM用不了，行级锁用mysql的储存引擎实现而不是mysql服务器。但行级锁对系统开销较大，处理高并发较好。

### MVCC（Multiversion Concurrency Control）

#### 事务等级

|等级|脏读|不可重复度|幻读|
|-|-|-|-|
|Read Uncommitted|<font style="color:RED">Y</font>|<font style="color:RED">Y</font>|<font style="color:RED">Y</font>|
|Read Committed|<font style="color:GREEN">N</font>|<font style="color:RED">Y</font>|<font style="color:RED">Y</font>|
|Repeatable Read|<font style="color:GREEN">N</font>|<font style="color:GREEN">N</font>|<font style="color:RED">Y</font>|
|Serializable|<font style="color:GREEN">N</font>|<font style="color:GREEN">N</font>|<font style="color:GREEN">N</font>|

#### 并发事务带来的问题

1. Lost Update: 当两个或多个事务修改同一行时，最后的更新会覆盖其他事务的更新，造成更新丢失。解决问题的方法就是一个事务未提交时其他事务不允许修改此行数据。
2. Dirty Reads: 一个事务读取另一个事务未提交的数据，导致脏读。
3. Non-Repeatable Reads: 一个事务多次读取相同数据获取到不同的结果
4. Phantom Reads: 一个事务按相同的查询条件重新读取以前检索过的数据，却发现其他事务插入了满足其查询条件的新数据，这种现象就称为 “幻读”。

#### 解决方案

1. 加读写锁
2. 一致性快照读，及MVCC

#### 实现原理

##### row
InnoDB的row中包含了一些额外的信息:
|属性名|长度|作用|
|-|-|-|-|
|DATA_TRX_ID|6字节|标记了最新更新这条记录的transaction id，每处理一个事务，其值自动+1|
|DATA_ROLL_PTR|7字节|指向当前记录的rollback segment的undo log;即回滚指针,指向上一个版本|
|DB_ROW_ID|6字节|当由innodb自动产生聚集索引时，聚集索引包括这个DB_ROW_ID的值，否则聚集索引中不包括这个值。此属性用于索引当中;即隐含自增ID,如果表没有主键,InnoDB会以DB_ROW_ID产生一个聚簇索引|
|DELETE BIT|1 bit|用于标记此记录是否被删除，待事务提交的时候才真正的删除|

##### undo log

两种undo log
|名称|作用|
|-|-|
|insert undo log|代表食物在insert新纪录产生时记录的undo log,只在食物回滚时需要,并且在事务提交后立即被丢弃|
|update undo log|事务在进行update或delete时产生的undo log;不仅在事务回滚时需要，在快照读时也需要；所以不能随便删除，只有在快照读和事务回滚都不涉及该日志时，对应的日志才会被purge线程同意清除|

* purge相关:
   1. 为了实现InnoDB的MVCC机制，更新或删除操作都只是设置一下老记录的deleted_bit，并不真正的将过时的记录删除
   2. 为了节省磁盘空间，InnoDB有专门的purge线程来清理deleted_bit为true的记录。为了不影响MVCC正常工作，purge线程自己也维护了一个read view(这个read view相当于系统中最老最活跃事务的read view)；如果某个记录的deleted_bit为true，并且DB_TRX_ID相对于purge线程的read view可见，那么这条记录一定是可以被安全清除的。


*  对MVCC有实质帮助的时update undo log，undo log实际上存在rollback segment中的旧纪录链，执行流程如下:
   1. 一个事务插入了person表一条新纪录，记录如下:

      |name|age|DB_ROW_ID|DB_TRX_ID|DB_ROLL_PTR|
      |-|-|-|-|-|
      |Jerry|24|1|null|null|

   2. 事务1对该记录的name做出了修改，修改为Tom,步骤如下:
      a. 在事务1修改该行(记录)时，数据库先会对该行加排他锁
      b. 然后把该行的数据拷贝到undo log中，作为旧纪录，既在undo log中有当前行的拷贝副本
      c. 拷贝完毕后，修改该行name为Tom，并且修改隐藏字段的事务ID为当前事务1的ID，我们默认从1开始，之后递增，回滚指针指向拷贝到undo log的副本记录，即表示我的上一个版本就是它

      |name|age|DB_ROW_ID|DB_TRX_ID|DB_ROLL_PTR|
      |-|-|-|-|-|
      |Tom|24|1|1|0x12446545|

      undo log的0x12446545记录:
      |name|age|DB_ROW_ID|DB_TRX_ID|DB_ROLL_PTR|
      |-|-|-|-|-|
      |Jerry|24|1|null|null|

   3. 事务2对该记录进行修改,将age修改为30岁，步骤如下:
      a. 在事务2修改该行数据时，数据库也先为该行加锁
      b. 然后把该行数据拷贝到undo log中，作为旧纪录，发现该行记录已有undo log了，那么最新的旧数据作为链表的表头，插在该行记录的undo log的最前面
      c. 修改该行age为30岁，并且修改隐藏字段的事务ID为当前事务2的ID，那就是2，回滚指针指向刚刚拷贝到undo log的副本记录
      d. 事务提交，释放锁

      最新状态:
      |name|age|DB_ROW_ID|DB_TRX_ID|DB_ROLL_PTR|
      |-|-|-|-|-|
      |Tom|30|1|2|0x6546123|

      undo log的0x6546123记录:
      |name|age|DB_ROW_ID|DB_TRX_ID|DB_ROLL_PTR|
      |-|-|-|-|-|
      |Tom|24|1|1|0x12446545|

      undo log的0x12446545记录:
      |name|age|DB_ROW_ID|DB_TRX_ID|DB_ROLL_PTR|
      |-|-|-|-|-|
      |Jerry|24|1|null|null|

##### Read View

事务进行快照读时产生的Read View:该事务在执行快照读的那一刻会生产数据库系统当前的一个快照，记录并维护当前事务活跃的ID

* 可见性算法:Read View会将要修改的数据的DB_TRX_ID与当前活跃的事务的某些属性进行比较，如果不匹配，则不符合可见性，则从undo log中寻找，直到找到满足特定条件的DB_TRX_ID，那这个DB_TRX_ID所在的旧纪录就是当前事务能看见的最新的老版本

##### 整体流程


