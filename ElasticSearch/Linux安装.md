# Linux安装elasearch5.5.2




进入 elasticsearch-6.1.3/bin目录下启动服务

./elasticsearch

提示错误

```
Exception in thread "main" java.lang.RuntimeException: don't run elasticsearch as root.
        at org.elasticsearch.bootstrap.Bootstrap.initializeNatives(Bootstrap.java:93)
        at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:144)
        at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:285)
        at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:35)
Refer to the log for complete error details.
```

因为版本的问题，最新的版本安全级别提高了，不允许采用root帐号启动，所以我们要添加一个用户。

```

    #添加一个用户：lucy
    $useradd lucy
    #给用户lucy设置密码，连续输入2次
    $passwd lucy
    #创建一个用户组 group1
    groupadd group1
    #分配 lili 到 group1 组
    usermod -G lucy group1
    #这里注意下，如果提示用户“group1”不存在，那么是因为服务器版本问题，你可以换成 usermod -G group1 lucy ,也就是用户和用户组对调一下使用。
   
   在elasticsearch-6.1.3目录下，建立两个文件夹，data logs
   mkdir -p data logs
   
    #在lucy 根目录下，给定用户权限。-R表示逐级（N层目录） ， * 表示 任何文件
    chown  -R lucy.group1 *
  
  或者  
   $chown -R lucy elasticsearch-5.2.2 
  
    #切换到lucy用户
   $ su lucy   // 进入elasticsearch目录
   $ ./bin/elasticsearch


    
    

```



几乎每一次装都会遇到的坑，这些坑只有5.x.x版本有，2.x.x版本的几乎没有遇到过：

1、机器内剩余内存较少，会启动失败，并且有类似如下报错：

```
Java HotSpot(TM) 64-Bit Server VM warning: INFO: os::commit_memory(0x000000008a660000, 1973026816, 0) failed; error='Cannot allocate memory' (errno=12)

```

这是因为es的jvm参数-Xmx和-Xms默认都为2G
修改config下的jvm.option文件

```
# vim elasticsearch/elasticsearch-5.5.1/config/jvm.opstions
将
-Xms2g
-Xmx2g 
改为
-Xms1g
-Xmx1g
或更小
-Xms512M
-Xmx512M

```

再次启动即可


```
ERROR: bootstrap checks failed
system call filters failed to install; check the logs and fix your configuration or disable system call filters at your own risk...
问题原因：因为Centos6不支持SecComp，而ES5.x.x默认bootstrap.system_call_filter为true进行检测，所以导致检测失败，失败后直接导致ES不能启动。详见 ：https://github.com/elastic/elasticsearch/issues/22899


解决方法：在elasticsearch.yml中配置bootstrap.system_call_filter为false，注意要在Memory下面: 
bootstrap.memory_lock: false 
bootstrap.system_call_filter: false
network.host:192.168.25.130

------

ERROR: bootstrap checks failed
max file descriptors [4096] for elasticsearch process likely too low, increase to at least [65536] 
max number of threads [1024] for user [lishang] likely too low, increase to at least [2048]


解决方法：切换到root用户，编辑limits.conf 添加类似如下内容


#vim /etc/security/limits.conf


添加如下内容:
* soft nofile 65536
* hard nofile 131072
* soft nproc 2048
* hard nproc 4096


------

max number of threads [1024] for user [lish] likely too low, increase to at least [2048]


解决方法：切换到root用户，进入limits.d目录下修改配置文件。
#vim /etc/security/limits.d/90-nproc.conf


修改如下内容：

* soft nproc 1024
修改为
* soft nproc 2048





-----

max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]


解决方法：切换到root用户修改配置sysctl.conf


#vim /etc/sysctl.conf


添加下面配置：
vm.max_map_count=655360
并执行命令：
#sysctl -p
然后，再启动elasticsearch，即可启动成功。


```


问题五：max file descriptors [4096] for elasticsearch process likely too low, increase to at least [65536]

解决：修改切换到root用户修改配置limits.conf 添加下面两行

```
命令:vi /etc/security/limits.conf

*        hard    nofile           65536
*        soft    nofile           65536
切换到es的用户。

```



