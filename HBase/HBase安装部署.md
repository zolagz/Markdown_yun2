# HBase安装部署


###下载安装包

解压安装包

```
wget http://mirrors.hust.edu.cn/apache/hbase/1.3.1/hbase‐1.3.1‐bin.tar.gz
```

```
 tar ‐zxvf hbase‐1.3.1‐bin.tar.gz ‐C /usr/local/dev/ cd usr/local/dev mv hbase‐1.3.1 hbase  
```

配置profile文件

```
export HBASE_HOME=/usr/local/dev/hbase-1.3.1

export PATH=$HBASE_HOME/bin:$PATH

```

source /etc/profile


###修改hbase配置文件

进入hbase/conf文件目录

修改regionservers

```
vi regionservers

node1
node2
node3

```
修改hbase‐site.xml

```
<configuration>

 <property>
       <name>hbase.rootdir</name>
       <value>hdfs://node1/hbase</value>
     </property>
     <property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
     </property>
     <property>
        <name>hbase.master.port</name>
        <value>16000</value>
     </property>
      <property>
       <name>hbase.zookeeper.property.dataDir</name>
       <value>/usr/local/dev/zookeeper-3.4.6/data/</value>
     </property>
     <property>
       <name>hbase.zookeeper.quorum</name>
       <value>node1,node2,node3</value>
     </property>
     <property>
       <name>hbase.zookeeper.property.clientPort</name>
       <value>2181</value>
     </property>

</configuration>
```

注意:以上配置集成的是hadoop ha集群。如果您的集群没有配置ha，hbase.rootdir 配置项目需要修改:hdfs://master:9000/hbase


###修改第三个配置文件

HBASE_MANAGES_ZK=false 表示，hbase和大家伙公用一个zookeeper集群，而不是自己管理集群。

```  	vi hbase‐env.sh      export JAVA_HOME=/export/servers/jdk   export HBASE_MANAGES_ZK=false

```


修改第四个配置文件 拷贝hadoop配置文件 拷贝hadoop的配置文件到hbase的配置文件目录

```
[root@node1 conf]# scp /usr/local/dev/hadoop-2.7.4/etc/hadoop/hdfs-site.xml  .

```


###分发配置文件

```
[root@node1 conf]# scp -r hbase-1.3.1 node2:/usr/local/dev/

[root@node1 conf]# scp -r hbase-1.3.1 node3:/usr/local/dev/

[root@node1 conf]# scp /etc/profile node2/etc/

[root@node1 conf]# scp /etc/profile node2/etc/

```

启动异常:

```   2017‐12‐27 06:27:54,882 INFO  [node01:16000.activeMasterManager] master.ServerManager: Waiting   for region servers count to settle; currently checked in 0, slept for 67247 ms, expecting   minimum of 1, maximum of 2147483647, timeout of 4500 ms, interval of 1500 ms.

```
   
###启动hbase
start-hbase.sh
   解决办法: 保证每台机器时间一致。
	ntpdate ‐u 0.uk.pool.ntp.org ntpdate ‐u 1.uk.pool.ntp.org

<!--

create time: 2018-03-12 21:37:37
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

