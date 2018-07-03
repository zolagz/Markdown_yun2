# Scala常用函数


```
    /**
      * Created by Administrator on 2017/1/11.
      *
      */
    object Scalatest {


      def main(args: Array[String]): Unit = {
        println("ceshi")

        MaxMin

        Filter

        DiffIntersectUnionDis

        Map

        Partition

        Reduce

        Sort

        Count

        GroupBy
      }


      case class Book(title : String ,pages : Int)

      //Seq(列表) -> 类比java的数组
      val Books = Seq(
        Book("Future of Scala developer",85),
        Book("Parallel algorithms",240),
        Book("Object Oriented Programming",130),
        Book("Mobile Development",495)
      )

      /**
        * 场景:求最大最小值
        * maxBy 最大值
        * minBy 最小值
        */
      def MaxMin (): Unit ={
        //获取最大的一个
        //Max is :Book(Mobile Development,495)
        println("Max is :" + Books.maxBy(book => book.pages))
        //Max is :Mobile Development
        println("Max is :" + Books.maxBy(book => book.pages).title)
        //Max is :495
        println("Max is :" + Books.maxBy(book => book.pages).pages)
        //获取最小的一个
        //Min is :Book(Future of Scala developer,85)
        println("Min is :" + Books.minBy(book => book.pages))
        //Min is :Future of Scala developer
        println("Min is :" + Books.minBy(book => book.pages).title)
        //Min is :85
        println("Min is :" + Books.minBy(book => book.pages).pages)
      }


      /**
        * 场景: 过滤数据
        * filter 过滤
        */
      def Filter(): Unit ={
        var filbook = Seq[Book]()
        //返回一个集合
        println("over 200 is : ")
        filbook = Books.filter(book => book.pages >= 200)
        //240 495
        for(i <- 0 to filbook.size-1){
          println(filbook(i).pages)
        }
      }

      /**
        * 场景:集合的差交并重功能
        * diff,以前者为主,取前者差集
        * intersect,以前者为主,取两者交集
        * union,两个集合取并集
        * distinct,去重
        */
      def DiffIntersectUnionDis(): Unit ={
        val num1 = Seq(1,2,3,4,5,6)
        val num2 = Seq(4,5,6,7,8,9)

        var Di = Seq[Int]()
        var Inter = Seq[Int]()
        var Un = Seq[Int]()
        //list(1,2,3)
        Di = num1.diff(num2)
        Di.foreach(print)
        println()
        //list(4,5,6)
        Inter = num1.intersect(num2)
        Inter.foreach(print)
        println()
        //list(1,2,3,4,5,6,4,5,6,7,8,9)
        Un = num1.union(num2)
        Un.foreach(print)
        println()
        Un.distinct.foreach(print)
      }

      /**
        * 场景: 对数据处理
        * map函数的逻辑是遍历集合中的元素并对每个元素调用函数,返回函数使用后的值
        */
      def Map(): Unit ={
        val numbers = Seq(1,2,3,4,5,6)
        var number1 = Seq[Int]()
        var Stringadd = Seq[String]()
        number1 = numbers.map(n => n*2)
        println()
        //list(2,4,6,8,10,12)
        number1.foreach(print)
        Stringadd = numbers.map(n => n.toString + "ceshi")
        println()
        //list(1ceshi,2ceshi,3ceshi,4ceshi,5ceshi,6ceshi)
        Stringadd.foreach(print)
      }

      /**
        * 场景:将指定的集合按照自定的规则拆分成两个新的集合
        * partition,分组
        */
      def Partition(): Unit ={
        val numbers = Seq(1,2,3,4,5,6,7,8,9)
        val (list1,list2) = numbers.partition(n => n % 2 == 0)
        println()
        list1.foreach(print)
        println()
        list2.foreach(print)
      }

      /**
        * reduce : 定义一个变化f, f把两个列表的元素合成一个,遍历列表,最终把列表合并单一元素
        */
      def Reduce(): Unit ={
        val nums =List(1,2,3)
        //6
        val sum1 = nums.reduce((a,b) => a+b)
        //6
        val sum2 = nums.reduce(_+_)
        //6
        val sum3 = nums.sum
      }

      /**
        *sortBy :按照应用函数f之后产生的元素进行自然排序
        *sortWith :使用自定义的比较函数排序
        *sorted :按照元素自身排序
        *PS: 作用于list
        */
      case class User(name : String,mark : Int)
      def Sort(): Unit ={
        val nums =List(1,3,2,4)
        val sorted = nums.sorted
        //List(1,2,3,4)
        println()
        sorted.foreach(print)

        val user = List (("Homeway",25),("XSDYM",23),("ceshi",28))
        //case 使用自定类
        val sortedByAge = user.sortBy(User => User._2)
        //(XSDYM,23)(Homeway,25)(ceshi,28)
        sortedByAge.foreach(print)
        println()
        //case Scala的模式匹配(匹配List)
        val sortedWith = user.sortWith( {case (user1,user2) => user1._2 > user2._2})
        //(ceshi,28)(Homeway,25)(XSDYM,23)
        sortedWith.foreach(print)
      }

      /**
        * Count: 按照一定f函数统计所需数据
        */
      def Count(): Unit ={
        val nums = List(-1,-2,0,1,2)
        val plusCnt1  = nums.count( _ >= 0)
        println(plusCnt1)
        val plusCnt2  = nums.filter( _ > 0).length
        println(plusCnt2)
      }

      /**
        * groupBy : 将列表进行分组,分组的依据是应用f在元素上后产生的新元素
        * grouped : 按列表按照固定的grouped(4)大小进行分组 4个元素一组
        */
      def GroupBy(): Unit ={
        val data = List(("jie","male"),("jian","female"),("xian","male"),("jie1","male"),("jian1","female"),("xian1","male"))
        val group1 = data.groupBy(_._2)
        //map ("male"->list(("jie","male"),("xian","male")),"femail"->list("jian","femail"))
        val groupMap = data.groupBy({case (name,sex)=>sex})
        println()
        //Map(male -> List((jie,male), (xian,male), (jie1,male), (xian1,male)), female -> List((jian,female), (jian1,female)))
        println(groupMap.toString)

        val fixSizeGroupMap = data.grouped(4)
        println()
        //List((jie,male), (jian,female), (xian,male), (jie1,male))
        //List((jian1,female), (xian1,male))
        fixSizeGroupMap.foreach(println)
      }
}

```

<!--
create time: 2018-03-26 15:42:54
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

