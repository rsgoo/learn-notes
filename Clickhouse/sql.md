#### Clickhouse 数据表操作

```sql
create table tb_demo01
(
    id       UInt16,
    age      UInt8,
    name     String,
    birthday Date,
    gender   FixedString(2)
) ENGINE = Log;

insert into tb_demo02 values (1,24,'zss', '2020-1-11', 'M');
insert into tb_demo02 values (2,23,'lss', '2010-1-11', 'M');
insert into tb_demo02 values (3,23,'www', '2007-1-11', 'M');
insert into tb_demo02 values (4,23,'zll', '2013-1-11', 'M');
------------------------------------------------------------------

create table tb_demo02
(
    id       UInt16,
    age      UInt8,
    name     String,
    birthday Date,
    gender   FixedString(2)
) ENGINE = StripeLog;

insert into tb_demo02 values (1,24,'zss', '2020-1-11', 'M');
insert into tb_demo02 values (2,23,'lss', '2010-1-11', 'M');
insert into tb_demo02 values (3,23,'www', '2007-1-11', 'M');
insert into tb_demo02 values (4,23,'zll', '2013-1-11', 'M');

---------------------------------------------------------------------

create table tb_data_type_val(
id Int16,
age UInt8,
sal Float32,
comm Float64,
money Decimal(7,2)
) ENGINE = Memory;

insert into tb_data_type_val values(1,21,30000,10000,99999.99);

insert into tb_data_type_val values(1,21,30000,10000,999999.199);

---------------------------------------------------------------------

create table tb_data_type_string(
id String,
gender FixedString(8)
) ENGINE = Memory;

insert into tb_data_type_string values('dongdong1', 'a123456b')

insert into tb_data_type_string values('dongdong2', 'a123456b1')


------------------------------------------------------------------------

create table tb_data_type_date(
cdate1 Date,
cdate2 Date32,
cdate3 DateTime,
cdate4 DateTime64
) engine=Memory;

insert into tb_data_type_date values('2010-10-10','2010-10-10','2010-10-10 10:10:10','2010-10-10 10:10:10')

insert into tb_data_type_date values('2020-20-20','2020-20-20','2020-20-20 20:20:20','2020-20-20 20:20:20')

insert into tb_data_type_date values(1634571594,1634571594,1634571594,1634571594)


insert into tb_data_type_date values(1634571665941,1634571665941,1634571665941,1634571665941)


------------------------------------------------------------------------
------------------------------------------------------------------------
------------------------------------------------------------------------

create table t_uuid(
x UUID,
y String
) engine=Memory;


insert into t_uuid values(generateUUIDv4(), 'node6');

insert into t_uuid select generateUUIDv4(), 'lss';


------------------------------------------------------------------------
------------------------------------------------------------------------
------------------------------------------------------------------------


create table t_enum(
color Enum('RED'=1,'BLUE'=2,'GREEN'=3)
)engine=Memory;

insert into t_enum values(1);

--------------------------------------------------------------------------
select array(1,2,3,4,5) as num, toTypeName(num);

create table t_friend(
id UInt8,
name String comment '朋友姓名',    
friends Array(String) comment '朋友列表'
)Engine=Memory;


insert into t_friend values(1,'zss', array('a1','a2','a3'));
insert into t_friend values(1,'lss', array('b1','b2','b3'));
insert into t_friend values(3,'222', array('c0','c1','c2','c3','c4','c5'));

select id,name,friends.size0 from t_friend;

select id, name,arrayMap(e->upper(e), friends) from t_friend;


===============================================================


create table t_nested(
id Int16,
name String,
properties Nested(
	pid UInt32,
    k String,
    v String)
) engine = Memory;


// Nestedz
insert into t_nested values(
1,
'hadoop',
[1,2,3],
['hostname','ip','role'],
['localhost','127.0.0.1','root']
);

insert into t_nested values(
3,
'linux',
[11,22,33,44],
['hostname','ip','role','OS'],
['localhost','127.0.0.1','root','linux']
);


================================================
tuple

create table t_tuples(
a Tuple(s String, i Int64)
) engine=Memory;

insert into t_tuples values(('a',10));
insert into t_tuples values(('b',20));
insert into t_tuples values(('c',30));

select tuple(1,'name', 1101.9) as x,toTypeName(x);


====================================================

create table t_ip(
url String,
ip String
) engine=Memory;

insert into t_ip values('aa.com','127.0.0.1'); 

create table t_ipv4(
url String,
ip IPv4
) engine=Memory;

insert into t_ip values('aa.com','127.0.0.1'); 


======================================================

CREATE TABLE table_map (a Map(String, UInt64)) ENGINE=Memory;
create table table_map(a Map(String, UInt64)) Engine=Memory;

INSERT INTO table_map VALUES ({'key1':1, 'key2':10}), ({'key1':2,'key2':20}), ({'key1':3,'key2':30});

insert into table_map values({'key1':1,'key2':2,'key3':3});
insert into table_map values({'key11':11,'key12':12,'key13':13});

```



