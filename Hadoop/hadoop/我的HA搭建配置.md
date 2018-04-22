# 我的HA搭建配置

第4阶段/hadoop/day09/day09视频

###第一步 修改core-site.xml

创建文件目录：/export/data/hddata/

```
<configuration>
<!-- 集群名称在这里指定！该值来自于hdfs-site.xml中的配置 -->
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://ns1/</value>
	</property>
<!-- 这里的路径默认是NameNode、DataNode、JournalNode等存放数据的公共目录 -->
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/export/data/hddata/</value>
	</property>

<!-- ZooKeeper集群的地址和端口。注意，数量一定是奇数，且不少于三个节点-->
	<property>
		<name>ha.zookeeper.quorum</name>
		<value>node1:2181,node2:2181,node3:2181</value>
	</property>
</configuration>

```

###修改 hdfs-site.xml

创建文件目录：/export/data/journaldata

配置ssh免密登录

```
<configuration>
        <!--指定hdfs的nameservice为ns1，需要和core-site.xml中的保持一致 -->
        <property>
                <name>dfs.nameservices</name>
                <value>ns1</value>
        </property>
        <!-- ns1下面有两个NameNode，分别是nn1，nn2 -->
        <property>
                <name>dfs.ha.namenodes.ns1</name>
                <value>nn1,nn2</value>
        </property>
        <!-- nn1的RPC通信地址 -->
        <property>
                <name>dfs.namenode.rpc-address.ns1.nn1</name>
                <value>node1:9000</value>
        </property>
        <!-- nn1的http通信地址 -->
        <property>
                <name>dfs.namenode.http-address.ns1.nn1</name>
                <value>node1:50070</value>
        </property>
        <!-- nn2的RPC通信地址 -->
        <property>
                <name>dfs.namenode.rpc-address.ns1.nn2</name>
                <value>node3:9000</value>
        </property>
        <!-- nn2的http通信地址 -->
        <property>
                <name>dfs.namenode.http-address.ns1.nn2</name>
                <value>node3:50070</value>
        </property>
                <!-- 指定NameNode的edits元数据在JournalNode上的存放位置 -->
        <property>
                <name>dfs.namenode.shared.edits.dir</name>
                <value>qjournal://node1:8485;node2:8485;node3:8485/ns1</value>
        </property>
        <!-- 指定JournalNode在本地磁盘存放数据的位置 -->
        <property>
                <name>dfs.journalnode.edits.dir</name>
                <value>/export/data/journaldata</value>
        </property>
        <!-- 开启NameNode失败自动切换 -->
        <property>
                <name>dfs.ha.automatic-failover.enabled</name>
                <value>true</value>
        </property>
        <!-- 配置失败自动切换实现方式 -->
        <property>
                <name>dfs.client.failover.proxy.provider.ns1</name>
                <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
        </property>
        <!-- 配置隔离机制方法，多个机制用换行分割，即每个机制暂用一行-->
        <property>
                <name>dfs.ha.fencing.methods</name>
                <value>
                        sshfence
                        shell(/bin/true)
                </value>
        </property>
        <!-- 使用sshfence隔离机制时需要ssh免登陆 -->
        <property>
                <name>dfs.ha.fencing.ssh.private-key-files</name>
                <value>/root/.ssh/id_rsa</value>
        </property>
        <!-- 配置sshfence隔离机制超时时间 -->
        <property>
                <name>dfs.ha.fencing.ssh.connect-timeout</name>
                <value>30000</value>
        </property>
</configuration>

```
###修改mapred-site.xml

```
<configuration>
<!-- 指定mr框架为yarn方式 -->
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
	</configuration>
```

###修改yarn-site.xml

```
<configuration>
<!-- 开启RM高可用 -->
	<property>
		<name>yarn.resourcemanager.ha.enabled</name>
		<value>true</value>
	</property>
<!-- 指定RM的cluster id -->
	<property>
		<name>yarn.resourcemanager.cluster-id</name>
		<value>yrc</value>
	</property>
<!-- 指定RM的名字 -->
	<property>
		<name>yarn.resourcemanager.ha.rm-ids</name>
		<value>rm1,rm2</value>
	</property>
<!-- 分别指定RM的地址 -->
	<property>
		<name>yarn.resourcemanager.hostname.rm1</name>
		<value>node1</value>
	</property>
	<property>
		<name>yarn.resourcemanager.hostname.rm2</name>
		<value>node2</value>
	</property>
<!-- 指定zk集群地址 -->
	<property>
		<name>yarn.resourcemanager.zk-address</name>
		<value>node1:2181,node2:2181,node3:2181</value>
	</property>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
</configuration>

```
###修改slaves

```
node1
node2
node3
```
###严格按照下面步骤执行
***启动步骤：***

第一：在三台机子上，启动zookeeper

第二：手动启动journalnode（分别在在hadoop05、hadoop06、hadoop07上执行）

	hadoop-daemon.sh start journalnode
	
运行jps命令检验，node1、node2、node3上多了JournalNode进程
	
第三：格式化namenode

格式化后会在根据core-site.xml中的hadoop.tmp.dir配置生成个文件，

然后将生产的文件拷贝到node2,node3上去

在hadoop00上执行命令:
			
```
	hdfs namenode -format
	scp  -r /export/data/hddata/ root@node3:/export/data/hddata/
	scp  -r /export/data/hddata/ root@node3:/export/data/hddata/
```
第四：格式化ZKFC(在node1上执行即可)
	
	hdfs zkfc -formatZK
	
第五：启动HDFS(在node1上执行)
	
	start-dfs.sh
	
第六：启动YARN(**注意**：是在hadoop02上执行start-yarn.sh，把namenode和resourcemanager分开是因为性能问题，因为他们都要占用大量资源，所以把他们分开了，他们分开了就要分别在不同的机器上启动)

在node1上

start-yarn.sh

在node2上
			
yarn-daemon.sh start resourcemanager


<!--
create time: 2018-04-18 10:09:34
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

