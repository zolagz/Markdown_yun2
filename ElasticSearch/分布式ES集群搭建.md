# 分布式ES集群搭建

修改es目录下的conf/elasticsearch.yml

加入如下内容

```
cluster.name: guan
node.name: master
node.master: true
network.host: 192.168.25.130

```

<!--
create time: 2018-04-14 20:15:27
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

