# 1. Hadoop: The Definitive Guide

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [1. Hadoop: The Definitive Guide](#1-hadoop-the-definitive-guide)
  - [1.1. Progress](#11-progress)
  - [1.2. 实验记录](#12-实验记录)
  - [1.3. HDFS](#13-hdfs)
  - [1.4. chapter 6 Developing a MapReduce Application](#14-chapter-6-developing-a-mapreduce-application)
  - [chapter 7 how mr works](#chapter-7-how-mr-works)
  - [1.5. Chapter 10 Setting Up a Hadoop Cluster](#15-chapter-10-setting-up-a-hadoop-cluster)
  - [MR Types and Foramt](#mr-types-and-foramt)
  - [ch08](#ch08)
  - [cha09 mr feature](#cha09-mr-feature)
  - [ch22-ch24 Case Studies](#ch22-ch24-case-studies)
  - [1.6. New Words](#16-new-words)

<!-- /code_chunk_output -->

leave at 2022-02-11 16:52:20

start at 2022-02-12 21:25:36 P~50~

start at 2022-05-08 11:12:25

start at 2022-05-08 15:46:16

ch06: start at 2022-06-10 16:19:05

cha08: start at 2022-06-11 17:01:21

## 1.1. Progress

start at 2022-06-11 17:16:25 Page~58~

## 1.2. 实验记录

(1) while running `start-dfs.sh`, we may encounter an error `Error: JAVA_HOME is not set and could not be found. `
    
Solutions: You can edit `/home/bae/opt/hadoop-3.3.1/etc/hadoop/hadoop-env.sh`
file and set JAVA_HOME for Hadoop. Open the file and find the line as bellow
```
export JAVA_HOME=
```
Uncomment the line And update the java_home as per your environment

## 1.3. HDFS

:question: Questions:
1. $\S 3.2.5$ Hdfs High Availibility. method about replication and consistency,
   failover

## 1.4. chapter 6 Developing a MapReduce Application

:question: Questions:
1. how to run a junit unit test using CLI. through IDE such as Intelliji, or
   Vscode.
2. `start-dfs.sh` 启动后，`jps -l|ag namenode` 没有 namenode, 原因是格式化好的
   hdfs 文件系统路径在 /tmp，因为某种原因被清除了，所以需要设置固定的目录，在重
   新 `hadoop namenode --format && start-dfs.sh` 即可
3. 在初学阶段，面对日志聚合这样的问题时，不熟悉配置文件，有两个思路：看官方博客
   或者教程；看网上博客或者教程；逐个看 `yarn-site.xml` 研究解决方案。第三种可能
   是最笨的了，看完就忘了。所以没啥意义，所以应该是在实践中基于经验与推导，摸
   着石头过河，能有博客最好。[Hadoop 日志纪录篇](https:
   //www.cnblogs.com/yinzhengjie/p/13929630.html)

```
<property>
    <name>hadoop.tmp.dir</name>
    <value>/home/hdp/hadoop_tmp</value>
    <description>A base for other temporary directories.</description>
</property>
```

## chapter 7 how mr works

start at 2022-06-11 11:07:47 on page 188
stop at 2022-06-11 19:12:09 on page 216

:question: Questions:
1. `reduce slow start` “Reduce slow start” on page 308.

## 1.5. Chapter 10 Setting Up a Hadoop Cluster

CHAPTER 10 Setting Up a Hadoop Cluster

:question:Questions:
1. $\S 10$ Setting Up a Hadoop Cluster.Other Hadoop Properties. Reduce slow
   start P~308~, `mapreduce.job.reduce.slowstart.completedmaps`, 不应该 任意 reduce task 开始之前必须 等待所有 map tasks 结束之后吗？

Easily Confused Points:
1. Authentication and Authorization: 身份认证和鉴权
    - authentication is the process of verifying who someone is, 
    - authorization is the process of verifying what specific applications,
      files, and data a user has access to.
      
## MR Types and Foramt

start at 2022-06-12 12:30:07 at page 232

:question:Questions:
1. $\S 8$ MapReduce Types. The Default MapReduce Job. $\S 6$ 中的代码样例好像并
   没有 SetInputFormat, 并且文中提到因为 java 的一些缺点，type check 并不会发生
   在 compile time, 而是 run time.
2. split size 由谁决定？ 1. `minimumSize < blockSize < maximumSize`
   2. `totFileSize/mapContainerCnt`
3. 如下述代码所示， 
   下面这一行
   ```java
   job.setOutputFormatClass(SequenceFileOutputFormat.class);
   ```
   和这两行是不是重复了？
   ```java
   job.setOutputKeyClass(Text.class);
   job.setOutputValueClass(BytesWritable.class);
   ```

   ```java
   public class SmallFilesToSequenceFileConverter extends Configured
       implements Tool {

     @Override
     public int run(String[] args) throws Exception {
       Job job = JobBuilder.parseInputAndOutput(this, getConf(), args);
       if (job == null) {
         return -1;
       }
       
       job.setInputFormatClass(WholeFileInputFormat.class);
       job.setOutputFormatClass(SequenceFileOutputFormat.class);
       
       job.setOutputKeyClass(Text.class);
       job.setOutputValueClass(BytesWritable.class);

       job.setMapperClass(SequenceFileMapper.class);

       return job.waitForCompletion(true) ? 0 : 1;
     }
     
     public static void main(String[] args) throws Exception {
       int exitCode = ToolRunner.run(new SmallFilesToSequenceFileConverter(), args);
       System.exit(exitCode);
     }

   ```
   
## ch08

1. `SequenceFileInputFormat` 怎么使用
2. what is the output of map-only job, say `Example 9-3.`

## cha09 mr feature

start at 2022-06-12 13:08:24 at 247

resume at 2022-06-12 14:44:27 at page 256

## ch22-ch24 Case Studies

## 1.6. New Words

1. **hitherto**: $\mathbb{D}.$ [adverb] until now; until the particular time you
   are talking about 迄今；直到某时 $\mathbb{E}.$ a hitherto unknown species of
   moth 迄今仍属未知品种的蛾 Her life hitherto had been devoid of adventure. 迄
   今为止，她的生活没有冒险
1. **dispense with**: $\mathbb{D}.$ manage without; get rid of 没有也行；去掉：
   $\mathbb{E}.$ let's dispense with the formalities, hall we? 我们不要拘泥于形
   式，好吗？
1. **democratize**: $\mathbb{D}.$ introduce a democratic system or democratic
   principles to 向…引入民主制度（或原则）, 使民主化：$\mathbb{E}.$ public
   institutions need to be democratized. 公共组织应该民主化。$\mathbb{D}.$ make
   (something) accessible to everyone 普及，使大众化：$\mathbb{E}.$ mass
   production has not democratized fashion. 大规模生产还没有普及时尚。
1. **subsume**: $\mathbb{D}.$ include or absorb (something) in something else 把…
   包括在内，包含；把…并入：$\mathbb{E}.$ most of these phenomena can be
   subsumed under two broad categories. 这些现象大部分都可以包括在两大范畴中。
1. **whereby**: $\mathbb{D}.$ by which 靠那个，借以：$\mathbb{E}.$ a system
   whereby people could vote by telephone. 人们可以借助电话投票的系统。
1. **unleash**: $\mathbb{D}.$ release from a leash or restraint 放开；释放：
   $\mathbb{E}.$ they dig up badger setts and unleash terriers into them 他们挖
   开獾洞，放进猎犬 $\mathbb{E}.$ figurative 〈喻〉the failure of the talks
   could unleash more fighting. 会谈失败可能会引发更多战斗。
1. **pertain**: $\mathbb{D}.$ 适合 $\mathbb{E}.$ matters pertaining to the
   organization of government. 与组织政府有关的事。
1. **instrument**: $\mathbb{D}.$ Transitive Verb. ☆Equip (something) with
   measuring instruments. $\mathbb{E}.$ engineers have instrumented rockets to
   study the upper atmosphere $\mathbb{E}.$ a DC-8 aircraft instrumented as a
   flying laboratory $\mathbb{E}.$ For Protocol 4, animals were instrumented
   before delivery to measure pulmonary artery pressure and left atrial
   pressure.
1. **interrogate**: $\mathbb{D}.$ ☆Ask questions of (someone, especially a
   suspect or a prisoner) closely, aggressively, or formally. He plays with the
   wording of questions or suddenly interrogates me about my private
   life. $\mathbb{D}.$ Computing obtain data from (a computer file, database,
   storage device, or terminal)【计算机】（从计算机文档、数据库、存储器或终端内）
   获取信息，询问。$\mathbb{E}.$
1. **time-honored**: $\mathbb{D}.$ respected because it has been used or done
   for a long time 古老而受到尊重的；历史悠久的；由来已久的 $\mathbb{E}.$ They
       showed their approval in the time-honoured way (= by clapping, for
       example). 他们以传统的方式表示同意。$\mathbb{E}.$ The time-honored way of
       debugging programs is via print statements, and this is cer‐ tainly
       possible in Hadoop. 
1. **word**: $\mathbb{D}.$ fully qualified name 全限定名
1. **back-of-the-envelope**: $\mathbb{D}.$ adj. always used before a noun done
   to provide a rough idea of something 粗略估算的 $\mathbb{E}.$ I can't tell
   you the exact amount, but my back-of-the-envelope [=rough] calculation
   indicates a cost of about $300,000. 我无法告知你确切的金额，但我粗略估算的费
   用在 30 万美元左右。
1. **word**: $\mathbb{D}.$ (of a person) devoted to a task or purpose（人）专注
   的，全心全意的，（为…) 献身的：$\mathbb{E}.$ a team of dedicated doctors. 一
   队有献身精神的医生。1.1	(of a thing) exclusively assigned or allocated to or
   intended for a particular service or purpose（某物）专用的：$\mathbb{E}.$ a
   dedicated high-speed rail link from the Channel Tunnel. 与英吉利海峡隧道相通
   的专用高速铁路连接线。
1. **parlance**: $\mathbb{D}.$（尤指某职业或兴趣通用的）说法；用语：dated terms
   that were once in common parlance 曾是通用语的陈词 $\mathbb{E}.$ medical
   parlance. 医学用语。
1. **word**: $\mathbb{D}.$ hoax or trick (someone) 戏弄（某人）; 哄骗：
   $\mathbb{E} .$ they proceeded to spoof Western intelligence with false
   information. 他们接着用假消息来愚弄西方情报部门。
1. **back-of-the-envelope**: $\mathbb{D}.$ done to provide a rough idea of
   something 粗略估算的 $\mathbb{E}.$ The value of doing a back-of-the-envelope
   calculation like this is that it gives you a feel for how big your cluster
   should be
1. **interrogate**: $\mathbb{D}.$ sk questions of (someone, especially a suspect
   or a prisoner) closely, aggressively, or formally 讯问；审问；质问。
   $\mathbb{E}.$ Computing obtain data from (a computer file, database, storage
   device, or terminal)【计算机】（从计算机文档、数据库、存储器或终端内）获取信
   息，询问。
1. **bear in mind**: $\mathbb{D}.$ 记住；铭记不忘；注意
1. **expunge**: $\mathbb{D}.$ erase or remove completely (something unwanted or
   unpleasant) 擦去；抹去；除去；删去；勾销：$\mathbb{E}.$ he could expunge an
   unsatisfactory incident from his memory. 他可以将不满意的事情从记忆中彻底抹去。
1. **parlance**: $\mathbb{D}.$ a particular way of speaking or using words,
   especially a way common to those with a particular job or interest （尤指某职
   业或兴趣通用的）说法；用语：$\mathbb{E}.$ dated terms that were once in
   common parlance 曾是通用语的陈词 $\mathbb{E}.$ medical parlance. 医学用语。
1. **spoof**: $\mathbb{D}.$ /spuːf/ $\mathbb{E}.$ a trick played on someone as a
   joke 戏弄，玩笑。
1. **rogue**: $\mathbb{D}.$ behaving in a different way from other similar
   people or things, often causing damage 行为失常的；暴戾的 $\mathbb{E}.$ a
   rogue gene 变异基因
1. **indispensable**: $\mathbb{D}.$ absolutely necessary 不可或缺的，必需的：
   $\mathbb{E}.$ he made himself indispensable to the parish priest. 他成了对教
   区牧师来说不可或缺的人物。
1. **umbilical**: $\mathbb{D}.$ adj. 脐带的；母系的；中央的 $\mathbb{E}.$ As the
   map or reduce task runs, the child process communicates with its parent appli
   ‐ cation master through the umbilical interface.
1. **albeit**: $\mathbb{D}.$ though 虽然，尽管：$\mathbb{E}.$ he was making
   progress, albeit rather slowly. 他那时在不断进步，虽然十分缓慢。
1. **boilerplate**: $\mathbb{D}.$ standardized pieces of text for use as clauses
   in contracts or as part of a computer program 合同样板；（计算机程序）样板文
   件。
1. **involved**: $\mathbb{D}.$ difficult to comprehend; complicated 难懂的，深奥
   的；复杂的，棘手的：$\mathbb{E}.$ a long, involved conversation. 一次漫长又复
   杂的谈话。
1. **registry**: $\mathbb{D}.$ a place or office where registers or records are
   kept 登记处，注册处，挂号处。an official list or register 登记簿；注册簿。
   $\mathbb{E}.$ Our challenge is to take this data; transform it into a clean,
   integrated representation; and use it to create registries that help patients
   manage specific conditions, measure operational aspects of healthcare, and
   support a variety of analytics, as shown in figure 22-1.
1. **demographics**: $\mathbb{D}.$ statistical data relating to the population
   and particular groups within it 人口统计数据：$\mathbb{E}.$ the demographics
   of book buyers. 购书者人口统计数据。
1. **metamorphosis**: $\mathbb{D}.$ Zoology (in an insect or amphibian) the
   process of transformation from an immature form to an adult form in two or
   more distinct stages【动】（昆虫或两栖动物的）变态。a change of the form or
   nature of a thing or person into a completely different one（人或物的）变形；
   变质：$\mathbb{E}.$ his metamorphosis from presidential candidate to
   talk-show host. 他从总统候选人到访谈节目主持人的大转变。
1. **plummet**: $\mathbb{D}.$ 1.1	no obj. fall or drop straight down at high
   speed 快速落下，陡直落下：$\mathbb{E}.$ a climber was killed when he
   plummeted 300 feet down an icy gully. 一位登山者从 300 英尺高处陡然掉到一个冰
   谷里给摔死了。$\mathbb{D}.$ 1.2	decrease rapidly in value or amount（在价值
   或数量上）骤然下跌，陡然下降：$\mathbb{E}.$ hardware sales plummeted. 硬件销
   售量陡然下降。
1. **deluge**: $\mathbb{D}.$ a severe flood 洪水。$\mathbb{E}.$ the Deluge  the
   biblical Flood (recorded in Genesis 6-8) (《圣经》中的）大洪水 (《创世纪》
   6-8)。$\mathbb{D}.$ a heavy fall of rain 大雨，暴雨：$\mathbb{E}.$ a deluge
   of rain hit the plains. 暴雨肆虐了平原。
1. **compassionate**: $\mathbb{D}.$ feeling or showing sympathy and concern for
   others 感到同情的；有同情心的；深表同情的。$\mathbb{E}.$ allowed him to go
   home on compassionate grounds

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