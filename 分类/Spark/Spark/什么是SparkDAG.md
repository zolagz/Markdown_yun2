# 什么是SparkDAG
[来源](https://blog.csdn.net/sinat_31726559/article/details/51738155)
###概念
在spark里每一个操作生成一个RDD，RDD之间连一条边，最后这些RDD和他们之间的边组成一个有向无环图，这个就是DAG。

###在spark的应用

Spark内核会在需要计算发生的时刻绘制一张关于计算路径的有向无环图，也就是DAG。

有了计算的DAG图，Spark内核下一步的任务就是根据DAG图将计算划分成任务集，也就是Stage，这样可以将任务提交到计算节点进行真正的计算。Spark计算的中间结果默认是保存在内存中的，Spark在划分Stage的时候会充分考虑在分布式计算中可流水线计算（pipeline）的部分来提高计算的效率，而在这个过程中，主要的根据就是RDD的依赖类型。

根据不同的transformation操作，RDD的依赖可以分为窄依赖（Narrow Dependency）和宽依赖（Wide Dependency，在代码中为ShuffleDependency）两种类型。窄依赖指的是生成的RDD中每个partition只依赖于父RDD(s) 固定的partition。宽依赖指的是生成的RDD的每一个partition都依赖于父 RDD(s) 所有partition。窄依赖典型的操作有map, filter, union等，宽依赖典型的操作有groupByKey, sortByKey等。可以看到，宽依赖往往意味着shuffle操作，这也是Spark划分stage的主要边界。对于窄依赖，Spark会将其尽量划分在同一个stage中，因为它们可以进行流水线计算。

至于以上一些shuffer过程建议大家多看看spark官网会比较好点


<!--
create time: 2018-04-23 23:21:53
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