####  Clickhouse 数据库引擎

```
grant all privileges on *.* to root@'%' identified by '11019';
```



```sql
create DATABASE  db_mysql engine=MySQL('localhost:3306', 'runoob', 'root', '11019');
```

```sql
create table tb_stripe_log(
id UInt8,
name String
)engine=StripeLog;

-----------------------------------

create table tb_tiny_log(
id UInt8,
name String
)engine=TinyLog;

-----------------------------------

create table tb_log(
id UInt8,
name String
)engine=Log;
```

---------------------------

```sql
在合并树引擎中必须指定主键 或是是 ORDER BY
不指定主键的情况下 order by 字段就是主键字段
ck中主键没有唯一约束

【在合并的分区上排序】

create table tb_merge(
uid UInt8,
name String,
age UInt8,
city String
) engine=MergeTree
partition by city
order by uid;

-------------------------------------
insert into tb_merge values(4,'a4',24,'bja4');

insert into tb_merge values(2,'a2',22,'bja2');

insert into tb_merge values
(1,'a1',21,'bja1'),
(3,'a3',23,'bja3'),
(5,'a3',25,'bja5');

insert into tb_merge values
(9,'a1',21,'bja6'),
(10,'a3',23,'bja2'),
(11,'a3',25,'bja1');

insert into tb_merge values
(12,'a1',21,'zy'),
(13,'a3',23,'zy'),
(14,'a3',23,'bj'),
(15,'a3',25,'zy');

insert into tb_merge values
(16,'a1',21,'bj'),
(17,'a3',23,'zy'),
(18,'a3',23,'bj'),
(19,'a3',25,'zy');


//进行数据合并
optimize table tb_merge final;


-----------------------------------------

#### 去掉分区合并， 如果执行optimize 则会将全部数据合并到一起
create table tb_merge2(
uid UInt8,
name String,
age UInt8,
city String
) engine=MergeTree
order by uid;

insert into tb_merge2 values
(1,'a1',21,'bja1'),
(3,'a3',23,'bja3'),
(5,'a3',25,'bja5');

insert into tb_merge2 values
(9,'a1',21,'bja6'),
(10,'a3',23,'bja2'),
(11,'a3',25,'bja1');

insert into tb_merge2 values
(12,'a1',21,'zy'),
(13,'a3',23,'zy'),
(14,'a3',23,'bj'),
(15,'a3',25,'zy');

insert into tb_merge2 values
(16,'a1',21,'bj'),
(17,'a3',23,'zy'),
(18,'a3',23,'bj'),
(19,'a3',25,'zy');

### 测试添加数据
insert into tb_merge2 values
(20,'a1',21,'bj'),
(21,'a3',23,'zy'),
(22,'a3',23,'bj'),
(23,'a3',25,'zy');


====================================
create table tb_orders_back(
oid UUID,
money Decimal(10,2),
ctime DateTime
) Engine = MergeTree
primary key oid
order by (oid,ctime)
partition by toYYYYMM(ctime);

create table tb_orders(
oid UUID,
money Decimal(10,2),
ctime DateTime
) Engine = MergeTree()
primary key oid
order by (oid,ctime)
partition by toYYYYMM(ctime);


insert into tb_orders values(generateUUIDv4(),100.00,now());
insert into tb_orders values(generateUUIDv4(),200.00,now());
insert into tb_orders values(generateUUIDv4(),300.00,now());
insert into tb_orders values(generateUUIDv4(),400.00,'2021-01-12 12:12:11');
insert into tb_orders values(generateUUIDv4(),400.00,'2021-01-02 22:12:11');
insert into tb_orders values(generateUUIDv4(),500.00,now());
insert into tb_orders values(generateUUIDv4(),600.00,now());
insert into tb_orders values(generateUUIDv4(),700.00,'2021-11-02 09:12:11');
insert into tb_orders values(generateUUIDv4(),900.00,'2021-11-12 19:22:11');
insert into tb_orders values(generateUUIDv4(),1600.00,'2021-06-02 09:12:11');
insert into tb_orders values(generateUUIDv4(),1900.00,'2021-06-12 19:22:11');

insert into tb_orders values(generateUUIDv4(),1900.00,'2021-11-12 09:52:11');


================================================
create table tb_orders_1(
oid Int8,
money Decimal(10,2),
ctime DateTime
) Engine = MergeTree()
primary key oid
order by (oid,ctime)
partition by toYYYYMM(ctime);

insert into tb_orders_1 values(1,100.00,now());

insert into tb_orders_1 values(2,200.00,now());

========================================================

create table tb_replacingMergeTree1(
oid Int8,
money Decimal(10,2),
ctime DateTime
) Engine = ReplacingMergeTree()
order by (oid)
partition by toDate(ctime);

insert into tb_replacingMergeTree1 values(3,30,'2021-01-01 11:11:11');

insert into tb_replacingMergeTree1 values(1,10,'2021-01-01 11:11:01');
insert into tb_replacingMergeTree1 values(1,10,'2021-01-01 11:11:01');

insert into tb_replacingMergeTree1 values(2,20,'2021-11-01 11:11:11');

insert into tb_replacingMergeTree1 values(1,10,'2021-01-01 11:11:01');

==========================================================================

# ReplacingMergeTree(version) 保留分区内 版本较大的数据
# version 可以是数值类型，也可以是时间类型

create table tb_replacingMergeTree2(
oid Int8,
money Decimal(10,2),
ctime DateTime
) Engine = ReplacingMergeTree(ctime)
order by (oid)
partition by toDate(ctime);

insert into tb_replacingMergeTree2 values(3,30,'2021-01-01 11:11:11');

insert into tb_replacingMergeTree2 values(1,10,'2021-01-01 11:11:01');
insert into tb_replacingMergeTree2 values(1,10,'2021-01-01 11:11:21');

insert into tb_replacingMergeTree2 values(2,20,'2021-11-01 11:11:11');
insert into tb_replacingMergeTree2 values(4,40,'2021-11-01 11:11:21');
insert into tb_replacingMergeTree2 values(1,10,'2021-01-01 11:11:11');

insert into tb_replacingMergeTree2 values(2,60,'2021-11-01 11:21:11');

------------------------------------------------------------
create table tb_replacingMergeTree3(
oid Int8,
money Decimal(10,2),
ctime DateTime,
version UInt8
) Engine = ReplacingMergeTree(version)
order by (oid)
partition by toDate(version);

insert into tb_replacingMergeTree3 values(3,30,'2021-01-01 11:11:11',1);

insert into tb_replacingMergeTree3 values(1,10,'2021-01-01 11:11:01',2);
insert into tb_replacingMergeTree3 values(1,10,'2021-01-01 11:11:21',2);

insert into tb_replacingMergeTree3 values(2,20,'2021-11-01 11:11:11',3);
insert into tb_replacingMergeTree3 values(4,40,'2021-11-01 11:11:21',4);
insert into tb_replacingMergeTree3 values(1,10,'2021-01-01 11:11:11',4);

insert into tb_replacingMergeTree3 values(2,60,'2021-11-01 11:21:11',5);
insert into tb_replacingMergeTree3 values(5,60,'2021-11-01 11:21:11',6);
insert into tb_replacingMergeTree3 values(5,60,'2021-12-01 11:21:11',6);


insert into tb_replacingMergeTree3 values(6,50,'2021-11-01 11:21:11',7);
insert into tb_replacingMergeTree3 values(6,50,'2021-11-01 11:31:11',7);
insert into tb_replacingMergeTree3 values(7,50,'2021-11-01 11:33:11',7);
```

