# Spark: The Definitive Guide

start at page 12 at 2022-06-13 17:08:32

start at at page 20 at 2022-06-13 20:59:36 

## chapter 2 A Gentle Introduction to Spark

:question:Questions:
1. $\S 8$ Page33. 
   ```Python
   sqlWay = spark.sql(""" 
   SELECT DEST_COUNTRY_NAME, count(1) 
   FROM flight_data_2015
   GROUP BY DEST_COUNTRY_NAME """)

   dataFrameWay = flightData2015\
   .groupBy("DEST_COUNTRY_NAME")\ 
   .count()

   sqlWay.explain()
   dataFrameWay.explain()
   ```
   
   ```
   >>> sqlWay.explain()
    == Physical Plan ==
    AdaptiveSparkPlan isFinalPlan=false
    +- HashAggregate(keys=[DEST_COUNTRY_NAME#17], functions=[count(1)])
       +- Exchange hashpartitioning(DEST_COUNTRY_NAME#17, 200), ENSURE_REQUIREMENTS, [id=#33]
          +- HashAggregate(keys=[DEST_COUNTRY_NAME#17], functions=[partial_count(1)])
             +- FileScan csv [DEST_COUNTRY_NAME#17] Batched: false, DataFilters: [], Format: CSV, Location: InMemoryFileIndex(1 paths)[file:/home/hin/project/src/github.com/HinsonM/spark-book/data/flight-d..., PartitionFilters: [], PushedFilters: [], ReadSchema: struct<DEST_COUNTRY_NAME:string>
   

   >>> dataFrameWay.explain()
    == Physical Plan ==
    AdaptiveSparkPlan isFinalPlan=false
    +- HashAggregate(keys=[DEST_COUNTRY_NAME#17], functions=[count(1)])
       +- Exchange hashpartitioning(DEST_COUNTRY_NAME#17, 200), ENSURE_REQUIREMENTS, [id=#46]
          +- HashAggregate(keys=[DEST_COUNTRY_NAME#17], functions=[partial_count(1)])
             +- FileScan csv [DEST_COUNTRY_NAME#17] Batched: false, DataFilters: [], Format: CSV, Location: InMemoryFileIndex(1 paths)[file:/home/hin/project/src/github.com/HinsonM/spark-book/data/flight-d..., PartitionFilters: [], PushedFilters: [], ReadSchema: struct<DEST_COUNTRY_NAME:string>
   ```

## chapter 3 A Tour of Sparks Toolset

## Part 2 chapter 4 Structured API Overview

start at page 55 at 2022-06-14 20:17:03

start at page 65 at 2022-06-15 10:00:57 

resume at page 74 at 2022-06-15 14:47:19

易混点概念：
1. DataFrame, Datasets: DataFrame untyped(check during runtime); Datasets check
   during compile-time. Datasets are only available to Java Virtual Machine (JVM)
   – based languages (Scala and Java) and we specify types with case classes or
   Java beans. Mostpart, use Dataframe, highly efficient in-merory format for
   computation.

:question:Questions:
1. $\S 8$ 

## Part 2 chapter 5 Structured API Overview

:question:Questions:
1. $\S 5.1$ Schemas. Page 67. `StructType()` 中 `List` 中的倒数第二个参数是什么？
   ```python
   myManualSchema = StructType([StructField("DEST_COUNTRY_NAME", StringType(), True),
   StructField("ORIGIN_COUNTRY_NAME", StringType(), True),
   StructField("count", LongType(), False, metadata={"hello":"world"})
   ]) 

   df = spark.read.format("json").schema(myManualSchema)\
   .load("/data/flight-data/json/2015-summary.json")
   ```

## Part 2 chapter 6 . Working with Different Types of Data

start at page 89 at 2022-06-15 16:15:04

resume at page 108 at 2022-06-15 19:05:31

:question:Questions:
1. $\S 6.5$ Working With Strings. Page 102. pyspark 支持 `varargs` 特性以及下面
   的例子，没看懂。

## Part 2 chapter 7 . Working with Different Types of Data

start at page 125 at 2022-06-15 20:29:43

