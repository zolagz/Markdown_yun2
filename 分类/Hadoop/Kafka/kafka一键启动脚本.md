# kafka一键启动脚本

在目录下有三个文件夹分别是slave,start_kafka.sh,stop_kafka.sh

```
cat slave

node1
node2
node3

```

```
cat start_kafka.sh

cat /usr/local/dev/kafka/bin/slave | while read line
do
{
 echo $line
 ssh $line "source /etc/profile;nohup kafka-server-start.sh /usr/local/dev/kafka/config/server.properties >/dev/null 2>&1 &"
}&
wait
done 
```

```
cat stop_kafka.sh

cat /usr/local/dev/kafka/bin/slave | while read line
do
{
 echo $line
 ssh $line "source /etc/profile;jps |grep Kafka |cut -c 1-4 |xargs kill -s 9 "
}&
wait
done 
```

<!--
create time: 2018-07-04 11:14:23
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

