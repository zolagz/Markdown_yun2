# es批量导入概述


首先把数据封装成我们需要的格式

转成我们需要的json格式

通过BulkRequestBuilder,将批量数据添加到request协议栈缓冲区
（调用BulkRequestBuilder对象的add方法添加数据）

然后
执行 get() 就能将数据插入创建的索引库，最后用BulkResponse判断是否插入失败


<!--
create time: 2018-04-23 12:45:40
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->