:question:Questions:
1. $\S 7.1.7$ Page 136 下述 `Window.unboundedPreceding` 和 `Window.currentRow`
   分别代表 `Window.unboundedPreceding = -9223372036854775808` 和
   `Window.currentRow = 0`的意思，具体表现为？
   ```python
    windowSpec = Window\
   .partitionBy("CustomerId", "date")\
   .orderBy(desc("Quantity"))\
   .rowsBetween(Window.unboundedPreceding, Window.currentRow)
   ```
2. $\S 7.3$ Window Functions. 看不懂
   ```python
   from pyspark.sql.window import Window
   from pyspark.sql.functions import desc
   windowSpec = Window\
     .partitionBy("CustomerId", "date")\
     .orderBy(desc("Quantity"))\
     .rowsBetween(Window.unboundedPreceding, Window.currentRow)
   
   # COMMAND ----------
   
   from pyspark.sql.functions import max
   maxPurchaseQuantity = max(col("Quantity")).over(windowSpec)
   
   # COMMAND ----------
   
   from pyspark.sql.functions import dense_rank, rank
   purchaseDenseRank = dense_rank().over(windowSpec)
   purchaseRank = rank().over(windowSpec)
   
   # COMMAND ----------
   
   from pyspark.sql.functions import col
   
   dfWithDate.where("CustomerId IS NOT NULL").orderBy("CustomerId")\
     .select(
       col("CustomerId"),
       col("date"),
       col("Quantity"),
       purchaseRank.alias("quantityRank"),
       purchaseDenseRank.alias("quantityDenseRank"),
       maxPurchaseQuantity.alias("maxPurchaseQuantity")).show()
   
   ```
   
   ```sql
   SELECT CustomerId, date, Quantity,
   rank(Quantity) OVER (PARTITION BY CustomerId, date
                        ORDER BY Quantity DESC NULLS LAST
                        ROWS BETWEEN
                          UNBOUNDED PRECEDING AND
                          CURRENT ROW) as rank,

   dense_rank(Quantity) OVER (PARTITION BY CustomerId, date
                              ORDER BY Quantity DESC NULLS LAST
                              ROWS BETWEEN
                                UNBOUNDED PRECEDING AND
                                CURRENT ROW) as dRank,

   max(Quantity) OVER (PARTITION BY CustomerId, date
                       ORDER BY Quantity DESC NULLS LAST
                       ROWS BETWEEN
                         UNBOUNDED PRECEDING AND
                         CURRENT ROW) as maxPurchase
   FROM dfWithDate WHERE CustomerId IS NOT NULL ORDER BY CustomerId
   ```

