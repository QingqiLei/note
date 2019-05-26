[TOC]

### 增加新用户权限

`CREATE USER 'hive'@'%' IDENTIFIED BY 'hive';`

`GRANT ALL PRIVILEGES ON *.* TO 'hive'@'%' ;`

### 安装

我在ubuntu上下载了一个bundle, 按照提示安装.

#### 验证安装

`cat /etc/passwd | grep mysql`

`cat /etc/group | grep mysql`

`mysqladmin --version`

进程: `ps -ef | grep mysql`

启动服务:  service mysql start

停止服务:  `service mysql stop`

设置开机启动: `chkconfig mysql on`   `chkconfig --list | grep mysql`

#### 连接数据库

如果第一次没有设置密码, 需要`/usr/bin/mysqladmin -u root password 123456`

`mysql -h host -u user -p`

#### 存储位置

数据:/var/lib/mysql

配置文件  /etc/mysql 或者/usr/share/mysql

#### 字符集

`show variables like 'character%'`

#### 修改配置文件

在 `/etc/mysql ` 文件夹中, my.cnf 文件

#### 日志文件

二进制日志: log-bin 主从复制

错误日志: log-error 默认是关闭的, 记录严重的警告的错误信息,每次启动和关闭的详细信息

查询日志: log 记录查询的sql 语句, 默认关闭, 如果开启会降低mysql 整体性能, 因为记录日志也是需要消耗系统资源

#### 数据文件

这就像是在图书馆中 frm 是书架子, myd 是书, myi 是书标签

frm : 存放表结构

myd: 数据

myi : 索引

### mysql 架构

mysql 是分层的, 插件式的,可拔插的, 插件式的存储引擎架构将查询处理和其他的系统任务以及数据的存储提取相分离.

一个mysql 应用场景一般是, 外部是connectors (jdbc, odbc, python), 连接到mysql server 中的connection pool, 

然后到第二层, sql interface, parser, optimizer, caches & buffers

第三层, pluggable storage engines(一般使用innoDB, mylsam)

第四层: 文件存储层(NTFS, UFS, EXT2 ), 

1. 连接层: 最上层是一些客户端和连接服务,包括本地sock 通信和大多数基于客户端/服务端工具实现的类似于tcp/ip 通信,主要完成一些类似于链接处理,授权认证,和相关的安全方案, 改成上引入了线程池的概念,为通过认证安全接入的客户端提供线程.同样在该层上可以实现基于SSL 的安全链接, 服务器也会为安全接入的每个客户端验证它所具有的操作权限.
2. 服务层: 完成核心服务功能, 如SQL 接口, 完成缓存的查询, SQL 的分析和优化及内置函数的执行, 所有跨存储引擎的功能也在这一层实现,如过程, 函数, 也会进行相应的优化,如确定查询表的顺序,是否利用索引,最后生成相应的执行操作,如果是select 语句, 还会查询内部的缓存.
3. 引擎层: 负责MySql 数据的存储和提取,
4. 存储层: 将数据存储在运行于裸设备的文件系统上,并完成与存储引擎的交互.



#### 存储引擎

show engines;

show variables like '%storage_engine%'

MylSAM : 主要是查询快

总结: MylSam 关注的是性能, innodb 关注的是事务

### SQL 性能下降原因

查询语句写的烂, 各种子查询导致没用索引,

索引失效(单值索引,复合索引): `create index idx_user_name on user(name)`, `create index idx_user_nameEmail on user(name, email)`

关联查询太多join (设计缺陷　或　不得己的需求)

服务器调优

### join

#### inner join

`select * from tableA A inner join tableB B on A.key = B.key`

#### left join

`select * from TableA A left join tableB B on A.key = B.key`

#### left join (only left) 

`select * from tableA A left join tableB B on A.key = B.key where B.key IS null`

找到A 独占的, 需要B 的为null

#### full outer join ()

`select * from talbeA A full outer join tableB B on A.key = B.key` (mysql 不支持)

合并加去重 

`select * from tableA A left join tableB B on A.key = B.key`

`union`

`select * from tableA A right join tableB B on A.key = B.key`

#### 去掉共有(各自私有的)

`select * from tableA A full outer join tableB B on A.key = B.key where A.key is null or B.key is null`

`select * from tableA A left join tableB B on A.key = B.key where B.key is null `

`union `

`select * from tableA A right join tableB B on A.key = B.key where A.key is null`



#### right join (only right)

`select * from tableA A right join tableB B on A.key = B.key where A.key is null`

B 独有, A 是null

#### right join

`select * from tableA A right join tableB B on A.key = B.key`



### 索引

#### 是什么

索引是帮助mysql 高效获取数据的数据结构.

索引有排序加查找两大功能,影响查找和排序.

为了加快查找所需数据的地址, 可以维护一个二叉查找树, 每个节点分别包含索引键值和一个指向对应数据记录物理地址的指针,这样就可以运用二叉查找在一定的复杂度内获取到相应数据.

一般索引本身也很大,不可能全部存储在内存中,索引以索引文件的形式存储在磁盘上.

一般索引都是B树(多路搜索树),其中聚集索引,次要索引,覆盖索引,复合索引,前缀索引,唯一索引默认都是使用B+树索引.除了B+ 树这种索引之外,还有哈希索引

#### 优势

提高数据检索的效率,减低数据库的IO 成本

降低数据排序的成本, 减低了CPU的消耗

#### 劣势

1. 索引也是一张表, 保存了主键与索引字段, 并指向实体表的记录,所以索引列也是占空间的

2. 虽然索引大大提高了查询速度,同时会降低更新表的速度,对表进行INSERT, update, DELETE, 因为更新表时,MySQL 不仅要保存数据,还要保存一下索引文件每次更新添加了索引列的字段, 都会调整因为更新所带来的键值变化后的索引信息
3. 索引只是提高效率的一个因素,如果MySQL 中有大数据量的表,就需要花时间研究建立最优秀的索引,或优化

#### 索引分类

1. 唯一索引: 索引列的值必须唯一, 但允许有空值.(这个可以用在银行卡账号上, )
2. 主键, 也就是聚簇索引(innodb 有, myisam 没有)
3. 单值索引: 一个索引只包含单个列,一个表可以有多个单列索引.(这个可以用在名字上, 可以重名)
4. 复合索引: 一个索引包含多个列

#### 语法

insert

insert into tablename(column) values(values);

创建

