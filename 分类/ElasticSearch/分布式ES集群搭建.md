# 分布式ES集群搭建

修改es目录下的conf/elasticsearch.yml

加入如下内容

```
cluster.name: guan
node.name: master
node.master: true
network.host: 192.168.25.130

```


```
cluster.name: guan
node.name: slave1

notwork.host: 192.168.25.130

```


遇到的问题 failed to send join request to master

[来源](https://www.cnblogs.com/wxw16/p/6160186.html)

```
[2018-03-19T04:19:17,448][INFO ][o.e.d.z.ZenDiscovery     ] [slave1] failed to send join request to master [{master}{ItqWCncSR76IIO7UHqiMog}{xJ8CMyr9QPqwWScWDZ3tJw}{192.168.25.130}{192.168.25.130:9300}], reason [RemoteTransportException[[master][192.168.25.130:9300][internal:discovery/zen/join]]; nested: IllegalArgumentException[can't add node {slave1}{ItqWCncSR76IIO7UHqiMog}{Ih3ixDHISn6Utwje7-S2kg}{192.168.25.130}{192.168.25.130:9301}, found existing node {master}{ItqWCncSR76IIO7UHqiMog}{xJ8CMyr9QPqwWScWDZ3tJw}{192.168.25.130}{192.168.25.130:9300} with the same id but is a different node instance]; ]
```

是因为复制的elasticsearch文件夹下包含了data文件中示例一的节点数据，需要把示例二data文件下的文件清空。

<!--
create time: 2018-04-14 20:15:27
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