##### 折叠合并树引擎

```sql
create table tb_cps_merge_tree_1(
user_id UInt64,
name String,
age UInt8,
sign Int8    
)engine = CollapsingMergeTree(sign)
order by user_id;

insert into tb_cps_merge_tree_1 values(1,'a1',11,1);
insert into tb_cps_merge_tree_1 values(6,'a6',16,1),(6,'a6',16,-1);
```



##### 带版本折叠合并树引擎

```sql
create table tb_vscmp(
user_id UInt64,
name String,
age UInt8,
sign Int8,
version UInt8
)engine = VersionedCollapsingMergeTree(sign,version)
order by user_id;

insert into tb_vscmp values(1001,'ada',18,-1,1);
insert into tb_vscmp values(1001,'ada',18,1,1),(101,'dad',19,1,1),(101,'dad',11,1,3);
insert into tb_vscmp values(101,'dad',11,1,2);
```

##### 求和合并树

```sql
create table tb_summing(
	id String,
    city String,
    money UInt32,
    comm Float64,
    ctime DateTime
)engine= SummingMergeTree()
partition by toDate(ctime)
order by (id,city)
primary key id;


# 分区上相同的id和city会自动合并求和
insert into tb_summing values
(1,'shanghai',10,20,'2021-06-12 01:11:12'),
(1,'shanghai',20,30,'2021-06-12 01:11:12'),
(3,'shanghai',10,20,'2021-11-12 01:11:12'),
(3,'beijing',10,20,'2021-11-12 01:11:12');

# 查询结果
SELECT *
FROM tb_summing

Query id: 868277f7-4671-4f19-ac1c-65bf3353d9e8

┌─id─┬─city─────┬─money─┬─comm─┬───────────────ctime─┐
│ 3  │ beijing  │    10 │   20 │ 2021-11-12 01:11:12 │
│ 3  │ shanghai │    10 │   20 │ 2021-11-12 01:11:12 │
└────┴──────────┴───────┴──────┴─────────────────────┘
┌─id─┬─city─────┬─money─┬─comm─┬───────────────ctime─┐
│ 1  │ shanghai │    30 │   50 │ 2021-06-12 01:11:12 │
└────┴──────────┴───────┴──────┴─────────────────────┘

# 再次插入一条

insert into tb_summing values(3,'beijing',110,120,'2021-11-12 01:11:12'),


#### 指定字段求和
create table tb_summing_1(
	id String,
    city String,
    money UInt32,
    comm Float64,
    ctime DateTime
)engine= SummingMergeTree(money)
partition by toDate(ctime)
order by (id,city)
primary key id;

insert into tb_summing_1 values
(1,'shanghai',10,20,'2021-06-12 01:11:12'),
(1,'shanghai',20,30,'2021-06-12 01:11:12'),
(3,'shanghai',10,20,'2021-11-12 01:11:12'),
(3,'beijing',110,120,'2021-11-12 01:11:12'),
(3,'beijing',10,20,'2021-11-12 01:11:12');
```