3. grouping sets 概念参考 [GROUPING SETS ](https://tech.antfin.com/docs/2/92807)

## Part 2 chapter 8 . Joins

start at page 145 2022-06-15 23:13:23

resume at page 145 2022-06-15 23:42:44

:question:Questions:
1. $\S 7.1.7$ Page 136 下述 `Window.unboundedPreceding` 和 `Window.currentRow`

## Part 2 chapter 10 . Spark SQL

start at page 192 2022-06-16 10:06:30

草草略过

:question:Questions:
1. $\S 10.5$ Page 193 下述 
   ```sql
   CREATE TABLE flights_csv (
     DEST_COUNTRY_NAME STRING,
     ORIGIN_COUNTRY_NAME STRING COMMENT "remember, the US will be most prevalent",
     count LONG)
   USING csv OPTIONS (header true, path '/data/flight-data/csv/2015-summary.csv')
   ```
## Part 2 chapter 11 . Datasets

start at page 192 2022-06-16 10:22:40

草草略过

:question:Questions:

## Part 3 chapter 12 . RDDS （略）

start at page 214 at 2022-06-16 12:55:36
resume at page 216 2022-06-16 14:04:14

草草略过

:question:Questions:

1. $\S 12.3 $ Creating RDDS. Page 218. 看不懂下面的代码片段所表达的意思。To
   operate on this data, you will need to convert this Row object to the correct
   data type or extract values out of it, as shown in the example that
   follows. This is now an RDD of type Row:

   ```scala 
   // in Scala 
   spark.range(10).toDF().rdd.map(rowObject => rowObject.getLong(0))
   ```

   ```python
   # in Python
   spark.range(10).toDF("id").rdd.map(lambda row: row[0])
   ```
   
## Part 4 Production Application Chapter 15 How Spark Runs on a Cluster
 
start at page 296 at 2022-06-17 00:47:21

start at page 253 at 2022-06-16 15:19:27

start at page 253 at 2022-06-16 15:32:43

:question:Questions:
1. 

### scala 学习

:question:Questions:
1. 下面的函数定义中的参数及返回值类型的生命不太懂：
   ```scala
   scala> def addOne(m: Int): Int = m + 1
   addOne: (m: Int)Int
   ```

## Chapter 17. Deploying Spark 略过

:question:Questions:
1. 

## Chapter 18. Monitoring and Debugging

start at page 297 at 2022-06-17 10:14:43

resume at page 300 at 2022-06-17 10:33:37

resume at page 309 at 2022-06-17 14:50:59

$\S 18.5$ Debugging and Spark First Aid. 常见问题及 Debug（待）
- Spark not starting;
- Errors During Execution
- Slow Tasks or Stragglers
- Slow Aggregations
- Slow Joins
- Slow Reads and Writes
- Driver OutOfMemoryError or Driver Unresponsive（待）
- Executor OutOfMemoryError or Executor Unresponsive（待）
- Unexpected Nulls in Results（待）
- No Space Left on Disk Errors（待）
- Serialization Errors（待）

:question:Questions:
1. $\S 18.4$ Page 302 因为最后的 transformation 和 action 是
   `.groupBy("is_glass").count().collect()` 所以基于 优化策略，spark 在
   `.selectExpr("instr(Description, 'GLASS') >= 1 as is_glass")` 之前先进行
   HashAggregate output 6 rows? 因为`selectExpr("instr(Description, 'GLASS') >=
   1 as is_glass") ` 产生 `is_glass` 的三个值，`None, True, False`, 而在
   `select` 之前，有 `repartition(2)` 产生两个 partition, 因此有 `2*3=6` 个
   output rows

   > The next step is our projection (selecting/adding/filtering columns)
   and the aggregation. Notice that in Figure 18-5 the number of output rows is
   six. This convienently lines up with the number of output rows multiplied by
   the number of partitions at aggregation time. This is because Spark performs
   an aggregation for each partition (in this case a hash-based aggregation)
   before shuffling the data around in preparation for the final stage.
   
   ```sql
   spark.read\
   .option("header", "true")\
   .csv("./data/retail-data/all/online-retail-dataset.csv")\
   .repartition(2)\
   .selectExpr("instr(Description, 'GLASS') >= 1 as is_glass")\
   .groupBy("is_glass")\
   .count()\
   .collect()
   ```
   
   ```log
   HashAggregate
   spill size: 0.0 B
   time in aggregation build total (min, med, max (stageId: taskId))
   627 ms (313 ms, 314 ms, 314 ms (stage 3.0: task 10))
   peak memory total (min, med, max (stageId: taskId))
   4.5 MiB (2.3 MiB, 2.3 MiB, 2.3 MiB (stage 3.0: task 10))
   number of output rows: 6
   number of sort fallback tasks: 0
   avg hash probe bucket list iters (min, med, max (stageId: taskId)):
   (1, 1, 1 (stage 3.0: task 10))
   ```
1. $\S 18.5$ Debugging and Spark First Aid. Slow Reads and Writes. Page 312. One
   solution to slow tasks is back-up trick (by turning on speculation, fire
   another duplicate task on the same data described in MapReduce paper). But
   there is one confusing point: 
   > it can cause duplicate data writes with some eventually consistent cloud
   > services
2. History Server UI `spark.eventLog.enabled` and the event log location with the configuration `spark.eventLog.dir`

## Chapter 19. Performance Tuning（草草看过）

start at  page 317 at 2022-06-17 16:02:17

:question:Questions:
1. 

## Part 5. Chapter 20. Stream Processing Fundamentals（待）

## Chapter 30. Graph Analytics（待）

## New Words

1. **momentarily**: $\mathbb{D}.$ for a very short time 片刻地，短暂地：
   $\mathbb{E}.$ as he passed Jenny's door, he paused momentarily. 他走过詹妮门
   前时迟疑了片刻。$\mathbb{D}.$ N. Amer. at any moment; very soon〈北美〉随时；
   立即，即刻：$\mathbb{E}.$ my husband will be here to pick me up
   momentarily. 我丈夫马上就会来接我。
1. **groundbreaking**: $\mathbb{D}.$ making new discoveries; using new methods
   开创性的；创新的；革新的 $\mathbb{E}.$ a groundbreaking piece of research 富
   有开拓性的一项研究
1. **arcane**: $\mathbb{D}.$ secret and mysterious and therefore difficult to
   understand 神秘的；晦涩难懂的 $\mathbb{E}.$ the arcane rules of cricket 鲜为
   人知的板球规则
1. **Penultimately**: $\mathbb{D}.$ immediately before the last one 倒数第二的
   $\mathbb{E}.$ the penultimate chapter/day/stage 倒数第二章／天／阶段
1. **whirlwind**: $\mathbb{D}.$ n. 旋风；旋风般的快速行动 adj. 旋风般的
1. **belabour**: $\mathbb{D}.$ attack or assault (someone) physically or
   verbally 攻击，袭击；抨击，责骂：$\mathbb{E}.$ Bernard was belabouring Jed
   with his fists. 伯纳德挥拳痛打杰德。$\mathbb{D}.$ argue or elaborate (a
   subject) in excessive detail 对…争论（或说明）过多；纠缠于：$\mathbb{E}.$
   there is no need to belabour the point. 没有必要对这一点多费口舌。
1. **coerce**: $\mathbb{D}.$ persuade (an unwilling person) to do something by
   using force or threats 强迫（或胁迫）（某人）做（某事）: $\mathbb{E}.$ he was
   coerced into giving evidence. 他被强迫作证。$\mathbb{D}.$ obtain (something)
   by such means 用强迫手段获得：$\mathbb{E}.$ their confessions were allegedly
   coerced by torture. 据称他们是被屈打成招的。
1. **behoove**: $\mathbb{D}.$ ERBwith obj.(it behoves someone to do something) 1
   formal it is a duty or responsibility for someone to do something; it is
   incumbent on〈正式〉某人做某事是其职责所在（或义不容辞的责任）: $\mathbb{E}.$
   it behoves the House to assure itself that there is no conceivable
   alternative. 议会有责任搞清楚没有任何可以想象得到的替代办法。$\mathbb{D}.$
   with negative it is appropriate or suitable; it befits 适合：$\mathbb{E}.$ it
   ill behoves Opposition Members constantly decry the sale of arms to friendly
    countries. 反对党议员们经常批评向友邦出售武器是不合适的。
1. **on-premises**: $\mathbb{D}.$ [网络] 内部部署；户内；预先定制
1. **synopsis**: $\mathbb{D}.$ a brief summary or general survey of something 提
   要；概要：$\mathbb{E}.$ a synopsis of the insurance cover provided is set out
   below. 所提供的保险范围的概要有如下述。
1. **Band-Aid**: $\mathbb{D}.$〈喻〉权宜之计，临时解决方法：$\mathbb{E}.$ a
   band-aid solution to a much deeper problem. 应付一个远为深重问题的权宜之计。

1.
**word**:
$\mathbb{D}.$
$\mathbb{E}.$
$\mathbb{SYN}.$
1.
**word**:
$\mathbb{D}.$
$\mathbb{E}.$
$\mathbb{SYN}.$
1.
**word**:
$\mathbb{D}.$
$\mathbb{E}.$
$\mathbb{SYN}.$
1.
**word**:
$\mathbb{D}.$
$\mathbb{E}.$
$\mathbb{SYN}.$
1.
**word**:
$\mathbb{D}.$
$\mathbb{E}.$
$\mathbb{SYN}.$
1.
**word**:
$\mathbb{D}.$
$\mathbb{E}.$
$\mathbb{SYN}.$