加上unique 是唯一索引, `create [unique] index indexname on mytable(columnname(length))`

`alter mytable add [unique] index indexname on (columnname)`

删除

`drop index indexname on mytable`

查看

show index from mytable;

#### mysql 索引结构

B+ 树

1. 结构

一个磁盘块包含几个数据项和指针,

如磁盘块1 包含数据项17 和35, 包含指针P1, P2, P3

p1  表示小于17 的磁盘块, p2 表示在17 和35之间的磁盘块, p3 表示大于35的磁盘块

真实数据存在于叶子节点: 

非叶子节点不存储真实数据,只存储指引搜索方向的数据项.

2. 查找:

如果要查找数据项29, 那么首先会把磁盘块1 由磁盘加载到内存中, 此时发生一次IO, 在内存中用二分查找确定29 在17 和35之间,锁定磁盘块1 的P2 指针,内存时间因为非常短(相对与磁盘IO), 可以忽略不计, 通过磁盘块1的P2 指针的磁盘地址把磁盘块3由磁盘加载到内存, 发生第二次IO, 29 在26 和30 之间, 锁定磁盘块3 的P2 指针, 通过指针加载磁盘块8到内存中, 发生第三次IO, 同时内存中做二分查找找到29, 结束查询, 一共三次IO.

真实情况是: 3层B+ 树可以表示上百万的数据, 如果上百万的数据查找只需要三次IO, 性能提高将是巨大的, 如果没有索引,每个数据项都要发生一次IO, 那么总共需要百万次的IO, 显然成本非常非常高.

#### 需要创建索引

1. 主键自动建立唯一索引
2. 频繁作为查询条件的字段应该创建索引
3. 查询中与其他表关联的字段,外键关系建立索引
4. 频繁更新的字段不适合创建索引, 因为每次更新不单单是更新了记录还会更新索引
5. where 条件里用不到的字段不创建索引
6. 单键 / 组合索引的选择问题. who? (在 高并发下倾向创建组合索引)
7. 查询中排序的字段, 排序字段若通过索引去访问将大大提高排序速度( order by)
8. 查询中统计或者分组字段(group by, 分组的前提是排序)

#### 不要创建索引

1. 表记录太少
2. 经常增删改的表
3. 数据重复且分布平均的表字段, (国籍都是中国, 国籍这个字段就不要建立索引, 性别也是)
4. 索引的选择性就是索引列中不同的值的数目与表中记录数的比, 如果一个表中有2000 条记录, 表索引列有1980 个不同的值, 那么这个索引的选择性就是1980 / 2000 = 0.99, 一个索引的选择性越接近1, 这个索引的效率就越高.

### 性能分析

#### MySQL Query Optimizer 

1. MySQL 有专门负责优化select 语句的优化器模块, 主要功能: 通过计算分析系统中收集到的统计信息,为客户端请求的Query 提供他认为最优的执行计划.

#### 常见瓶颈

1. CPU , 数据装入内存, 或者从磁盘上读取数据的时候
2. IO, 装入数据远大于内存容量的时候
3. 服务器配置

#### explain

explain + sql 语句

1. 表的读取顺序

2. 数据读取操作的操作类型

3. 哪些索引可以使用

4. 哪些索引被实际使用

5. 表之间的引用

6. 每张表有多少行被优化器查询

#### 各个字段

#### **id:**  

1.  id 相同的时候, 执行顺序由上至下.
2. 如果是子查询, id 的序号会递增,id 值越大优先级越高, 越先被执行

#### **select_type:**

1. simple,  简单的select 查询, 查询中不包含子查询或者union
2. primary, 查询中若包含任何复杂的字部分, 最外层查询则被标记为primary
3. subquery, 在select 或where 列表中包含子查询
4. derived, 在from 列表中包含子查询被标记为derived, MySQL 会递归地执行这些子查询,把结果放在临时表
5.  union, 若第二个select 出现在union 之后, 则被标记为union, 若union 包含在from子查询中,外层select 将被标记为derived
6. union result , 从union 表获取结果的select 



#### **table:** 

1. 一般就是table 名字
2. `<derived2>`  衍生的表

#### **type:** 

1. 访问类型
2. 从最好到最差: system > const> eq_ref > ref > range> index > all
3. 一般来说至少达到range 级别, 最好到ref

* system: 表只有一行记录, 等于系统表, 这是const 类型的特例, 平时不会出现

* const: 通过索引一次就能查到, const 用于比较primary key 或者unique 索引, 因为只匹配一行数据, 所以很快, 如将主键置于where 列表中, MySQL 就能将该查询转换为一个常量

* eq_ref: 唯一性索引扫描, 对于每个索引键,表中只有一条记录与之匹配, 常见于主键或唯一索引扫描

`explain select * from t1, t2 where t1.id = t2.id`

* ref: 非唯一索引扫描, 返回匹配某个单独值的所有行,本质上也是一种索引访问, 它返回所有匹配某个单独值的行,然而,它可能会找到多个符合条件的行, 所以他应该属于查找和扫描的混合体.

`create index idx_col1_col2 on t1(con1, col2)`

`explain select * from t1 where col1 = 'ac'`

* range: 只检索给定范围的行, 使用一个索引来选择行, key 列显示使用哪个索引, 一般就是在你的where语句中出现了between < > in 等查询, 这种范围扫描索引扫描比全表扫描要好, 因为它只需要开始于索引的某一点, 而结束于另一点, 不用扫描全部索引.

` explain select * from where id between 30 and 60`

* index: full index scan, index 与all 区别就是index 类型只便利索引树, 通常比all 块, 因为索引文件通常比数据文件小, 但index 是从索引中读取的, 而all 是从磁盘中读的.

`explain select id from user`

* all 全表扫描

#### **possible_keys**

显示可能应用在这张表中的索引, 一个或多个,但不一定被查询实际使用

#### **key**

null: 没有使用索引(索引失效)

查询中若使用了覆盖索引, 则该索引仅出现在key 列表中

#### **key_length** 

索引使用的最大可能长度, 并非实际使用长度

#### **ref:** 

`explain select * from t1, t2 where t1.col1 = t2.col1 and t1.col2 = 'ac'`

t1 的ref 就是 shared.t2.col1, const

#### **rows:**

根据表统计信息及索引选用情况, 大致估算出所需的记录所需要读取的行数

#### **extra:**

不适合在其他列显示但是重要的信息

