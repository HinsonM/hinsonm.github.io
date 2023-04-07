# 1. Streaming System

## 简评（来自知乎）

[streaming-system-review]: https://zhuanlan.zhihu.com/p/43301661

[streaming system 简评][streaming-system-review]

当我利用书中的思维体系来分析我所遇到的分布式系统，我第一次感觉到流式系统，甚至分
布式业务系统，其实是如此的简单，作者用非常流畅的思路解释了流处理中的各种概念，

这本书绝不仅仅是在讲流式处理，它的真名是"总结过去 10 年大数据处理发展的历史，Google 的经验；提出新的理论，升维我们的思维模式，降维打击我们的复杂问题；展现目前的行业趋势，预测未来的行业走势…."，这本书几乎影响了我对所有分布式系统的理解。

然而！ 分布式系统的搭建还是太难了（需要大量的知识积累和设计实现精力）。我们需
要新的抽象，基础服务来隔离这些复杂度，培养大量可以基于"这些新基础"（而不必理解这
些新基础的实现细节）就可以实现复杂分布式业务系统的 SDE，让 SDE resource 使用在真正
能够提供业务价值的 application 上（比如科学模型的建立），而不是一遍遍去用相同的手段
去处理这些繁琐的细节。我们需要更高一层的 Programming model，基础问题需要从关注使
用多少内存，使用多少硬盘，怎么做网络通信这些更底层的细节。升级到考虑什么时候使用
sync，什么时候必须要注意 failure tolerance，liveness 和 safety 怎么做 trade-off，什么
时候使用 state，什么时候使用 CRDT，什么时候需要幂等，怎么做 partition 如何设计
partitionkey，思考云环境和分布式环境的“新基础思考因素”。

多衍生数据一致性
- 基于 XA(eXtended Architecture), 2PC 的，不能 failure tolerance 的强 consistency
- 或者 no consistency at all, 容忍多种的数据可能不一样。

**试验！还是试验**

一个 bug 毁一个公司的成为越来越逼真的故事，数据的 corruption 成为企业无法容忍的错误，
这使得 human fault tolerance（即软件 bug 造成数据的永久损伤）成为系统设计需要考虑的重
中之重。

形式化验证的高 cost 使得这项技术迟迟不能在工业界得到大量应用，最终导致了
Replayable 构架的流行，即：用最简单和形式化验证过核心算法的 framework/通用基础云
设施等来收集原始数据，让只是凭借简陋的 testing 验证过的业务逻辑代码可以任意重复
利用原始数据 rerun，这样任何时候发现 bug 都可以通过重跑系统来重写派生的业务数据。

**Replayable 构架的能量**不仅仅是 **human fault tolerance**，如果我们拓展"什么是
错误"的定义，那么当第二版科学模型的效果比第一版好，那么第一版就可以看作是“bug”。
所以很“自然”的，一个可以 fix&rerun 的 replayable 构架系统也必然可以同时跑多版本
来做多版本实验。商业需求的爆炸造就了商业决策的爆炸，自动化的决策执行使得决策的影
响越来越大，各个公司决策的竞争，不再只是依赖某大佬的点子和经验，而是需要试验并拿
出数据支持自己的想法。Data Driven, Metrics Driven Decision Making，是现代高科技
公司决策层高管的生命线；一个能用更小的 cost 来同时试验多个 idea，得到试验数据来
验证想法的公司，打败竞争对手的几率会大大增加。这使得用不同计算模型，replay 相同
的数据来生成不同的结果成为系统设计的另外一个必须考虑的因素。而能够支撑
replayable 的数据框架将必然在新时代大放异彩。

**新的时代和它给的解**

