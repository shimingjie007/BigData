一 、数据倾斜

    数据倾斜就是在做数据计算时，数据的分散度不够，导致大量的数据集中在一台或者几台机器上计算，这些数据的计算速度远远低于平均计算速度，导致整个计算过程过慢。
    1. Hadoop中的数据倾斜：
    在执行MR程序或HQL on MR程序时，在ruduce阶段卡在99.99%，一直99.99%不能结束。 
    Hive的数据倾斜，一般都发生在Sql中Group和On上，而且和数据逻辑绑定比较深。
    2. Spark中的数据倾斜
    大量相同的key被partition到一个分区里
    
二 、数据倾斜产生的原因 

    以Spark和Hive的使用场景为例，触发Shuffle动作的操作很容易造成数据倾斜，比如countdistinct、group by、join等操作。

三、数据倾斜解决思路

    1. 业务逻辑层面
    数据往往和业务是强相关的，业务的场景直接影响到数据的分布。需要优化sql业务逻辑。
    2. 程序层面
    了解sql底层执行顺序，Shuffle优化。
    3. 调参层面
    Hadoop和Spark都自带了很多参数和机制来调节数据倾斜。

四、数据倾斜解决办法

    1. 从数据和业务角度
    有损的方法： 找到异常数据，比如ip为0的数据，过滤掉
    无损的方法： 先做数据预处理，对分布不均匀的数据，单独计算，先对key做一层hash，先将数据打散让它的并行度变大，再汇集
    2. 从计算平台角度
    MR ： 
    - mapjoin方式 
    - count distinct的操作，先转成group，再count 
    - hive.groupby.skewindata=true 
    - left semi jioin的使用 
    - 设置map端输出、中间结果压缩。（不完全是解决数据倾斜的问题，但是减少了IO读写和网络传输，能提高很多效率）
    Spark :
    - mapjoin方式 
    - 设置rdd压缩 
    - 合理设置driver的内存 
    - Spark Sql中的优化和Hive类似，可以参考Hive




