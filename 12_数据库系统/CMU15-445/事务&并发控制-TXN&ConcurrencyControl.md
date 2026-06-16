![[database_system]]
如图，并发控制的主题出现在DB的多个层级中

# #16 ConcurrencyTheory

Lecture16开始，课程来到了并发控制主题

本节课从motivation example引入，指出之所以要允许事务并发执行是因为需要更高性能（利用率/吞吐/速度），但同时这也额外带来了维持并发执行正确性问题

正确性的标准：ACID
- 原子性：
	From user's point of view: txn always either executes all its actions or executes no actions at all
	solution: logging ~~shadow paging~~
- 一致性：
	database consistency: 年龄不为负、主键唯一(数据库层面)
	transaction consistency: 转账收支相等、总分不变(应用层实现，是编程人员的责任)
- 隔离性：
	The DBMS provides transactions the illusion that they are running alone in the system.(like process in OS)
	引入：execution schedule 并发事务操作执行的顺序规划
	解决方案：concurrency control protocol（pessimistic & optimistic）接下来的**重点**
- 持久性：
	All of the changes of committed transactions must be durable (i.e., persistent) after a crash or restart.

# #17 TwoPhaseLocking

Lecture17承接Lecture16,我们不可能提前知道所有schedule中的operations，所以引入transaction lock，保证动态调度准确性

这节课讲的整体上属于上一节提到的悲观锁

1. 锁类型：shared-lock & exlusive-lock 
2. 两段锁定：
	一种pessimistic concurrency control protocol
	一旦开始释放锁，不能再获得锁（但是依旧有脏读，且容易致使级联终止cascading abort）
	sloution：强严格两段锁，只能在事务commit时候释放锁，牺牲了并发性能
3. 死锁解决：
	检测：waits-for graph中出现环路
	预防：wait-die & wound-wait 策略
4. 锁粒度：
	分层锁，提高效率
	Importantly, if a transaction is using tuple-level locks, it needs to communicate that no other transaction can grab a page-level lock (or anything higher) since that would conflict. To facilitate this, intention locks are implicit locks that signal that there are explicit locks held at lower levels.
	引入锁粒度后，为了解决冲突引入意向锁，是一种隐式锁

![[意向锁兼容矩阵.png]]

# #18 Timestamp Ordering Concurrency Control

承接上一节，本节时间戳顺序并发协议是实现乐观并发协议（OCC）的原理，除此之外本节补充了幻读这个异常和隔离等级控制
1. 乐观并发协议 OCC
	In OCC, the DBMS creates a private workspace for each transaction.
	3phases: read-> validation-> commit
	...
2. 幻读
	In our previous discussions, we have considered transactions that operate on **a static set of objects** within the database. However, when transactions perform **insertions, updates, and deletions**, we encounter a new set of complications.
	解决：加索引锁 Index Locking（key-value/gap/key-range/hierarchical locks）
3. 隔离等级
	![[隔离等级.png]]
	实现：
	![[隔离等级实现原理.png]]
	现实中因为性能和默认等级，很少用最高隔离等级