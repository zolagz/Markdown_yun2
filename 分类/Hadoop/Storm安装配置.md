# Storm安装配置

前期需要安装jdk, zookeeper

下载安装storm1.1.1

解压安装包

```
tar -zxvf apache-storm-1.1.1.tar.gz -C /export/servers/cd /export/servers/ln -s apache-storm-1.1.1 storm
```

修改配置文件

```
mv /export/servers/storm/conf/storm.yaml /export/servers/storm/conf/storm.yaml.bakvi /export/servers/storm/conf/storm.yaml

```

粘贴如下内容到storm.yaml中

```
#指定storm使用的zk集群
storm.zookeeper.servers:
     - "node1"
     - "node2"
     - "node3"
#指定storm集群中的nimbus节点所在的服务器
nimbus.host: "storm01"
#指定nimbus启动JVM最大可用内存大小
nimbus.childopts: "-Xmx1024m"
#指定supervisor启动JVM最大可用内存大小
supervisor.childopts: "-Xmx1024m"
#指定supervisor节点上，每个worker启动JVM最大可用内存大小
worker.childopts: "-Xmx768m"
#指定ui启动JVM最大可用内存大小，ui服务一般与nimbus同在一个节点上。
ui.childopts: "-Xmx768m"
#指定supervisor节点上，启动worker时对应的端口号，每个端口对应槽，每个槽位对应一个worker
supervisor.slots.ports:
    - 6700
    - 6701
    - 6702
    - 6703

```

分发安装包

```
scp -r /export/servers/apache-storm-1.1.1 storm02:/export/servers然后分别在各机器上创建软连接cd /export/servers/ln -s apache-storm-1.1.1 storm
```

启动集群

*	在nimbus.host所属的机器上启动 nimbus服务```
cd /export/servers/storm/bin/nohup ./storm nimbus &
```*	在nimbus.host所属的机器上启动ui服务```
cd /export/servers/storm/bin/nohup ./storm ui &
```*	在其它个点击上启动supervisor服务```
cd /export/servers/storm/bin/nohup ./storm supervisor &
```

查看集群

访问nimbus.host:/8080，即可看到storm的ui界面。
![](http://p2ehgqigv.bkt.clouddn.com/18-3-9/93139922.jpg)


Storm常用操作命令

有许多简单且有用的命令可以用来管理拓扑，它们可以提交、杀死、禁用、再平衡拓扑。*	提交任务命令格式：storm jar 【jar路径】 【拓扑包名.拓扑类名】 【拓扑名称】```
bin/storm jar examples/storm-starter/storm-starter-topologies-0.10.0.jar storm.starter.WordCountTopology wordcount
```*	杀死任务命令格式：storm kill 【拓扑名称】 -w 10（执行kill命令时可以通过-w [等待秒数]指定拓扑停用以后的等待时间）```
storm kill topology-name -w 10
```*	停用任务命令格式：storm deactivte  【拓扑名称】```
storm deactivte topology-name
```我们能够挂起或停用运行中的拓扑。当停用拓扑时，所有已分发的元组都会得到处理，但是spouts的nextTuple方法不会被调用。销毁一个拓扑，可以使用kill命令。它会以一种安全的方式销毁一个拓扑，首先停用拓扑，在等待拓扑消息的时间段内允许拓扑完成当前的数据流。
*	启用任务命令格式：storm activate【拓扑名称】
        storm activate topology-name*	重新部署任务命令格式：storm rebalance  【拓扑名称】
        storm rebalance topology-name
        再平衡使你重分配集群任务。这是个很强大的命令。比如，你向一个运行中的集群增加了节点。再平衡命令将会停用拓扑，然后在相应超时时间之后重分配worker，并重启拓扑。


<!--
create time: 2018-03-09 20:54:39
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

