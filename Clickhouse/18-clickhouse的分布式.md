#### 分布式

- 在集群的每个节点上安装 ClickHouse 服务

- 配置 `/etc/clickhouse-server/config.xml` 中的 **<listen_host>::</listen_host>**, 目的是外部的可以访问

- 配置 Zookeeper，然后正常启动

- 应用 Zookeeper 的地址（修改config.xml文件）

  ```bash
  # java 安装  /etc/profile
  export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
  export PATH=$PATH:$JAVA_HOME/bin
  ```

-  在 /etc/clickhouse-server/config.xml 中 新增如下配置

  ```xml
  ### 在约723行左右
  <zookeeper>
      <node>
          <host>192.168.93.134</host>
          <port>2181</port>
      </node>
      <node>
          <host>192.168.93.132</host>
          <port>2181</port>
      </node>
      <node>
          <host>192.168.93.133</host>
          <port>2181</port>
      </node>
  </zookeeper>
  ```

- 在三个 clickhouse 节点中分别新建三个库/表

```sql
create database tb_zk;
use tb_zk;

create table tb_demo1(
	id Int8,
    name String
) engine=ReplicatedMergeTree('/clickhouse/tables/01/tb_demo1','192.168.93.134')
order by id;

insert into tb_demo1 values(1,'a134');
---------------------------------------------------------------------------

create database tb_zk;
use tb_zk;
create table tb_demo1(
	id Int8,
    name String
) engine=ReplicatedMergeTree('/clickhouse/tables/01/tb_demo1','192.168.93.132')
order by id;

create table tb_demo2(
	id Int8,
    name String
) engine=ReplicatedMergeTree('/clickhouse/tables/01/tb_demo2','192.168.93.132')
order by id;

insert into tb_demo1 values(1,'a132');

---------------------------------------------------------------------------
create database tb_zk;
use tb_zk;
create table tb_demo1(
	id Int8,
    name String
) engine=ReplicatedMergeTree('/clickhouse/tables/01/tb_demo1','192.168.93.133')
order by id;

insert into tb_demo1 values(1,'a133');

------------------------------------------------------------------------
```

- 在zookeeper 查看

```bash
./zkCli.sh 
ls /clickhouse/
```

- 创建数据表

  ```sql
  # 创建本地表
  create table tb_demo3 on cluster cluster1(
  id Int8,
  name String
  )engine=MergeTree()
  order by id;
  
  # 创建分布式表
  create table tb_demo3_all cluster cluster1 engine=Distributed('cluster1','default','tb_demo3',id)  as tb_demo3;
  ```

  