##### 视图

```sql
CREATE TABLE apps
(
    `id` Int32,
    `app_name` String,
    `url` String,
    `country` String
)ENGINE=MergeTree
primary key id;

INSERT INTO `apps` VALUES ('1', 'QQ APP', 'http://im.qq.com/', 'CN'), ('2', '微博 APP', 'http://weibo.com/', 'CN'), ('3', '淘宝 APP', 'https://www.taobao.com/', 'CN'),('4', '百度', 'https://www.baidu.com/', 'CN');


# 建立视图的时候同步数据，当表数据更新的时候，视图中的数据也同步更新。但是当删除的时候，物化视图中的数据不会被删除

## 数据不会同步更新
create materialized view apps_view engine=Log as select * from apps;  
## 数据同步更新
create materialized view apps_view engine=Log as populate select * from apps;

create materialized view apps_view as populate select * from apps;
```

##### 聚合引擎

```sql
#1 建立明细表
create table detail_table(
	id UInt8,
	ctime Date,
    money UInt64
) engine=MergeTree()
partition by toDate(ctime)
order by id;

#2 插入明细数据
insert into detail_table values(1,'2021-08-06',100);
insert into detail_table values(1,'2021-08-06',100);
insert into detail_table values(2,'2021-08-07',200);
insert into detail_table values(2,'2021-08-07',200);

#3 建立预先聚合表
create table agg_table(
	id UInt8,
    ctime Date,
    cnt AggregateFunction(count,UInt64)
)engine=AggregatingMergeTree()
partition by toDate(ctime)
order by id;


#4 从聚合表中读取数据，插入到聚合表
insert into agg_table
select id,ctime,countState(money) from detail_table group by id,ctime;

```

