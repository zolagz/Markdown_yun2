# Hadoop之shell常用命令

###-ls
* 使用方法:hadoop fs -ls [-h] [-R] <args> 
* 功能:显示文件、目录信息。示例:
	
	hadoop fs -ls /user/hadoop/file1###-mkdir* 使用方法:hadoop fs -mkdir [-p] <paths>* 功能:在 hdfs 上创建目录，-p 表示会创建路径中的各级父目录。 示例:

```
hadoop fs -mkdir –p /user/hadoop/dir1
```
	###-put* 使用方法:hadoop fs -put [-f] [-p] [ -|<localsrc1> .. ]. <dst> 
* 功能:将单个 src 或多个 srcs 从本地文件系统复制到目标文件系统。	-p:保留访问和修改时间，所有权和权限。	-f:覆盖目的地(如果已经存在)示例:

```
hadoop fs -put -f localfile1 localfile2 /user/hadoop/hadoopdir```
###-get* 使用方法:hadoop fs -get [-ignorecrc] [-crc] [-p] [-f] <src> <localdst> -ignorecrc:跳过对下载文件的 CRC 检查。-crc:为下载的文件写 CRC 校验和。功能:将文件复制到本地文件系统。示例:hadoop fs -get hdfs://host:port/user/hadoop/file localfile###-appendToFile* 使用方法:hadoop fs -appendToFile <localsrc> ... <dst> 
* 功能:追加一个文件到已经存在的文件末尾示例:

```
hadoop fs -appendToFile localfile /hadoop/hadoopfile

```
###-cat
* 使用方法:hadoop fs -cat [-ignoreCrc] URI [URI ...] 
* 功能:显示文件内容到 stdout示例:

```
hadoop fs -cat /hadoop/hadoopfile```
###-tail
* 使用方法:hadoop fs -tail [-f] URI 
* 功能:将文件的最后一千字节内容显示到 stdout。 -f 选项将在文件增长时输出附加数据。 
* 示例:

```
hadoop fs -tail /hadoop/hadoopfile```
###-chgrp
* 使用方法:hadoop fs -chgrp [-R] GROUP URI [URI ...] 
* 功能:更改文件组的关联。用户必须是文件的所有者，否则是超级用户。	
	-R 将使改变在目录结构下递归进行。* 示例:

```
hadoop fs -chgrp othergroup /hadoop/hadoopfile```

###-chmod* 功能:改变文件的权限。使用-R 将使改变在目录结构下递归进行。 
* 示例:

```
hadoop fs -chmod 666 /hadoop/hadoopfile```
###-chown* 功能:改变文件的拥有者。使用-R 将使改变在目录结构下递归进行。 
* 示例:

```
hadoop fs -chown someuser:somegrp /hadoop/hadoopfile
```
###-copyFromLocal
* 使用方法:hadoop fs -copyFromLocal <localsrc> URI 
* 功能:从本地文件系统中拷贝文件到 hdfs 路径去 
* 示例:

```
hadoop fs -copyFromLocal /root/1.txt /```###-copyToLocal* 功能:从 hdfs 拷贝到本地*示例:

```
hadoop fs -copyToLocal /aaa/jdk.tar.gz

```

###-cp* 功能:从 hdfs 的一个路径拷贝 hdfs 的另一个路径* 示例: hadoop fs -cp /aaa/jdk.tar.gz /bbb/jdk.tar.gz.2###-mv* 功能:在 hdfs 目录中移动文件* 示例:

```
hadoop fs -mv /aaa/jdk.tar.gz /```
###-getmerge* 功能:合并下载多个文件* 示例:比如 hdfs 的目录 /aaa/下有多个文件:log.1, log.2,log.3,... 

```
hadoop fs -getmerge /aaa/log.* ./log.sum```###-rm* 功能:删除指定的文件。只删除非空目录和文件。-r 递归删除。 
* 示例:

```
hadoop fs -rm -r /aaa/bbb/```
###-df* 功能:统计文件系统的可用空间信息 示例:

```
hadoop fs -df -h /```
###-du* 功能:显示目录中所有文件大小，当只指定一个文件时，显示此文件的大小。 
* 示例:

```
hadoop fs -du /user/hadoop/dir1```
###-setrep* 功能:改变一个文件的副本系数。-R 选项用于递归改变目录下所有文件的副本 系数。* 示例:

```
hadoop fs -setrep -w 3 -R /user/hadoop/dir1
```

![](http://p2ehgqigv.bkt.clouddn.com/18-3-14/64372132.jpg)
![](http://p2ehgqigv.bkt.clouddn.com/18-3-14/86963045.jpg)

<!--
create time: 2018-03-14 11:04:09
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

