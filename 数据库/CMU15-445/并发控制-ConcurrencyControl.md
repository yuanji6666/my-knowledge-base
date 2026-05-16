![[database_system]]
如图，并发控制的主题出现在DB的多个层级中

# #16 ConcurrencyTheory

Lecture16开始，课程来到了并发控制主题，本节课从motivation example引入，提出问题：怎样控制并发事务，主要内容围绕正确标准：ACID四个方面讲解

- 原子性：
	From user's point of view: txn always either executes all its actions or executes no actions at all
	solution: logging ~~shadow paging~~
- 一致性：
	database consistency: 年龄不为负、主键唯一(数据库层面)
	transaction consistency: 转账收支相等、总分不变(应用层实现)
- 隔离性：
	The DBMS provides transactions the illusion that they are running alone in the system.(like process in OS)
	引入：execution schedule 并发事务操作执行的顺序规划
	解决方案：concurrency control protocol（pessimistic & optimistic）
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