##### MySQL引擎

```sql
# MySQL 建表语句
create table tb_test1(
	id int,
	name varchar(20)
)engine=innodb DEFAULT CHARSET=utf8mb4;

insert into tb_test1 values(1,"a1"),(2,"a2"),(3,"a3");

---------------------------------------------------------------------

# clickhouse 建表语句
create table tb_test1_mysql(
	id Int32,
    name String
)engine =MySQL('127.0.0.1:3306', 'ck','tb_test1', 'root', '11019');

insert into tb_test1_mysql values(4,'a4'),(5,'a5'),(6,'a6');

```

##### 通过函数直接操作MySQL

```mysql
select * from mysql('127.0.0.1:3306','runoob','apps','root','11019');
```

##### 文件引擎

> 加载本地文件数据

```sql
create table tb_file(
    id Int8,
    name String,
    age UInt8
)engine=File(CSV);
```

##### 将文本文件加载到 `Clickhouse` 表中

````sql
#apps.log 文件内容如下
```
11,aa.com,https://www.aa.com,CN
12,bb.com,https://www.bb.com,CN
13,cc.com,https://www.cc.com,CN
```
# 文件导入到ClickHosue
cat apps.log  |clickhouse-client --password -q 'insert into doit26.apps format CSV';

clickhouse-client --password -q 'insert into doit26.apps format CSV' < users.txt

### 带分割符号

````



##### Buffer引擎

【Buffer 表并不是实时刷新数据的，只有在阈值条件满足时才会刷新。阈值条件由三组【最小值和最大值】组成。含义如下

- min / max_time：时间条件的最大、最小值，单位是秒。从第一次向表内写入数据的时候开始计算；
- min / max_rows：数据行条件的最大、最小值；
- min / max_bytes：数据体量条件的 最大、最小值，单位是字节。

【Buffer 表刷新的判断条件依据有三个；刷新条件根据三个阈值的最大、最小值判断

1：如果三组条件中 【所有的最小阈值】都已经满足，则触发刷新操作；

