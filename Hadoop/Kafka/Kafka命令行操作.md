# Kafka命令行操作

* 在node1上创建一个订单的topic

```
bin/kafka-topics.sh --create --zookeeper node1:2181 --replication-factor 1 --partitions 1 --topic order

```
![](http://p2ehgqigv.bkt.clouddn.com/18-3-8/74154398.jpg)

* 在node2上	编写代码启动一个生产者，生产数据

```
bin/kafka-console-producer.sh --broker-list node1:9092 --topic order

```
![](http://p2ehgqigv.bkt.clouddn.com/18-3-8/12965176.jpg)

* 在node3上  编写代码启动给一个消费者，消费数据

```
bin/kafka-console-consumer.sh --zookeeper node1:2181 --from-beginning --topic order

```
![](http://p2ehgqigv.bkt.clouddn.com/18-3-8/13603096.jpg)



###更多命令

```
Kafka常用操作命令
l 查看当前服务器中的所有topic
bin/kafka-topics.sh --list --zookeeper  hadoop02:2181
l 创建topic
./kafka-topics.sh --create --zookeeper hadoop02:2181 --replication-factor 1 --partitions 3 --topic first
l 删除topic
sh bin/kafka-topics.sh --delete --zookeeper hadoop02:2181 --topic test
需要server.properties中设置delete.topic.enable=true否则只是标记删除或者直接重启。
l 通过shell命令发送消息
kafka-console-producer.sh --broker-list hadoop02:9092 --topic beicai
l 通过shell消费消息
sh bin/kafka-console-consumer.sh --zookeeper hadoop02:2181 --from-beginning --topic test1
l 查看消费位置
sh kafka-run-class.sh kafka.tools.ConsumerOffsetChecker --zookeeper hadoop02:2181 --group testGroup
l 查看某个Topic的详情
sh kafka-topics.sh --topic test --describe --zookeeper hadoop02:2181


```


<!--
create time: 2018-03-08 12:19:28
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

