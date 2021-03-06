# Flume安装部署
Flume的安装非常简单,上传安装包到数据源所在节点上,然后解压  
	
	tar -zxvf apache-flume-1.6.0-bin.tar.gz然后进入flume的目录，修改conf下的flume-env.sh，在里面配置JAVA_HOME根据数据采集需求配置采集方案，描述在配置文件中(文件名可任意自定义),指定采集方案配置文件，在相应的节点上启动flume agent先用一个最简单的例子来测试一下程序环境是否正常
先在flume的conf目录下新建一个文件

```
vi   netcat-logger.conf# 定义这个agent中各组件的名字a1.sources = r1a1.sinks = k1a1.channels = c1# 描述和配置source组件：r1a1.sources.r1.type = netcata1.sources.r1.bind = localhosta1.sources.r1.port = 44444# 描述和配置sink组件：k1a1.sinks.k1.type = logger# 描述和配置channel组件，此处使用是内存缓存的方式a1.channels.c1.type = memorya1.channels.c1.capacity = 1000a1.channels.c1.transactionCapacity = 100# 描述和配置source  channel   sink之间的连接关系a1.sources.r1.channels = c1a1.sinks.k1.channel = c1
```启动agent去采集数据

```bin/flume-ng agent -c conf -f conf/netcat-logger.conf -n a1  -Dflume.root.logger=INFO,console```
-c conf   指定flume自身的配置文件所在目录-f conf/netcat-logger.con  指定我们所描述的采集方案-n a1  指定我们这个agent的名字