2：如果三组条件中 【其中之一的最大值】已经满足，则触发刷新操作；

【buffer建表语句

```sql
create table xx(
	id Int64
) engine=Log;

create table buffer_to_xx as memory_1 engine=Buffer(default,xx,16(并发数),10(min_time),100(max_time),10000,1000000,10000000,100000000)

=============================================================
# 创建一个目标表
create table tb_user_target(uid Int8, name String) Engine=TinyLog;

# 创建一个缓存表

create table tb_user_buffer as tb_user_target engine=Buffer(doit26database,tb_user_target,16,10,100,1000,10000,10000000,100000000);

# 向缓存表中插入数据
insert into tb_user_buffer values(1,'a1'),(2,'a2'),(3,'a3');

# 等待以后查询目标表中的数据
select * from tb_user_target;

# 批量插入缓存表
insert into tb_user_buffer select number+1,'zss' from numbers(100000);

```

##### 表操作

```sql
# 只有MergeTree引擎才能修改表结构
create table test_alter1(
	id Int8,
    name String
)engine=MergeTree()
order by id;

# 设置索引力度
SETTINGS index_granularity =8192;

# 增加字段
alter table test_alter1 add column age UInt8;

# 修改字段
alter table test_alter1 modify column age Int32;

# 删除字段
alter table test_alter1 drop column age;

# 给字段添加注释
alter table test_alter1 comment column name '用户名';

# 修改表名
rename table test_alter1 to test_alter1_new;

# 修改多张表名
rename table test_alter1 to test_alter1_new, table_old to table_new;

# 移动数据表
rename table test_alter1 to doit26.test_alter1;
```

```sql
clickhouse-client --password --format_csv_delimiter='#' -q 'insert into doit26.apps format CSV' < apps_new.log
```

##### 快速复制一张表

```sql
# 复制表结构 + 复制表数据
create table apps_new as apps;
insert into apps_new select * from apps;

# 建表+复制数据一步到位
create table apps_one engine=Log as select * from apps;
```

##### 分区操作

- 分区表一定是 MergeTree

```sql
create table t_partition_1(
id String,
ctime DateTime
)engine=MergeTree()
partition by toYYYYMM(ctime)
order by id
settings index_granularity=8192;

insert into t_partition_1 values(1,now()),(2,'2021-06-07 11:22:33'),(3,'2011-06-17 09:12:33');

insert into t_partition_1 values(1,now()),(2,'2012-09-09 11:22:33'),(3,'2012-09-10 09:12:33');

# 查看数据表分区情况
select name,table,partition from system.parts where table='t_partition_1';

# 删除分区【删除分区后，分区中的所有数据将被清除】
alter table t_partition_1 drop partition '201106';

# 复制分区
create table t_partition_2 as t_partition_1;
## 将表1的202110分区数据复制到2表
alter table t_partition_2 replace partition '202110' from t_partition_1;hu

# 分区的装载与卸载
## 卸载
alter table t_partition_1 detach partition '201209';

## 装载
alter table t_partition_1 attach partition '201209';


```

##### arrayJoin()

```sql
create table test_array_join(
	id Int8,
    vs String
)engine=Memory;

insert into test_array_join values(1,'a1,a11,a111'),(2,'b2,b22,b222'),(3,'c3,c33,c333');

with splitByString(',', vs) as vs_array select *, vs_array from test_array_join;

# 查询数据如下
select *,splitByString(',',vs) as vs_array,x from test_array_join array join vs_array as x;
<==============================================================
Query id: cb640a89-e5db-4a70-8265-e52645bb1705

┌─id─┬─vs──────────┬─vs_array────────────┬─x────┐
│  1 │ a1,a11,a111 │ ['a1','a11','a111'] │ a1   │
│  1 │ a1,a11,a111 │ ['a1','a11','a111'] │ a11  │
│  1 │ a1,a11,a111 │ ['a1','a11','a111'] │ a111 │
│  2 │ b2,b22,b222 │ ['b2','b22','b222'] │ b2   │
│  2 │ b2,b22,b222 │ ['b2','b22','b222'] │ b22  │
│  2 │ b2,b22,b222 │ ['b2','b22','b222'] │ b222 │
│  3 │ c3,c33,c333 │ ['c3','c33','c333'] │ c3   │
│  3 │ c3,c33,c333 │ ['c3','c33','c333'] │ c33  │
│  3 │ c3,c33,c333 │ ['c3','c33','c333'] │ c333 │
└────┴─────────────┴─────────────────────┴──────┘
9 rows in set. Elapsed: 0.001 sec. 
==============================================================>
```