1. Using filesort 会对数据使用一个外部的索引排序, 而不是按照表内索引顺序读取, MySQL 无法利用索引完成的排序称为 文件排序 (危险)
2. Using temporary, 用临时表保存中间结果, 常见于order by, group by, 当group by 后面的字段不是全部为索引字段的时候, 就会有临时表 `explain select name from user where id in (1,2) group by name`
3. using index,  相应的select 操作使用了覆盖索引, 避免访问了表的数据行,效率不错!
4. using where 表示发生了过滤, 1 如果where 语句没有用到索引的第一个列(既可以使用索引, 也可以索引失效), 就会出现using where,, 2 或者用到了索引第一列, 但是用到了非索引列(一定不会索引失效). 也会using where, (可用没用到索引没有关系) 3 或者使用了第一列, 但是用的是范围
5. using index condition: select 中有非索引列, 并且where 语句使用了索引列第一列和非第二个索引(除了其他索引列, 没有用到剩下的索引列), 或者第二索引列使用了范围

如果同时出现using where, 表示索引被用来执行索引键值的查找(使用索引回表),

同时出现using index, using where, 情况是where 没有使用索引第一个列, select 包含索引列

如果没有同时出现using where, 表示索引用来读取数据 .

覆盖索引: 一般数据库也能使用索引找到一个列的数据,不必读取整个行, 毕竟索引叶子节点存储了它们索引的数据, 当能通过读取索引就能得到想要的数据, 就不需要读取了,一个索引包括了满足查询结果的数据就叫做覆盖索引.

### 索引

`create table if not exists article(id int(10) unsigned not null primary key auto_increment, author_id int(10) unsigned not null ,category_id int(10) unsigned not null, views int(10) unsigned not null, comments int(10) unsigned not null,  title varbinary(255) not null, content text not null);`

insert into article (author_id , category_id, views, comments, title, content) values (1,1,1,1,'1', '1');

sql :

explain select id, author_id from article where category_id = 1 and comments > 1 order by views desc limit 1;

extra: using index condition, using filesort, type range

现在想要提高效率, rows 减少. type 是all(错了), extra 还出现了using filesort, 也是最坏情况

#### 单表的范围加orderby 索引失效并使用filesort

新建索引+ 删除索引

`create index idx_article_ccv on article(category_id, comments, views)`

常亮比范围更精确, comments 的范围会使**索引失效**.

extra 里使用Using filesort 是无法接受的, 先排序category_id, 遇到相同的category_id, 再排序comments, 遇到相同的comments, 再排序views, 当comments 字段在联合索引里处于中间位置时, comments > 1 条件是一个范围值, Mysql 无法利用索引再对后面的views 部分进行检索, 即range 类型查询字段后面的索引失效.

删除: `drop index idx_article_ccv on article`(也可以不drop, 会自动选择)

create index idx_article_cv on article (category_id, views); 这样就可以了

选择views , 没有选择comments 的原因是, filesort 就是因为没有使用索引而发生的排序, 所以views一定要在索引中, 

如果使用create index idx_article_views_commnents on article (category_id, views,comments);

并且order by 也会和范围一样, 也会让后面的索引列失效, 所以**效果和上面的相同.**

#### 双表

`explain select * from class left join book on class.card = book.card`

s左连接,索引加到右边,left join 条件用于确定如何从右表搜索行,左边一定都有

左连接: 在右边加索引, 右连接:在左表加连接

#### 三表

`explain select * from class left join book on class.card =book.card left join phone on book.card = phone.card`

class 左连接 book 左连接 phone

不动的是小的.也就是在大的表上面加索引

### 索引优化

#### 索引失效

1. 全值匹配我最爱
2. 最佳左前缀法则: 如果索引了多列, 要遵循最左前缀法则, 指的是查询从索引的最左前列开始并且不跳过索引中的列 ``, **这里不是说查询条件的顺序, 而是说查询条件中是否包含索引最左列字段, 不包含也有可能使用索引**,   如果用到了第一列, 那么不管怎么都会用到索引, 如果没有用到第一列, 那么如果在select 或者where 出现任何一个非索引列, 都不会用到索引
3. 不在索引列上做任何操作(计算, 函数, 类型转换) `explain select * from staffs where left(name, 4) = 'July'`
4. 范围之后全失效(order by 也是这样) explain select * from staffs where name = 'July' and age > 25 and pos = 'manager'`  
5. order by 后面顺序也很重要, 如果where 用到了第一个索引列, order by 第三个索引列, 第二个索引列, 那么会**使用filesort **(当然还会使用索引, 不是索引失效),单独order by 用不了索引，想要使用索引, 要考虑加where 或加limit
6. 尽量使用覆盖索引, 只访问索引的查询, 减少select * (如果是覆盖了, 在extra 中会using index)
7. 不使用不等于 != <>, 这是负向条件, union in or 都可以使用索引
8. 用or 分隔开的条件, 如果有没有使用到索引的条件, 那么都不会使用索引
9. is null, is not null 无法使用索引 `select * from staffs where name is null`
10. like 以通配符开头 会索引失效 (可以使用覆盖索引解决) `create index idx_user_nameAge on user(name,age)`  `select id, name, age from user where name like '%aa%'` 这个可以. `select name, age, email from user where name like '%aa%'` 这个不可以.. 

解决方法: 1. 左边不写 % ,  2. 使用覆盖索引

9. 字符串不加单引号索引   失效

varchar 类型 一定要使用单引号. (varchar 的数值是数字, where 语句如果没有加单引号, 就会不使用索引, 这就是类型转换)

10. 少用or  `select * from staffs where name = 'July ' or name = 'z3'`



index(a,b,c)

where a = 3 and b like 'kk%' and c = 4             使用到a b c

where a = 3 and b like '%kk' and c = 4                a

where a = 3 and b like '%kk%' and c = 4                 a

where a = 3 and b like 'k%kk%' and c = 4              a  b c

### 索引练习

#### 例子1 

`select count(*) from (select name from user group by name) nameTable;`

group by 分组 前一定排序, 会有临时表产生

一张test03表, 四个字段, 索引从c1 到c4

1. 

`explain select * from test03 where c1 = 'a1' and c2 = 'a2' and c3 = 'a3' and c4 = 'a4'`

和 `explain select * from test03 where c1 = 'a1' and c2 = 'a2' and c4 = 'a4' and c3 = 'a3'` 

上面两个是相同的, 都是用到了4 个.

2. 

`explain select * from test03 where cl = 'al' and c2 = 'a2' and c3 > 'a3'and c4 = 'a4'`

c3 用不到查找, 只能用排序, 范围之后全失效, 用到了3 个

3. 

`explain select * from test03 where c1 = 'a1' and c2 = 'a2' and c4 > 'a4' and c3 = 'a3'`

用到了4个

4. 

`explain select * from test03 where c1 ='a1' and c2 = 'a2 'and c4 = 'a4' order by c3`

用到了2 个, c1 和 c2,  也到了c3的排序, 但没有统计到里面

5. 

`explain select * from test03 where c1 = 'a1' and c2 = 'a2'order by c3`

和上面的是一样的.

6. 

`explain select * from test03 where c1 = 'a1' and c2 = 'a1' order by c4`

两个,这个会有Using filesort 

7.

`explain select * from test03 where c1 = 'a1' and c5 = 'a5' order by` c2, c3

用到了1个, 但是也用到了c2, c3 的排序, 无filesort

8.    

`explain select * from test03 where c1 = 'a1' and c5` = 'a5 ' **order c3,c2**

一个, 使用了filesort, order by

9. 

`explain select *from test03 where c1 = 'a1' and c2 = 'a2' order by c2 , c3`

两个, 查找和排序都按照顺序来

10. 

`explain select * from test03 where c1 = 'a1'and` c2 = 'a2'` and c5 = 'a5' order by c2,c3`

和上面一样, c1 c2 两个字段索引, c2 c3 用于排序

11. 

`explain select * from test03 where c1 = 'a1' and c2 = 'a2' and c5 = 'a5' order by c3, c2`

没有filesort, 和上面的不同是因为有一个c2, 因为c2 已经是一个常量了. 只有一份,不用排序.

12 `explain select * from test03 where c1 = 'a1' and c4 = 'a4' group by c2 , c3`

一个, 没有索引失效

13. `explain select * from test03 where c1 = 'a1' and c4 = 'a4'group by c2, c3` 使用了using temporary, using filesort,   
14. 

对于单值索引,  尽量针对当前query 过滤性更好的索引

在选择组合索引的时候, 当前query 中过滤性更好的字段在索引字段顺序中,位置越靠前越好

在选择组合索引的时候, 尽量选择可以能够包含当前query 中的where 字句中更多字段的索引

尽可能通过分析统计信息和调整query 的写法来发到选择合适索引的目的

#### 例子2

using where 表示发生了过滤

```sql

