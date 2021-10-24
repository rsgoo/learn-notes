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
```

