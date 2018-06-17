# Spark读取文件

spark默认读取的是hdfs上的文件。

如果读取本地文件，则需要加file:///usr/local/spark/README.md。 (测试时候发现，本地文件必须在spark的安装路径内部或者平行)

读取hdfs文件, 可以这样指定路径 hdfs://ns1/tmp/test.txt。

如果不指定任何前缀，则使用hdfs的默认路径/user/data/











<!--
create time: 2018-06-14 20:57:40
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