多数据存储之间的 consistency 的问题，本质上与分布式数据库的 replication 问题一致，而
分布式数据库的 replica 可以通过 total order broadcast，来广播主节点的 WriteAheadLog
或者 changelog 到 replica 节点，由于 total order broadcast 保证了所有 replica 节点收到的
message 是绝对相同的顺序，那么只要 replica 节点按照受到 message 的顺序作出对数据相同
的更改，我们就实现了分布式数据库对数据的 replication，而 WriteAheadLog 或 changelog
本质就是 data stream；如果我们把数据库的 total order broadcast 的这项技术应用到分布
式业务系统的设计中去，那么我们就可以解决分布式系统中最难点一个问题：消息时序，而
kafka 可以说为分布式数据 (change) 提供了一个 logic clock 和 total order，任何数据生产
者只需要把数据输入 Kafka，任何 Kafka 的 consumer 都保证会以相同的顺序接收同一个
partition 上的 message，同时利用 Kafka 的 replayable 特性，数据可以写一次，而重新以任
意形式 apply 在其他存储形式上 (cache, elastic search, datawarehouse, keyvalue DB 等）。
这样，我们就解决了多存储的一致性问题。 

如此循环，我们就得到了一个以 Kafka 的 message index 为 logical clock 的全局
eventually consistent 的分布式流处理系统。

## 1.1. 阅读心得

## 1.2. New Words

1. **pervade**: $\mathbb{D}.$ (especially of a smell) spread through and be
   perceived in every part of（尤指气味）弥漫于，渗透于：$\mathbb{E}.$ a smell
   of stale cabbage pervaded the air. 腐烂卷心菜的臭味弥漫在空气中。$\mathbb{D}
   .$ (of an influence, feeling, or quality) be present and apparent throughout
   （影响，感情，品质）遍及于，充满：$\mathbb{E}.$ the sense of crisis which
   pervaded Europe in the 1930s.20 世纪 30 年代遍布欧洲的危机感。$\mathbb{E}.$
   Streaming data is a big deal in big data these days. As more and more
   businesses seek to tame the massive unbounded data sets that pervade our
   world, streaming systems have finally reached a level of maturity sufficient
   for mainstream adoption. 
1. **agnostic**: $\mathbb{D}.$ (religion 宗教） holding or showing the belief
   that it is not possible to know whether God exists or not 不可知论的
   $\mathbb{E}.$ With this practical guide, data engineers, data scientists, and
   developers will learn how to work with streaming data in a conceptual and
   platform-agnostic way. $\mathbb{D}.$ (computing 计算机） (often in compounds
   常构成复合词） (used about computer hardware or software) able to be used
   with many different types of computer systems, software or operating systems
   （计算机硬件或软件）兼容…的，不限…的 $\mathbb{E}.$ platform-agnostic =>
   platform-unaware => cross-platform: Now that the services are
   platform-agnostic, they can be accessed by far more users. 由于这些服务不限平
   台，因此更多的用户可以使用了。
1. **majestic**: $\mathbb{D}.$ having or showing impressive beauty or dignity 雄
   伟的，壮丽的；庄严的，庄重的：$\mathbb{E}.$ the majestic castle walls. 宏伟的
   城堡外墙。
1. **trout**: $\mathbb{D}.$ a chiefly freshwater fish of the salmon family,
   found in both Eurasia and North America and highly valued for food and game
   鲑，鲑鳟鱼。
1. **interlude**: $\mathbb{D}.$ an intervening period of time; an interval 间歇，
   空闲；间隙，空隙：$\mathbb{E}.$ enjoying a lunchtime interlude. 享受午餐时的一
   段空闲。$\mathbb{D}.$ a pause between the acts of a play （戏剧的）幕间暂停，幕
   间休息。$\mathbb{D}.$ a thing occurring or done during an interval 插曲，间歇
   时发生（或插入）的事情。
1. **timely**: $\mathbb{D}.$ done or occurring at a favourable or useful time;
   opportune 及时的，适时的：$\mathbb{E}.$ a timely warning. 及时警告。
1. **therein**: $\mathbb{D}.$〈古或正式〉在那里；在那点上；在其中。☆In that
   place, document, or respect. $\mathbb{E}.$ it shall be sufficient evidence of
   the facts therein contained

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