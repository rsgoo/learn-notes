概述：简要说明如何安装 kafka 消息中间件

[toc]

#### 1: download and install kafka

- *其中2.13 是 scala 版本、3.8.0是 kafka 版本*

> wget https://dlcdn.apache.org/kafka/3.8.0/kafka_2.13-3.8.0.tgz

> tar -xzf kafka_2.13-3.8.0.tgz

> cd kafka_2.13-3.8.0


#### 2: 使用 KRaft 模式启动 kafka

- Generate a Cluster UUID（生成集群UUID）

> KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"

- Format Log Directories（格式化日志目录）

> bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c config/kraft/server.properties

- Start the Kafka Server (启动 kafka 服务)

> bin/kafka-server-start.sh config/kraft/server.properties

#### 3: Create topic to store events

- 创建 topic

> bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092

- 描述 topic

> bin/kafka-topics.sh --describe --topic quickstart-events --bootstrap-server localhost:9092

- 列出所有的 topic

> bin/kafka-topics.sh --list --bootstrap-server localhost:9092

- 修改 topic

> bin/kafka-topics.sh --bootstrap-server localhost:9092 --topic quickstart-events --alter --partitions 2

- 删除 topic

> bin/kafka-topics.sh --bootstrap-server localhost:9092 --topic quickstart-events --delete


#### 4:【producer生产者】Write some events into topic

> bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092
> \> this is message 1
> \> this is message 2
> \> this is message 3


#### 5:【consumer消费者】Read the events

> bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092

[reference: Apache Kafka Quickstart](https://kafka.apache.org/quickstart)