##### 查询立方体模型

```sql
create table table_with(
    id UInt8,
    visit UInt8,
    province String,
    city String,
    area String
)engine=MergeTree()
order by id;

insert into table_with values(1,11,'北京','海淀','永泰庄');
insert into table_with values(2,12,'北京','海淀','林翠桥');
insert into table_with values(3,13,'北京','海淀','奥森南门');
insert into table_with values(4,14,'北京','朝阳','来广营');
insert into table_with values(5,15,'北京','朝阳','望京站');
insert into table_with values(6,16,'北京','朝阳','望京西站');
insert into table_with values(7,17,'北京','石景山','古城');
insert into table_with values(8,18,'北京','石景山','首钢');
insert into table_with values(9,19,'北京','石景山','八角');
insert into table_with values(10,10,'遵义','播州','龙坑');

select province, count(visis) as ct group by province;

select province, count(visit) as ct from table_with  group by province;

select province, city,count(visit) as ct from table_with group by province,city;

select province, city,sum(visit) from table_with group by province,city;

# with cube 操作
select province, city,sum(visit) from table_with group by province,city,area with cube;

# with rollup 操作
select province, city,sum(visit) from table_with group by province,city,area with rollup;

# with totals 操作
select province, city,sum(visit) from table_with group by province,city,area with totals;

```

##### Clickhouse 函数

```sql
# 将 float 的 11.018 转换为Int8类型
select cast(11.018,'Int8');
select cast(11.018 as Int8);

# 单个条件函数
select if(1==2,'11','22');

# 多条件判断
select *,multiif(gender='M','男',gender='F','女','性别未知') as sex from tb_if;,

# json 字符串中字段提取
select visitParamExtractRaw('{"name":"dd","age":"27","language":"golang"}','language');

SELECT JSONExtract('{"name":"dd","age":"27","language":"golang"}', 'Tuple(String,String,UInt8)')

# json 查询
## 准备lang.json文件
{"name":"dd1","age":27,"language":"golang"}
{"name":"dd2","age":28,"language":"rust"}
{"name":"dd3","age":29,"language":"php"}
{"name":"dd4","age":30,"language":"clickhouse"}
{"name":"dd5","age":31,"language":"mysql"}


## 创建数据表
create table table_json(
str String
)engine=Log();

## 数据导入
### 数据格式地址：https://clickhouse.com/docs/en/interfaces/formats/
cat lang.json |clickhouse-client --password -q 'insert into doit36.table_json format JSONAsString';

## json字段提取
select *,visitParamExtractRaw(str,'language') from table_json;

select *,visitParamExtractRaw(str,'language'),visitParamExtractRaw(str,'age') as age from table_json;

with
	visitParamExtractRaw(str,'age') as age,
	visitParamExtractRaw(str,'language') as language
select language,age from table_json;

with
	visitParamExtractRaw(str,'language') as language,
	visitParamExtractRaw(str,'age') as age
select language,age from table_json;

### 去除language 字段的引号 【select trim(BOTH '""' FROM '"LANG"');】
with
	visitParamExtractRaw(str,'language') as language,
	visitParamExtractRaw(str,'age') as age
select trim(BOTH '""' FROM language),age from table_json;
```

 