// 索引使用了xxx yyy zzz
```

1. explain select xxx from table2, select yyy from table2. select zzz from table2

extra 是using index, 索引覆盖, 查询的内容可以在索引中拿到

2 explain select aaa from table2 where xxx = 'something'

查询内容不在索引内, extra 是null,  type 是ref , key 是inx, 虽然使用了索引, 但没有索引覆盖, 回表

3 explain select yyy from table2 where xxx = 'something' 

extra:  using index, type 是ref , key 是inx, 使用了索引, 并且索引覆盖, 

4 explain  select yyy from table2 where zzz = 'something'

extra: using where; using index, type index, key inx, 查询内容在索引内，where条件为索引第三列，这次不是最左列了，extra依旧是using where using index，但是type变成了index，这表明发生了索引扫描，因为where条件不是最左列的缘故，其性能肯定是差于ref的。

5 explain select xxx from table2 where xxx='something' and yyy= 'some' and zzz='2018-01-19 00:00:00';

using index, ref , inx,  这样的查询很快

6 explain select aaa from table2 where xxx='something' and yyy= 'some' and zzz='2018-01-19 00:00:00';

这个和上面一个唯一不同的就是extra: null, 发生了回表, 当然也使用了索引

7 explain select xxx from table2 where zzz='2018-01-19 00:00:00'; explain select yyy from table2 where zzz='2018-01-19 00:00:00';

extra usingwhere, using index, type index, key inx, 使用了索引

8 explain select yyy from table2 where xxx='2018-01-19 00:00:00' 

using index

9 explain select aaa from table2 where xxx='2018-01-19 00:00:00'

extra: null type ref,   key inx, 使用了索引, 但是回表了

10 explain select aaa from table2 where yyy='2018-01-19 00:00:00';

using where, type all, key null

11 explain select xxx,aaa from table2 where  xxx = 't' and aaa = 'g';

using where, 使用了索引

12 explain select xxx,aaa from table2 where  xxx = 't' and zzz = 'g';

Using index condition, 使用了索引

where 语句如果包含索引的最左列, 那么不管where 中其他的条件, 和select 中的条件, 都会使用索引

13 explain select xxx from table2 where yyy ='g' order by aaa;

all null Using where; Using filesort, 没有用到第一列, 在order by 中使用了非索引列

14 explain select xxx from table2 where yyy ='g' order by zzz;

index inx Using where; Using index; Using filesort

15 explain select xxx from table2 where yyy ='g' order by xxx;

index inx Using where; Using index

16 explain select aaa from table2 where yyy ='g' order by xxx;

Using where; Using filesort 没有使用索引

#### 例子三

```
create table user(id int(11) auto_increment primary key, name varchar(20), gender int(1), age int(3), status int(1), remark varchar(255) );
create index idx on user(name);
```

1 explain select * from user where name like '%s%'

all        using where  

2 explain select * from user where name like 's%'

range  using index condition   

3 explain select * from user where name =1;

all    using where

```
// 复合索引
create index inx_name_status on user(name, status);
```

4  explain select * from user where name = 'df' and status =1;

ref  null, 使用了索引, key 为, inx_name_status 

5 explain select * from user where status = 2;

all using where, 没有用到索引, key: null



### 查询截取分析

#### 为排序使用索引(order by)

MySQL两种排序方式, 文件排序, 有序索引排序

MySQL 能为排序和查询使用相同的索引

1. order by 能使用索引的最左前缀

a   a,b  a,b,c   a desc,b desc,c desc

2. 如果where 使用索引的最左前缀为常量, 使用order by 能使用索引

where a = const orderby b, c      where a = const and b  = const order by c

where   a = const order by b, c    where a = const and b > const order by b ,  c

3. 不能用的情况

order a , b desc, c desc                 排序不一致

where g = const order by b, c      丢失 a 索引

where a = const order by c               丢失 b 索引

where a = const order by a, d            d 不是索引的一部分

where a in (), order by b, c     对于排序来说, 多个相等条件也是范围查询

#### order by 查询优化

1. 小表驱动大表,  `for(int i = 5) for(int j = 1000) ` 这样好.  for(int i = 1000) for(int j = 5) 这样不好..

   `select * from A where id in (select id from B)` B: 部门表, A 员工表, 这样好, 当B表的数据集小于A表的数据集时, 用in 好于exists. 

   `select * from A where exists (select 1 from B where B.id = A.id) `A 的数据集小于B的数据集,    将主查询的数据,放到子查询中做条件验证, 根据验证结果(true , false) 来决定主查询的结果是否得以保留

2. order by 优化(去掉filesort)

尽量使用index 排序, 不要用filesort

现在有一张表, age 和birth 两个字段, 索引.

* `explain select * from user where age > 20 order by age`

用到了索引, using where, using index 

*　`explain select * from user where age> 20 order by age, birth`

用到了1个索引, using where, using index

* `explain select * from user where age > 20 order by birth`

 using filesort, 没有了带头大哥age, 

* `explain select * from user where age > 20 order by birth, age`

using filesort, 也不行

* `explain select * from user order by birth`

用到了filesort

* `explain select * from user where birth > '2016-01-28 00:00:00' order by birth`

用到了filesort

* `explain select * form user where birth > '2016-01-28 00:00:00' order by age`

using where, using index

* `explain select * from user order by age asc, birth desc`

用到了filesort, order by 默认升序, 

order by 满足两情况, 会使用Index方式方式排序: 1. order by 语句使用索引最左前列   2. 使用where 字句与order by 子句条件列组合满足索引最左前列.

#### 提高order by 速度

1. 不要使用select * , 
2. 提高sort_buffer_size
3. 提高max_length_for_sort_data

#### group by 优化

先排序后分则, 最佳左前缀, 当

### 慢查询日志分析

#### 什么是慢查询日志

* MySQL 的慢查询日志是MySQL提供的一种日志记录, 它用来记录在MySQL中相应时间超过阈值的语句, 具体指运行时间超过long_query_time 值的sql, 则会被记录到慢查询日志中
* 具体指运行时间超过long_query_time值的sql, 则会被记录到慢查询日志中, long_query_time 的默认值为10, 意思是运行10 秒以上的语句
* 由他来查看哪些SQL 超出了我们的最大忍耐时间值, 比如一条sql 执行超过5 秒钟, 我们就算慢sql, 希望能收集5秒的sql, 结合之前的explain 进行全面分析.
* 默认,mysql 不开启慢查询日志

#### 开启

1. 只对当前有效, MySQL 重启失效

`show variables like '%slow_query_log%'`

`set global slow_query_log = 1`

2. 一直有效
3. 修改my.cnf 文件 slow_query_low=1   slow_query_log_file=/var/lib/mysql/atguigu-slow.log

#### 设置时间

默认大于10 秒, 会被记录

`show variables like '%long_query_time%';`

set global long_query_time=3, 需要新开一个回话,会看到 long_query_time 数值变化了

想要永久有效, 在my.cnf 中配置

```
slow_query_log=1
slow_query_log_file=/var/lib/mysql/qingqi.log
long_query_time=3
log_output=FILE
```

#### mysqldumpslow

s: 以何种排序,  c: 访问次数  i: 锁定时间  r: 返回记录  t: 查询事件  al: 平均锁定事件  ar:平均返回记录数

at: 平均查询时间  t: 返回前面多少条数据   g 正则表达式 

1. 得到返回记录集最多的10 个sql, mysqldumpslow -s r -t 10 /var/lib/mysql/chauncey-System-Product-Name-slow.log
2. 得到访问次数最多的10 个sql, mysqldumpslow -s c -t 10 /var/lib/mysql/chauncey-System-Product-Name-slow.log
3. 得到按照时间排序的前10条里面含有左连接的查询语句   mysqldumpslow -s t -t 10 -g 'left join' /var/lib/mysql/chauncey-System-Product-Name-slow.log
4. 建议和 |more 使用, 否则有可能出现爆屏情况  mysqldumpslow -s r -t 10  /var/lib/mysql/chauncey-System-Product-Name-slow.log

### 批量

插入1000 万条数据,

1. #### 建表

```
create table dept(
id int unsigned primary key auto_increment,
deptno mediumint unsigned not null default 0,
dname varchar(20) not null default '',
loc varchar(13) not null default ''
)engine = innodb ;