测试```
nc node1 44444
```![](https://img-blog.csdn.net/20170210150351560?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


先要往agent采集监听的端口上发送数据，让agent有数据可采。随便在一个能跟agent节点联网的机器上：telnet anget-hostname  port   （telnet localhost 44444）###	Flume简单案例1．	采集目录到HDFS采集需求：服务器的某特定目录下，会不断产生新的文件，每当有新文件出现，就需要把文件采集到HDFS中去根据需求，首先定义以下3大要素	
*	采集源，即source——监控文件目录 :  spooldir	
*	下沉目标，即sink——HDFS文件系统  :  hdfs sink	
*	source和sink之间的传递通道——channel，可用file channel 也可以用内存channel配置文件编写：

```# Name the components on this agenta1.sources = r1a1.sinks = k1a1.channels = c1# Describe/configure the source##注意：不能往监控目中重复丢同名文件a1.sources.r1.type = spooldira1.sources.r1.spoolDir = /root/logsa1.sources.r1.fileHeader = true# Describe the sinka1.sinks.k1.type = hdfsa1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/a1.sinks.k1.hdfs.filePrefix = events-a1.sinks.k1.hdfs.round = truea1.sinks.k1.hdfs.roundValue = 10a1.sinks.k1.hdfs.roundUnit = minutea1.sinks.k1.hdfs.rollInterval = 3a1.sinks.k1.hdfs.rollSize = 20a1.sinks.k1.hdfs.rollCount = 5a1.sinks.k1.hdfs.batchSize = 1a1.sinks.k1.hdfs.useLocalTimeStamp = true#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本a1.sinks.k1.hdfs.fileType = DataStream# Use a channel which buffers events in memorya1.channels.c1.type = memorya1.channels.c1.capacity = 1000a1.channels.c1.transactionCapacity = 100# Bind the source and sink to the channela1.sources.r1.channels = c1a1.sinks.k1.channel = c1```Channel参数解释：capacity：默认该通道中最大的可以存储的event数量trasactionCapacity：每次最大可以从source中拿到或者送到sink中的event数量2．	采集文件到HDFS采集需求：比如业务系统使用log4j生成的日志，日志内容不断增加，需要把追加到日志文件中的数据实时采集到hdfs根据需求，首先定义以下3大要素	采集源，即source——监控文件内容更新 :  exec  ‘tail -F file’	下沉目标，即sink——HDFS文件系统  :  hdfs sink	Source和sink之间的传递通道——channel，可用file channel 也可以用 内存channel
	
配置文件编写：

```
# Name the components on this agenta1.sources = r1a1.sinks = k1a1.channels = c1# Describe/configure the sourcea1.sources.r1.type = execa1.sources.r1.command = tail -F /root/logs/test.loga1.sources.r1.channels = c1# Describe the sinka1.sinks.k1.type = hdfsa1.sinks.k1.hdfs.path = /flume/tailout/%y-%m-%d/%H%M/a1.sinks.k1.hdfs.filePrefix = events-a1.sinks.k1.hdfs.round = truea1.sinks.k1.hdfs.roundValue = 10a1.sinks.k1.hdfs.roundUnit = minutea1.sinks.k1.hdfs.rollInterval = 3a1.sinks.k1.hdfs.rollSize = 20a1.sinks.k1.hdfs.rollCount = 5a1.sinks.k1.hdfs.batchSize = 1a1.sinks.k1.hdfs.useLocalTimeStamp = true#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本a1.sinks.k1.hdfs.fileType = DataStream# Use a channel which buffers events in memorya1.channels.c1.type = memorya1.channels.c1.capacity = 1000a1.channels.c1.transactionCapacity = 100# Bind the source and sink to the channela1.sources.r1.channels = c1a1.sinks.k1.channel = c1

```

参数解析：

```•	rollInterval默认值：30hdfs sink间隔多长将临时文件滚动成最终目标文件，单位：秒；如果设置成0，则表示不根据时间来滚动文件；注：滚动（roll）指的是，hdfs sink将临时文件重命名成最终目标文件，并新打开一个临时文件来写入数据；•	rollSize默认值：1024当临时文件达到该大小（单位：bytes）时，滚动成目标文件；如果设置成0，则表示不根据临时文件大小来滚动文件；•	rollCount默认值：10当events数据达到该数量时候，将临时文件滚动成目标文件；如果设置成0，则表示不根据events数据来滚动文件；•	round默认值：false是否启用时间上的“舍弃”，这里的“舍弃”，类似于“四舍五入”。•	roundValue默认值：1时间上进行“舍弃”的值；•	roundUnit默认值：seconds时间上进行“舍弃”的单位，包含：second,minute,hour

```示例：

```a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/%Sa1.sinks.k1.hdfs.round = truea1.sinks.k1.hdfs.roundValue = 10a1.sinks.k1.hdfs.roundUnit = minute当时间为2015-10-16 17:38:59时候，hdfs.path依然会被解析为：/flume/events/20151016/17:30/00因为设置的是舍弃10分钟内的时间，因此，该目录每10分钟新生成一个。```

十、	Flume的load-balance、failover负载均衡是用于解决一台机器(一个进程)无法解决所有请求而产生的一种算法。Load balancing Sink Processor能够实现load balance功能，如下图Agent1是一个路由节点，负责将Channel暂存的Event均衡到对应的多个Sink组件上，而每个Sink组件分别连接到一个独立的Agent上，示例配置，如下所示：```a1.sinkgroups = g1a1.sinkgroups.g1.sinks = k1 k2 k3a1.sinkgroups.g1.processor.type = load_balancea1.sinkgroups.g1.processor.backoff = true  #如果开启，则将失败的sink放入黑名单a1.sinkgroups.g1.processor.selector = round_robin  # 另外还支持randoma1.sinkgroups.g1.processor.selector.maxTimeOut=10000 #在黑名单放置的超时时间，超时结束时，若仍然无法接收，则超时时间呈指数增长```Failover Sink Processor能够实现failover功能，具体流程类似load balance，但是内部处理机制与load balance完全不同。Failover Sink Processor维护一个优先级Sink组件列表，只要有一个Sink组件可用，Event就被传递到下一个组件。故障转移机制的作用是将失败的Sink降级到一个池，在这些池中它们被分配一个冷却时间，随着故障的连续，在重试之前冷却时间增加。一旦Sink成功发送一个事件，它将恢复到活动池。 Sink具有与之相关的优先级，数量越大，优先级越高。例如，具有优先级为100的sink在优先级为80的Sink之前被激活。如果在发送事件时汇聚失败，则接下来将尝试下一个具有最高优先级的Sink发送事件。如果没有指定优先级，则根据在配置中指定Sink的顺序来确定优先级。示例配置如下所示：a1.sinkgroups = g1a1.sinkgroups.g1.sinks = k1 k2 k3a1.sinkgroups.g1.processor.type = failovera1.sinkgroups.g1.processor.priority.k1 = 5  #优先级值, 绝对值越大表示优先级越高a1.sinkgroups.g1.processor.priority.k2 = 7a1.sinkgroups.g1.processor.priority.k3 = 6a1.sinkgroups.g1.processor.maxpenalty = 20000  #失败的Sink的最大回退期（millis）```
十一、	Flume实战案例1．	日志的采集和汇总1.1．	案例场景A、B两台日志服务机器实时生产日志主要类型为access.log、nginx.log、web.log 现在要求：把A、B 机器中的access.log、nginx.log、web.log 采集汇总到C机器上然后统一收集到hdfs中。但是在hdfs中要求的目录为：
/source/logs/access/20160101/**/source/logs/nginx/20160101/**/source/logs/web/20160101/**1.2．	场景分析1.3．	数据流程处理分析1.4．	功能实现①　	在服务器A和服务器B上创建配置文件	
exec_source_avro_sink.conf```
# Name the components on this agenta1.sources = r1 r2 r3a1.sinks = k1a1.channels = c1# Describe/configure the sourcea1.sources.r1.type = execa1.sources.r1.command = tail -F /root/data/access.loga1.sources.r1.interceptors = i1a1.sources.r1.interceptors.i1.type = static##  static拦截器的功能就是往采集到的数据的header中插入自##	 己定义的key-value对a1.sources.r1.interceptors.i1.key = typea1.sources.r1.interceptors.i1.value = accessa1.sources.r2.type = execa1.sources.r2.command = tail -F /root/data/nginx.loga1.sources.r2.interceptors = i2a1.sources.r2.interceptors.i2.type = statica1.sources.r2.interceptors.i2.key = typea1.sources.r2.interceptors.i2.value = nginxa1.sources.r3.type = execa1.sources.r3.command = tail -F /root/data/web.loga1.sources.r3.interceptors = i3a1.sources.r3.interceptors.i3.type = statica1.sources.r3.interceptors.i3.key = typea1.sources.r3.interceptors.i3.value = web# Describe the sinka1.sinks.k1.type = avroa1.sinks.k1.hostname = 192.168.200.101a1.sinks.k1.port = 41414# Use a channel which buffers events in memorya1.channels.c1.type = memorya1.channels.c1.capacity = 20000a1.channels.c1.transactionCapacity = 10000# Bind the source and sink to the channela1.sources.r1.channels = c1a1.sources.r2.channels = c1a1.sources.r3.channels = c1a1.sinks.k1.channel = c1```②　	在服务器C上创建配置文件	avro_source_hdfs_sink.conf  文件内容为```#定义agent名， source、channel、sink的名称a1.sources = r1a1.sinks = k1a1.channels = c1#定义sourcea1.sources.r1.type = avroa1.sources.r1.bind = mini2a1.sources.r1.port =41414#添加时间拦截器a1.sources.r1.interceptors = i1a1.sources.r1.interceptors.i1.type = org.apache.flume.interceptor.TimestampInterceptor$Builder#定义channelsa1.channels.c1.type = memorya1.channels.c1.capacity = 20000a1.channels.c1.transactionCapacity = 10000#定义sinka1.sinks.k1.type = hdfsa1.sinks.k1.hdfs.path=hdfs://192.168.200.101:9000/source/logs/%{type}/%Y%m%da1.sinks.k1.hdfs.filePrefix =eventsa1.sinks.k1.hdfs.fileType = DataStreama1.sinks.k1.hdfs.writeFormat = Text#时间类型a1.sinks.k1.hdfs.useLocalTimeStamp = true#生成的文件不按条数生成a1.sinks.k1.hdfs.rollCount = 0#生成的文件按时间生成a1.sinks.k1.hdfs.rollInterval = 30#生成的文件按大小生成a1.sinks.k1.hdfs.rollSize  = 10485760#批量写入hdfs的个数a1.sinks.k1.hdfs.batchSize = 10000flume操作hdfs的线程数（包括新建，写入等）a1.sinks.k1.hdfs.threadsPoolSize=10#操作hdfs超时时间a1.sinks.k1.hdfs.callTimeout=30000#组装source、channel、sinka1.sources.r1.channels = c1a1.sinks.k1.channel = c1

```③　	配置完成之后，在服务器A和B上的/root/data有数据文件access.log、nginx.log、web.log。先启动服务器C上的flume，启动命令在flume安装目录下执行 ：bin/flume-ng agent -c conf -f conf/avro_source_hdfs_sink.conf -name a1 -Dflume.root.logger=DEBUG,console	然后在启动服务器上的A和B，启动命令在flume安装目录下执行 ：bin/flume-ng agent -c conf -f conf/exec_source_avro_sink.conf -name a1 -Dflume.root.logger=DEBUG,console	2．	Flume自定义拦截器（了解）2.1．	案例背景介绍Flume是Cloudera提供的一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。Flume有各种自带的拦截器，比如：TimestampInterceptor、HostInterceptor、RegexExtractorInterceptor等，通过使用不同的拦截器，实现不同的功能。但是以上的这些拦截器，不能改变原有日志数据的内容或者对日志信息添加一定的处理逻辑，当一条日志信息有几十个甚至上百个字段的时候，在传统的Flume处理下，收集到的日志还是会有对应这么多的字段，也不能对你想要的字段进行对应的处理。2.2．	自定义拦截器根据实际业务的需求，为了更好的满足数据在应用层的处理，通过自定义Flume拦截器，过滤掉不需要的字段，并对指定字段加密处理，将源数据进行预处理。减少了数据的传输量，降低了存储的开销。2.3．	功能实现本技术方案核心包括二部分：	编写java代码，自定义拦截器内容包括：1.	定义一个类CustomParameterInterceptor实现Interceptor接口。2.	 在CustomParameterInterceptor类中定义变量，这些变量是需要到 Flume的配置文件中进行配置使用的。每一行字段间的分隔符(fields_separator)、通过分隔符分隔后，所需要列字段的下标（indexs）、多个下标使用的分隔符（indexs_separator)、多个下标使用的分隔符（indexs_separator)。3.	添加CustomParameterInterceptor的有参构造方法。并对相应的变量进行处理。将配置文件中传过来的unicode编码进行转换为字符串。4.	写具体的要处理的逻辑intercept()方法，一个是单个处理的，一个是批量处理。5.	接口中定义了一个内部接口Builder，在configure方法中，进行一些参数配置。并给出，在flume的conf中没配置一些参数时，给出其默认值。通过其builder方法，返回一个CustomParameterInterceptor对象。6.	定义一个静态类，类中封装MD5加密方法7.	 通过以上步骤，自定义拦截器的代码开发已完成，然后打包成jar， 放到Flume的根目录下的lib中	修改Flume的配置信息新增配置文件spool-interceptor-hdfs.conf，内容为：

```a1.channels = c1a1.sources = r1a1.sinks = s1#channela1.channels.c1.type = memorya1.channels.c1.capacity=100000a1.channels.c1.transactionCapacity=50000#sourcea1.sources.r1.channels = c1a1.sources.r1.type = spooldira1.sources.r1.spoolDir = /root/data/a1.sources.r1.batchSize= 50a1.sources.r1.inputCharset = UTF-8a1.sources.r1.interceptors =i1 i2a1.sources.r1.interceptors.i1.type =cn.itcast.interceptor.CustomParameterInterceptor$Buildera1.sources.r1.interceptors.i1.fields_separator=\\u0009a1.sources.r1.interceptors.i1.indexs =0,1,3,5,6a1.sources.r1.interceptors.i1.indexs_separator =\\u002ca1.sources.r1.interceptors.i1.encrypted_field_index =0a1.sources.r1.interceptors.i2.type = org.apache.flume.interceptor.TimestampInterceptor$Builder#sinka1.sinks.s1.channel = c1a1.sinks.s1.type = hdfsa1.sinks.s1.hdfs.path =hdfs://192.168.200.101:9000/flume/%Y%m%da1.sinks.s1.hdfs.filePrefix = eventa1.sinks.s1.hdfs.fileSuffix = .loga1.sinks.s1.hdfs.rollSize = 10485760a1.sinks.s1.hdfs.rollInterval =20a1.sinks.s1.hdfs.rollCount = 0a1.sinks.s1.hdfs.batchSize = 1500a1.sinks.s1.hdfs.round = truea1.sinks.s1.hdfs.roundUnit = minutea1.sinks.s1.hdfs.threadsPoolSize = 25a1.sinks.s1.hdfs.useLocalTimeStamp = truea1.sinks.s1.hdfs.minBlockReplicas = 1a1.sinks.s1.hdfs.fileType =DataStreama1.sinks.s1.hdfs.writeFormat = Texta1.sinks.s1.hdfs.callTimeout = 60000a1.sinks.s1.hdfs.idleTimeout =60

```启动：
bin/flume-ng agent -c conf -f conf/spooldir-hdfs.conf -name a1 -Dflume.root.logger=DEBUG,console	

<!--
create time: 2018-03-02 12:35:24
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

