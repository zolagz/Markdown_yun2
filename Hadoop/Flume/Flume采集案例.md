# Flume采集案例

###案例2 监控指定文件的内容的改变，并在控制台输出

在conf文件下新建exec_example.conf,内容如下：

```
# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = exec

// 在这里指定监控文件路径
a1.sources.r1.command = tail -F /root/logs/flume_test.log
a1.sources.r1.shell = /bin/sh -c

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

在flume目录下执行如下命令：

```
bin/flume-ng agent \
--name a1 \
--conf conf \
--conf-file conf/exec_sexample.conf \
-Dflume.root.logger=INFO,console

```

然后追加内容到/root/logs/flume_test.log文件中



##案例3，将A服务器上的日志，实时采集到B服务器

技术选型：

	exec source + memory channel + avro sink
	
	avro source + memory channel + logger sink

配置avro-first.conf



```
# Name the components on this agent
first.sources = first_r1
first.sinks = first_k1
first.channels = first_c1

# Describe/configure the source

# 执行shell命令的类型
first.sources.first_r1.type = exec

// 在这里指定监控文件路径
first.sources.first_r1.command = tail -F /root/logs/flume_test.log
first.sources.first_r1.shell = /bin/sh -c

# Describe the sink
#sinks类型，要绑定hostname ,并指定port
first.sinks.first_k1.type = avro
first.sinks.first_k1.hostname = node1
first.sinks.first_k1.port = 44444

# Use a channel which buffers events in memory
first.channels.first_c1.type = memory

# Bind the source and sink to the channel
first.sources.first_r1.channels = first_c1
first.sinks.first_k1.channel = first_c1


```

配置avro-second.conf

```
# Name the components on this agent
av1.sources = rv1
av1.sinks = kv1
av1.channels = cv1

# Describe/configure the source
#sources类型avro  
av1.sources.rv1.type = avro
av1.sources.rv1.channels = cv1
av1.sources.rv1.bind =node1
av1.sources.rv1.port = 44444


# Describe the sink
# 指定控制台输出
av1.sinks.kv1.type = logger

# Use a channel which buffers events in memory
av1.channels.cv1.type = memory

# Bind the source and sink to the channel
av1.sources.rv1.channels = cv1
av1.sinks.kv1.channel = cv1

```


先启动second

```
bin/flume-ng agent \
--name av1 \
--conf conf \
--conf-file conf/avro-second.conf \
-Dflume.root.logger=INFO,console
```

如上步骤下来：测试通过







在启动first

```
bin/flume-ng agent \
--name first \
--conf conf \
--conf-file conf/avro-first.conf \
-Dflume.root.logger=INFO,console

```


<!--
create time: 2018-03-14 09:02:23
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