create table emp(
id int unsigned primary key auto_increment,
empno mediumint unsigned not null default 0,
ename varchar(20) not null default "",
job varchar(9) not null default "",
mgr mediumint unsigned not null default 0,
hiredate date not null,
sal decimal(7,2) not null,
comm decimal(7,2) not null,
deptno mediumint unsigned not null default 0
)engine = innodb
```



2. #### 设置参数 `log_bin_trust_function_creators`

`show variables like '%log_bin_trust_function_creators%'`

`set global log_bin_trust_function_creators = 1`

3. #### 创建函数, 保证每条数据都不同, (随机产生字符串, 随机产生部门编号)

```sql
delimiter $$
create function rand_string(n int) returns varchar(255)
begin
declare chars_str varchar(100) default 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
declare return_str varchar(255) default '';
declare i int default 0;
while i < n do
set return_str = concat(return_str, substring(chars_str, floor(1 + rand()*52),1));
set i= i+1;
end while;
return return_str;
end $$
```

查看帮助: help substring; help concat;

rand_string(n int) 就是返回一个长度为n 的字符串 

```
delimiter $$
create function rand_num() returns int(5)
begin
declare i int default 0;
set i = floor(100 + rand()*10);
return i;
end $$;

```

100 到110 部门号

4. #### 创建存储过程

```
delimiter $$
create procedure insert_emp(in start int(10), in max_num int(10))
begin
declare i int default 0;
set autocommit = 0;
repeat
set i = i +1;
insert into emp(empno, ename, job, mgr, hiredate, sal, comm, deptno) values((start +i), rand_string(6), 'salesman', 0001, curdate(), 2000, 400, rand_num());
until i = max_num
end repeat;
commit;
end $$
```



```
delimiter $$
create procedure insert_dept(in start int(10), in max_num int(10))
begin 
declare i int default 0;
set autocommit = 0;
repeat
set i = i + 1;
insert into dept(deptno, dname, loc) values((start +i), rand_string(10), rand_string(8));
until i = max_num
end repeat;
commit;
end $$
```



5. #### 调用存储过程

dept: 

`delimiter ;`

`call insert_dept(100, 10);`



call insert_emp(2000000, 5000000);

### show profile

是mysql 提供可以用来分析当前回话中语句执行的资源消耗情况, 可以用于sql 的调优测量. 默认关闭,并保存最近15次运行结果.

1. 查看mysql 是否支持
2. 开启功能 `show variables like '%profiling%';`  `set  profiling = on;`
3. 运行sql
4. 查看结果 `show profiles ` 找到sql 的id,    `show profile cpu, block io for query 5;`
5. 诊断sql   (converting heap to myisam 查询结果太大, 内存都不够用了,往磁盘上搬运,, creating tmp table 创建临时表, 一般group by 会创建临时表,, copying to tmp table on disk 把内存中的临时表复制到磁盘, 危险)
6. 日常开发注意:

### 全局查询日志

只允许在测试环境用

`set global general_log=1;`

`set global log_output='TABLE'`

`select * from mysql.general_log;`

### 锁

计算机协调多个进程或线程并发访问某一资源的机制

在数据库中, 除传统的计算资源( 如 CPU, RAM, I/O等)的争用以外, 数据也是一种供许多用户共享的资源, 如何保证数据并发访问的一致性, 有效性是所有数据库必须解决的一个问题, 锁冲突也是影响数据库并发访问性能的一个重要因素, 从这个角度来说, 锁对数据库而言显得尤其重要, 也更加复杂.

#### 分类

1. 从对数据操作的类型: 读锁, 写锁
2. 表锁, 行锁, 页锁

#### 特点

表锁: 偏向MyISAM 存储引擎, 开销小, 加锁块, 无死锁, 锁定粒度大,发生锁冲突的概率最高,并发度最低

加锁 `lock table tableName read`, lock table tablename write

查看  `show open tables`

解锁  `unlock tables;`

#### 读锁

共享锁, session 1加锁, 可以查询当前表,不能修改当前表,  不能查询其他表, 也不能修改

session 2 也可以查询当前的和其他表, 如果修改当前表,会被阻塞, 可以修改其他表

也就是都不能写 

#### 写锁

可以读和写被加写锁的, 不能读和写其他表

其他表可以读写, 被加写锁的不可以读和写

**简而言之, 读锁会阻塞写, 但是不会阻塞读, 写锁会把读和写都阻塞**

#### 分析表锁定

除了使用show open tables, 还可以使用 show status like 'table%'

Table_locks_immediate: 产生表级锁定的次数, 表示可以立即获取锁的查询次数, 每立即获取锁值加1

Table_locks_waited: 出现表级锁定争用而发生等待的次数, (不能立即获取锁的次数, 每等待一次锁值加1), 此值高说明存在严重的表级锁争用情况

Myisam 的读写锁调度是写优先, 这也是myisam 不适合做写为主表的引擎, 因为加写锁后, 其他线程不能做任何当前表的操作, 大量更新会使查询很难得到锁, 从而造成永久阻塞.

#### 行锁

偏向于InnoDB 存储引擎, 开销大,加锁慢, 会出现死锁, 锁定粒度最小, 发生锁冲突的概率最低, 并发度也最高,

InnoDB 与myisam 最大的不同: 支持**事务**, 采用**行锁**,

`set autocommit = 0` 别人没有提交的修改无法得知

如果两个session 都没有开启autocommit, 那么 两边都要commit 才能看到对方commit的修改

session 1 update,但是没有提交.  session 2 update相同一行 会阻塞, 当session 1 提交后阻塞消失

如果不是相同一行, 就不会阻塞.

#### 事务ACID

1. 原子性: 事务是一个原子操作单元, 其对数据的修改, 要么全部执行, 要么全都不执行
2. 一致性: 事务开始和完成时, 所有相关数据都必须应用于事务的修改, 事务结束时, 所有的内部数据结构也都必须是正确的.
3. 隔离性: 保证事务在不受外部并发操作影响的 '独立' 环境执行, 这意味事务处理过程中中间状态对外部是不可见的.
4. 持久性: 事务完成后, 它对于数据的修改是永久性的, 即使出现系统故障也能保持

#### 没有事务的问题

通过事务的隔离级别来解决

1. 更新丢失: 当两个或多个事务选择同一行, 然后基于最初选定值更新该行时 由于每个事务不是道其他事务, 就会发生丢失更新问题-- 最后的更新覆盖了由其他事务所做的更新, 如果一个事务在提交前, 另一个事务不能访问读同一行,就可以避免
2. 脏读: 事务A 读取到事务B 已修改 但没有提交的数据, 并在这个数据的基础上做了修改, 如果B回滚, A 读取的数据就无效, 不符合一致性要求
3. 不可重复读: 一个事务在读取某个数据后某个时间, 再次读取以前读取的数据, 发现其读取的数据已经发生改变(事务A 读取到了事务B 提交的修改数据)
4. 幻读:一个事务按相同的查询条件重新读取以前检索过的数据, 却发现其他事务插入了满足其他查询条件的新数据 (事务A 读取到了事务B 提交的新增数据)

#### 隔离级别

查看隔离级别: `show variables like 'transaction_isolation'`, 默认 repeatable read , 有可能幻读

| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
| -------- | ---- | ---------- | ---- |
| 未提交读 | 是   | 是         | 是   |
| 已提交读 | 否   | 是         | 是   |
| 可重复读 | 否   | 否         | 是   |
| 可序列化 | 否   | 否         | 否   |

#### 无索引行锁升级为表锁

索引失效会导致行锁升级为表锁

比如如果varchar列,在update 语句或者select 没有加单引号

#### 间隙锁

在执行过程中通过范围查找的话, 会锁定整个范围内所有的索引键值, 即使这个键值并不存在.

`update test_innodb_lock set b = '0629' where a > 1 and a < 6`

`insert into test_innodb_lock values(2, '2000')`   会被阻塞

a列 没有2的记录, 这样就是间隙锁, 第二行会阻塞

#### 如何锁定一行

`select * from test_innodb_lock where a = 8 for update` 锁定a 为8 的那一行

在另一个session 中无法更新那一行

### 总结

通过 `show status like 'innodb_row_lock%'` 查看系统上行锁的争夺情况

+-------------------------------+-------+
| Innodb_row_lock_current_waits | 0     | 当前正在等待锁定的数量
| Innodb_row_lock_time          | 26211 | 从系统启动到现在锁定总时间长度
| Innodb_row_lock_time_avg      | 6552  | 每次等待所花平均时间
| Innodb_row_lock_time_max      | 12464 |  从系统启动到现在等待最长的一次所花时间
| Innodb_row_lock_waits         | 4     |  系统启动到现在总共等待的次数
+-------------------------------+-------+

1. 尽量让所有数据检索都通过索引来完成, 避免无索引行锁升级为表锁
2. 合理设计索引,尽量缩小锁的范围
3. 尽可能减小检索条件, 避免间隙锁
4. 尽量控制事务大小, 减少锁定资源量和时间长度
5. 尽可能降低事务隔离等级

#### 页锁

开销和加锁时间介于表锁和行锁之间, 会出现死锁, 锁定粒度介于表锁和行锁之间, 并发度一般

## 主从复制

slave 会从master 读取binlog来进行数据同步

1. master 将改变记录到二进制日志, 这些记录过程叫做二进制日志事件, 
2. slave将master 的binary log events 拷贝到它的中继日志
3. slave重做中继日志中的事件, 将改变应用到自己的数据库中, MySQL复制是异步的且串行化的

#### 原则

1. 每个slave 只有一个master, 在[mysqld] 下写
2. 每个slave 只能有一个唯一的服务器ID
3. 每个master可以有多个slave

#### 配置

1. mysql 版本一致

server

```sql
grant replication slave on *.* to 'lisi'@'192.168.1.109' ;
```

## 调优

### 调优有风险

两个方面: 安全和性能

优化选择

　　优化成本:硬件>系统配置>数据库表结构>SQL及索引

　　优化效果:硬件<系统配置<数据库表结构

mysql

msyqladmin                                 mysql客户端，可进行管理操作

mysqlshow                                  功能强大的查看shell命令

show [SESSION | GLOBAL] variables          查看数据库参数信息

SHOW [SESSION | GLOBAL] STATUS             查看数据库的状态信息

information_schema                         获取元数据的方法

SHOW ENGINE INNODB STATUS                  Innodb引擎的所有状态

SHOW PROCESSLIST                           查看当前所有连接session状态

explain                                    获取查询语句的执行计划

show index                                 查看表的索引信息

slow-log                                   记录慢查询语句

mysqldumpslow                              分析slowlog文件的



zabbix                  监控主机、系统、数据库（部署zabbix监控平台）

pt-query-digest         分析慢日志

mysqlslap               分析慢日志

sysbench                压力测试工具

mysql profiling         统计数据库整体状态工具    

Performance Schema      mysql性能状态统计的数据

workbench               管理、备份、监控、分析、优化工具（比较费资源）

### 数据库层面问题解决思路

#### 针对突然的业务办理卡顿，无法进行正常的业务处理！需要立马解决的场景！

1 、show processlist

2、explain select id ,name from stu where name='clsn'; # ALL id name age sex

select id,name from stu where id=2-1 函数 结果集>30;

　　　 show index from table;

3、通过执行计划判断，索引问题（有没有、合不合理）或者语句本身问题

4、show status like '%lock%'; # 查询锁状态

　　kill SESSION_ID; # 杀掉有问题的session

#### 针对业务周期性的卡顿，例如在每天10-11点业务特别慢，但是还能够使用，过了这段时间就好了。

1、查看slowlog，分析slowlog，分析出查询慢的语句。

2、按照一定优先级，进行一个一个的排查所有慢语句。

3、分析top sql，进行explain调试，查看语句执行时间。

4、调整索引或语句本身。

### 系统层面

cpu方面

vmstat、sar top、htop、nmon、mpstat

内存

​    free 、ps -aux 、

IO设备（磁盘、网络）

iostat 、 ss  、 netstat 、 iptraf、iftop、lsof、

**vmstat** **命令说明：**

Procs：r显示有多少进程正在等待CPU时间。b显示处于不可中断的休眠的进程数量。在等待I/O

Memory：swpd显示被交换到磁盘的数据块的数量。未被使用的数据块，用户缓冲数据块，用于操作系统的数据块的数量

Swap：操作系统每秒从磁盘上交换到内存和从内存交换到磁盘的数据块的数量。s1和s0最好是0

Io：每秒从设备中读入b1的写入到设备b0的数据块的数量。反映了磁盘I/O

System：显示了每秒发生中断的数量(in)和上下文交换(cs)的数量

Cpu：显示用于运行用户代码，系统代码，空闲，等待I/O的CPU时间

**iostat命令说明**

实例命令：  iostat -dk 1 5

　　　　    iostat -d -k -x 5 （查看设备使用率（%util）和响应时间（await））

tps：该设备每秒的传输次数。“一次传输”意思是“一次I/O请求”。多个逻辑请求可能会被合并为“一次I/O请求”。

iops ：硬件出厂的时候，厂家定义的一个每秒最大的IO次数

"一次传输"请求的大小是未知的。

kB_read/s：每秒从设备（drive expressed）读取的数据量；

KB_wrtn/s：每秒向设备（drive expressed）写入的数据量；

kB_read：读取的总数据量；

kB_wrtn：写入的总数量数据量；这些单位都为Kilobytes。

### 系统层面问题解决办法

#### 　　你认为到底负载高好，还是低好呢？

　　　　在实际的生产中，一般认为 cpu只要不超过**90%**都没什么问题 。

#### cpu负载高，IO负载低

存不够

磁盘性能差

SQL问题 ------>去数据库层，进一步排查sql问题

IO出问题了（磁盘到临界了、raid设计不好、raid降级、锁、在单位时间内tps过高）

tps过高: 大量的小数据IO、大量的全表扫描

读操作多

#### IO负载高，cpu负载低

大量小的IO 写操作：

　　autocommit ，产生大量小IO

　　IO/PS,磁盘的一个定值，硬件出厂的时候，厂家定义的一个每秒最大的IO次数。

大量大的IO 写操作

　　SQL问题的几率比较大

#### IO和cpu负载都很高

硬件不够了或sql存在问题

### 数据库参数优化

thread_concurrency       # 并发线程数量个数

​    sort_buffer_size         # 排序缓存

​    read_buffer_size         # 顺序读取缓存

​    read_rnd_buffer_size     # 随机读取缓存

​    key_buffer_size          # 索引缓存, 在机器能承受范围内, 越大越好

​    thread_cache_size        # (1G—>8, 2G—>16, 3G—>32, >3G—>64)

### 连接层

max_connections           # 最大连接数，看交易笔数设置    

​    max_connect_errors        # 最大错误连接数，能大则大

​    connect_timeout           # 连接超时

​    max_user_connections      # 最大用户连接数

​    skip-name-resolve         # 跳过域名解析

​    wait_timeout              # 等待超时

​    back_log                  # 可以在堆栈中的连接数量

### 存储引擎层

default-storage-engine

innodb_buffer_pool_size       # 没有固定大小，50%测试值，看看情况再微调。但是尽量设置不要超过物理内存70%

innodb_file_per_table=(1,0)

innodb_flush_log_at_trx_commit=(0,1,2) # 1是最安全的，0是性能最高，2折中

binlog_sync

Innodb_flush_method=(O_DIRECT, fdatasync)

innodb_log_buffer_size        # 100M以下

innodb_log_file_size          # 100M 以下

innodb_log_files_in_group     # 5个成员以下,一般2-3个够用（iblogfile0-N）

innodb_max_dirty_pages_pct   # 达到百分之75的时候刷写 内存脏页到磁盘。

log_bin

max_binlog_cache_size         # 可以不设置

max_binlog_size               # 可以不设置

innodb_additional_mem_pool_size    #小于2G内存的机器，推荐值是20M。32G内存以上100M



### 另一个参数说明

- key_buffer_size索引块缓存区大小, 针对MyISAM存储引擎,该值越大,性能越好.但是超过操作系统能承受的最大值,反而会使mysql变得不稳定. ----这是很重要的参数
- sort_buffer_size 这是索引在排序缓冲区大小，若排序数据大小超过该值，则创建临时文件，注意和MyISAM_sort_buffer_size的区别----这是很重要的参数
- read_rnd_buffer_size当排序后按排序后的顺序读取行时，则通过该缓冲区读取行，避免搜索硬盘。将该变量设置为较大的值可以大大改进ORDER BY的性能。但是，这是为每个客户端分配的缓冲区，因此你不应将全局变量设置为较大的值。相反，只为需要运行大查询的客户端更改会话变量
- join_buffer_size用于表间关联(join)的缓存大小
- tmp_table_size缓存表的大小
- table_cache允许 MySQL 打开的表的最大个数，并且这些都cache在内存中
- delay_key_write针对MyISAM存储引擎,延迟更新索引.意思是说,update记录时,先将数据up到磁盘,但不up索引,将索引存在内存里,当表关闭时,将内存索引,写到磁盘

## 数据库隔离试验

mysql 中的variable 分为global 和session

```
// 设置隔离等级
SET session TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
read committed, repeatable read, serilizabled
```

mysql 默认repeatable read, 而且解决了幻读, 也就是说在一个事务中, 前后查询到的相同

repeatable read 和serializable 不同就是serializable 会锁住整张表, 导致插入失败

read uncommitted 

又有脏数据问题

```
begin;
insert / update /delete // 此时在另一个session 可以查询到没有commit 的改变
commit;
```

read committed 会有不可重复读问题, 可以理解为在事务中多次select 结果不一样(修改, 增加, 删除)

在一个事务开始之后, select 语句之前, 另一个session insert 的数据可以在事务的第一个select 中显示出来, 但是多个select 显示的相同, 也就是说和第一个select 相同.

```
begin;
select * from account;
// 在另一个session 中修改, 增加, 删除
select * from account;
commit;

