# Hive内置函数


###数学函数


round  四舍五入


select round(45.946 ,2)

round(参数1，参数2)
参数1 ，对谁四舍五入，
参数2，保留小数点后面的几位  

ceil 向上取整

select ceil(45.9)



floor 向下取整



字符函数：
* lower  转小写
* upper   转大写
* length  求长度
* concat  拼加一个字符串
	 
	 select concat('Hello',' word');
	 
* substr  求一个字符串的子串

	substr(a,b)  从a中，第b位开始取，取右边所有的字符串
	select substr('hello world',3)
	
	substr(a,b,c)  从a中，第b位开始取，取c个字符
	
* trim  去掉字符串前后的空格
* lpad  左填充
  
  select lpad（参数1 ，参数2 ，参数3）
  
  参数1：对谁填充
  参数2：从第几位开始填充
  参数：要填充的内容
  
  select lpad('abcd',10,'*'),rpad('abc',10,'&');

* rpad  右填充 



Hive收集函数和转换函数

* 收集函数 -- size
	
	size(map(<key,value>,<key,value>))
	
	select size(map(1,'tom',2,'marry'));

* 转换函数 -- cast

 ![](http://p2ehgqigv.bkt.clouddn.com/18-3-19/20212491.jpg)
 
 ###hive 日期函数
 
 * to_date
 
 	select to_date('2015-04-23 11:23:15'); 
 
 * year
 		
 		select year('2015-04-23 11:23:15'); 
 		
 * month
 
 		select month('2015-04-23 11:23:15'); 
 * day
 
 		select day('2015-04-23 11:23:15'); 
 		
 * weekofyear

		select weekofyear('2015-04-23 11:23:15'); 
		
 * datediff

		 select datediff('2015-04-23 11:23:15','2015-06-23 11:23:15'); 
		 
 * date_add

		 select date_add('2015-04-23 11:23:15',2); 
		 
 * date_sub

		 select date_sub('2015-04-23 11:23:15',2); 
 

###条件函数
* coalesce:从左到友返回第一个不为null的值

* case...when...:条件表达式

CASE a WHEN b THEN c [WHEN d THEN e] * [ELSE f] END


<!--
create time: 2018-03-06 21:39:13
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