```

repeatable read 解决了不可重复读和幻读, 也就是一个事务中多次select 结果一定相同

但是repeatable read 比上一个read committed 不好的地方在于使用了间隙锁, 如果一个事务中使用update 或者delete  , 带有where 语句, 在另一个session2 中insert 符合条件的记录, 那么session2会卡住

```
begin;
update account set name =  'jianxi' where balance = 16;
// 在另一个session, insert into account (name, balance) values('bb', 16);
// 会卡住
commit; // 另一个session 会成功

```

### mysql 聚簇索引和辅助索引  Clustered and Secondary Indexes

<https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html>

每一个Innodb 表有一个存储着行数据的聚簇索引, 通常聚簇索引和主键含义相同,

* 当你在一张表上创建一个主键时, innodb 会使用主键作为聚簇索引, 如果一张表中没有值不重复, 值不为null的列, 那么最好新增加一个自动增加的列作为主键
* 如果没有定义主键, mysql 会使用第一个唯一索引所在列,并且值都不为null,   unique index, 
* 如果上面两条都不符合, innodb 内部生成一个聚簇索引列, gen_clust_index

#### 为什么聚簇索引加快查询

因为聚簇索引直接指向存储着所有行数据的page, 如果一张表很大, 那么会节省磁盘io,

#### 辅助索引和聚簇索引有什么不同

所有非聚簇索引是辅助索引,辅助索引存储聚簇索引( 主键 ), 聚簇索引存储表数据, mysql 的辅助索引有 唯一索引, 索引(单列和多列), 全文索引

### innodb 索引物理结构

使用 b 树

#### 聚簇索引查询

聚簇索引，将主键组织到一棵B+树中，而行数据就储存在叶子节点上，若使用”where id = 14″这样的条件查找主键，则按照B+树的检索算法即可查找到对应的叶节点，之后获得行数据。若对Name列进行条件搜索，则需要两个步骤：第一步在辅助索引B+树中检索Name，到达其叶子节点获取对应的主键。第二步使用主键在主索引B+树种再执行一次B+树检索操作，最终到达叶子节点即可获取整行数据。

#### 看上去聚簇索引的效率明显要低于非聚簇索引，因为每次使用辅助索引检索都要经过两次B+树查找，这不是多此一举吗？聚簇索引的优势在哪？

1.  由于行数据和叶子节点存储在一起，这样主键和行数据是一起被载入内存的，找到叶子节点就可以立刻将行数据返回了，如果按照主键Id来组织数据，获得数据更快。
2. 辅助索引使用主键作为”指针” 而不是使用地址值作为指针的好处是，减少了当出现行移动或者数据页分裂时辅助索引的维护工作，使用主键值当作指针会让辅助索引占用更多的空间，换来的好处是InnoDB在移动行时无须更新辅助索引中的这个”指针”。使用聚簇索引就可以保证不管这个主键B+树的节点如何变化，辅助索引树都不受影响。
3. 