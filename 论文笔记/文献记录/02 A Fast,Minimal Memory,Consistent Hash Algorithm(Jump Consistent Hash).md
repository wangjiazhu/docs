# A Fast, Minimal Memory, Consistent Hash Algorithm(Jump Consistent Hash)

​														John Lamping, Eric Veach

​																			Google

> ​													约翰·兰平，埃里克·维奇
> ​																谷歌

## **Abstract**

​		We present jump consistent hash, a fast, minimal memory, consistent hash algorithm that can be expressed in about 5 lines of code. In comparison to the algorithm of Karger et al., jump consistent hash requires no storage, is faster, and does a better job of evenly dividing the key space among the buckets and of evenly dividing the workload when the number of buckets changes. Its main limitation is that the buckets must be numbered sequentially, which makes it more suitable for data storage applications than for distributed web caching. 

> 摘要
>
> ​		我们提出跳变一致哈希，一个快速，最小内存，一致哈希算法，可以表达在大约5行代码。 与Karger等人的算法相比，跳变一致哈希算法不需要存储，速度更快，并且更好地在桶中均匀分配关键空间，以及在桶数变化时均匀分配工作负载。 它的主要限制是桶必须按顺序编号，这使得它更适合于数据存储应用程序，而不是分布式Web缓存。

## **Introduction**

​		Karger et al. [1] introduced the concept of consistent hashing and gave an algorithm to implement it. Consistent hashing specifies a distribution of data among servers in such a way that servers can be added or removed without having to totally reorganize the data. It was originally proposed for web caching on the Internet, in order to address the problem that clients may not be aware of the entire set of cache servers. 

> 引言
>
> ​		Karger等 [1] 引入了一致散列的概念，并给出了实现它的算法。一致散列指定数据在服务器之间的分布，这样就可以添加或删除服务器，而不必完全重新组织数据。最初提出用于Internet上的web缓存，以解决客户端可能不知道整个缓存服务器集的问题。

​		Since then, consistent hashing has also seen wide use in data storage applications. Here, it addresses the problem of splitting data into a set of shards, where each shard is typically managed by a single server (or a small set of replicas). As the total amount of data changes, we may want to increase or decrease the number of shards. This requires moving data in order to split the data evenly among the new set of shards, and we would like to move as little data as possible while doing so. 

> ​	从此，一致性哈希也在数据存储应用程序中得到广泛使用。在这里，它解决了将数据切分为一组分片，其中通常由单个服务器(或一小部分服务器副本)管理每个分片的问题。随着数据量的变化，我们可能希望增加或者减少分片的数量。这需要数据迁移以便在新的分片集之间平均拆分数据，并且我们希望在这样做的时尽可能少的数据迁移。

​		Assume, for example, that data consisting of key­value pairs is to be split into 10 shards. A simple way to split the data is to compute a hash, h(key), of each key, and store the corresponding key­value pair in shard number h(key) mod 10. But if the amount of data grows, and now needs 12 shards to hold it, the simple approach would now assign each key to shard h(key) mod 12, which is probably not the same as h(key) mod 10; the data would need to be completely rearranged among the shards. 

> ​	例如，假设由键值对组成的数据被切分为10个分片。分割数据的一个简单方法是计算每个键的哈希值$h(key)$，并将响应的键值对存储在分片号$h(key)\ mod\ 10$ 中。但是如果数据量增加，现在需要12个分片来保存它，那么简单的方法是将每个键分配给分片$h(key)\ mod\ 12$,这可能与$h(key)\ mod\ 10$不同；数据需要在分片之间完全重新排列。

​		But it is only necessary to move 1/6 of the data stored in the 10 shards in order to end up with the data balanced among 12 shards. Consistent hashing provides this. Our jump consistent hash function takes a key and a number of buckets (i.e., shards), and returns one of the buckets. The function satisfies the two properties: (1) about the same number of keys map to each bucket, and (2) the mapping from key to bucket is perturbed as little as possible when the number of buckets is changed. Thus, the only data that needs to move when the number of  buckets changes is the data for the relatively small number of keys whose bucket assignment changed. 

> ​		但是仅需要移动存储在10个分片中$1/6$的数据，以使数据在12个分片之间重新平衡。一致性哈希提供了这一点。我们跳跃一致哈希需要一个键和多个存储桶(即分片)，并返回其中的一个存储桶。该函数满足两个属性：(1) 每个桶映射的键数量大致相同；(2)当存储桶数量变化时，从键到存储桶的映射尽可能少的被扰动。因此，当存储桶的数量改变时，仅需要移动存储桶key分配发生变化的相对较少的数据。

​		The jump consistent hash algorithm is fast and has a large memory advantage over the one presented in Karger et al. Their algorithm needs thousands of bytes of storage per candidate shard in order to get a fairly even distribution of keys. In a large data storage application, where there may be thousands of shards, that means that each client needs megabytes of memory for its data structures, which must be stored long term for the algorithm to be efficient. In contrast, jump consistent hash needs no memory beyond what fits in a few registers. Jump consistent hash also does a better job of splitting the keys evenly among the buckets, and of splitting the rebalancing workload among the shards. On the other hand, jump consistent hash does not support arbitrary server names, but only returns a shard number; it is thus primarily suitable for the data storage case. 

> ​		跳跃一致哈希算法速度快，并且比Karger等人提出的算法具有更大的内存优势。他们的算法需要每个候选分片有数千字节的存储空间来使key得到相当均匀的分布。在一个大数据存储应用中可能存在着数千个分片，这意味着每个客户端都需要数兆字节的内存来存储其数据结构，这些数据必须长期存储才能使算法高效运行。相比之下，跳跃一致哈希除了需要几个寄存器之外不需要任何内存。跳跃一致哈希还可以更好地在存储区之间均匀切分keys，并且在分片拆分后重新平衡分片的工作负载。另一方面，跳跃一致哈希不支持随意的服务器名称，而仅仅返回一个分片号；因此它主要用于数据存储情况下。

​		Figure 1 shows a complete implementation of jump consistent hash. Its inputs are a 64 bit key and the number of buckets. It outputs a bucket number in the range [0, num_buckets). The rest of this note explains what is going on in this code and gives theoretical and empirical performance results.

> ​		图1展示了跳跃一致哈希的完整实现。它的输入是一个64位的`key`和存储桶`kucket`的数量。它的输出是一个存储桶`bucket`的编号，范围值为$[0, num\_buckets)$。该注释的其余部分解释了此代码将要做的运算，并给出了理论和经验性能结果。

```c++
int32_t JumpConsistentHash(uint64_t key, int43_t num_buckets){
    int64_t b=-1, j=0;
    while(j < num_buckets){
        b = j;
        key = key * 2862933555777941757ULL + 1;
        j = (b + 1) * (double(1LL << 31)) / double((key >> 33) + 1);
    }
    return b;
}
```

$$
图1\ \ \ \ 跳跃一致哈希算法(c++)
$$

## **Related work**

​		Karger et al.’s consistent hash algorithm works by associating each bucket with a number of
randomly chosen points on the unit circle. Given a key, it hashes the key to a position on the unit circle, proceeds along the circle in a clockwise direction from there until it finds the first chosen point, and returns the bucket associated with that point. Storing the association requires memory proportional to the number of buckets times the number of points chosen per bucket. Karger et al.’s experiments used 1000 points per bucket to get to a standard deviation of 3.2% in the number of keys assigned to different buckets.

> 相关工作
>
> ​		Karger等人的一致性哈希算法通过将每个桶`bucket`与单位圆上许多随机选择的点相关联来工作。给定一个`key`值，通过哈希将其映射到单位圆上的一个位置，从那里顺时针方向沿着园前进，直到找到第一个选择节点，并返回这个节点相关的存储桶`bucket`。存储关系需要和存储桶`bucket`数量乘以每个存储桶选择节点数量成比例的内存。Karger等人的实验使用每个存储桶`bucket`1000个节点来获得分配给每个不同桶`bucket`的`key`数量的标准差为3.2%。

​		The only other algorithm we are aware of that computes a consistent hash is the rendezvous algorithm by Thaler and Ravishankar [3]. Used as a consistent hash, the original version of their algorithm takes a key, and for each candidate bucket, computes a hash function value h(key, bucket). It then returns the bucket for which the hash yielded the highest value. This requires time proportional to the number of buckets. Wang et al. [4] show how the buckets can be organized into a tree to make the time proportional to the log of the number of buckets. But their variant comes at the cost of balance when shards are added or removed, because they only re­balance across the lowest level nodes of their tree.

> ​		另一个所了解的计算一致性哈希算法是Thaler和Ravlshankar[3]的会和算法。作为一致性的哈希，其算法原始版本需要一个`key`，并且对于每个候选`bucket`，计算一个哈希函数值$h（key, bucket）$。然后它返回哈希产生最高值的`bucket`， 这需要时间与`bucket`数量成正比。Wang等[4]人展示了如何将`bucket`组织为一个`tree`，使时间与`bucket`数量的对数成正比。但是当添加或删除分片时，他们的差异是以平衡为代价的，因为他们只在`tree`的最低级别`node`之间重新平衡。

​		Both of these algorithms allow buckets to have arbitrary ids, and handle not only new buckets being added, but also arbitrary buckets being removed. This ability to add or remove buckets in any order can be valuable for cache servers where the servers are purely a performance improvement. But for data storage applications, where each bucket represents a different shard of the data, it is not acceptable for shards to simply disappear, because that shard is only place where the corresponding data is stored. Typically this is handled by either making the shards redundant (with several replicas), or being able to quickly recover a replacement, or accepting lower availability for some data. Server death thus does not cause reallocation of data; only capacity changes do. This means that shards can be assigned numerical ids in increasing order as they are added, so that the active bucket ids always fill the range [0, num_buckets).

> ​		这两种算法都允许桶具有任意id，并且不仅可以处理新增的`bucket`，还可以处理任意删除的`bucket`。这种以任意顺序添加或删除`bucket`的能力对于缓存服务器非常有价值，这些服务器纯粹为了提高性能。但是对于数据存储应用，每个`bucket`代表着不同的数据分片，分片简单的消失是不能接受的，因为分片是唯一存储相应数据的地方。通常，这是通过使分片冗余(具有多个副本),或能够快速恢复替换，或接受某些数据较低的可用性来处理。因此服务器宕机不会导致数据重新分配，只有容量变化才会。这意味着可以在添加分片时按递增顺序为分片分配数字ID，以便活跃的`bucket`ID始终填充在$[0,num_buckets)$范围。

**Explanation of the algorithm**

​		Jump consistent hash works by computing when its output changes as the number of buckets increases. Let ch(key, num_buckets) be the consistent hash for the key when there are
num_buckets buckets. Clearly, for any key, k, ch(k, 1) is 0, since there is only the one bucket. In
order for the consistent hash function to balanced, ch(k, 2) will have to stay at 0 for half the keys, k, while it will have to jump to 1 for the other half. In general, ch(k, n+1) has to stay the same asch(k, n) for n/(n+1) of the keys, and jump to n for the other 1/(n+1) of the keys.

> ​		跳跃一致性哈希的工作原理是计算它的输出随着`bucket`数量增加而发生变化。当有$num\_buckets$个`bucket`时，令$ch(key, num\_buckets)$作为`key`的一致哈希。显然，对于任意键`k`,$ch(k, 1)$都是0，因为只有一个`bucket`。为了使一致的哈希函数达到平衡，$ch(k,2)$必须将一半键`k`保持为0，而另一半则必须跳到1。通常，对于所有键的n/(n+1)，$ch(k,n+1)$必须与$ch(k, n)$保持相同，而对于其余1/(n+1)的键，则跳转到第n个位置(第n+1个bucket上)。

​		Here are examples of the consistent hash values for three keys, k1, k2, and k3, as num_buckets goes up:

> ​		以下是随着`num_buckets`的增加，三个键k1、k2和k3的一致性哈希值示例：

![image-20221127194307406](https://s2.loli.net/2022/11/27/uBbj3EWnCo9TyzH.png)

​		A linear time algorithm can be defined by using the formula for the probability of ch(key, j) jumping when j increases. It essentially walks across a row of this table. Given a key and number of buckets, the algorithm considers each successive bucket, j, from 1 to num_buckets­1, and uses ch(key, j) to compute ch(key, j+1). At each bucket, j, it decides whether to keep ch(k, j+1) the same as ch(k, j), or to jump its value to j. In order to jump for the right fraction of keys, it uses a pseudo­random number generator with the key as its seed. To jump for 1/(j+1) of keys, it generates a uniform random number between 0.0 and 1.0, and jumps if the value is less than 1/(j+1). At the end of the loop, it has computed ch(k, num_buckets), which is the desired answer. In code: 

> ​		线性时间算法可以通过使用`j`增加时$ch(key, j)$跳跃概率的公式定义。它基本上遍历了这张表的一行。给定一个`key`和`bucket`的数量，该算法考虑从1到`num_buckets-1`连续`bucket` j，并使用$ch(key, j)$计算$ch(key, j+1)$。在每个bucket`j`，它决定是保持$ch(k,j+1)$与$ch(k, j)$相同，还是将其值跳转到`j`。为了需要跳转的部分keys跳转正确，它使用一个伪随机数生成器以`key`作为种子。为了跳转1/(j+1)的keys，它生成一个介于0.0和1.0之间的统一随机数，如果该值小于1/(j+1)则跳转。在循环结束时，他计算了$ch(k,num_buckets)$,这是所需的答案。在代码中

```python
import random

def ch(key, num_buckets):
    random.seed(key)
    b = 0
    for j in range(1, num_buckets):
        if random.random() < (1.0 / (j + 1)):
            b = j
    return b
```

​		We can convert this to a logarithmic time algorithm by exploiting that ch(key, j+1) is usually unchanged as j increases, only jumping occasionally. The algorithm will only compute the destinations of jumps ­­ the j’s for which ch(key, j+1) ≠ ch(key, j). Also notice that for these j’s, ch(key, j+1) = j. To develop the algorithm, we will treat ch(key, j) as a random variable, so that we can use the notation for random variables to analyze the fractions of keys for which various propositions are true. That will lead us to a closed form expression for a pseudo­random variable whose value gives the destination of the next jump. 

> ​		我们可以利用$ch(key,j+1)$通常随着j的增加保持不变，只是偶尔跳跃，从而将其转化为对数时间算法。该算法将只计算$ch(key,j+1)≠ch(key,j)$的j的跳跃目的地。还要注意，对于这些j，$ch(key,j+1)=j$。为了开发算法，我们将$ch(key,j)$视为随机变量，以便我么可以使用随机变量的符号来分析各种命题问真的keys的部分。这将致使我们得到一个伪随机变量的封闭形式表达式，该变量的值给出了下一次跳跃的目的地。

​		Suppose that the algorithm is tracking the bucket numbers of the jumps for a particular key, k. And suppose that b was the destination of the last jump, that is, ch(k, b) ≠ ch(k, b+1), and ch(k, b+1) = b. Now, we want to find the next jump, the smallest j such that ch(k, j+1) ≠ ch(k, b+1), or equivalently, the largest j such that ch(k, j) = ch(k, b+1). We will make a pseudo­random variable whose value is that j. To get a probabilistic constraint on j, note that for any bucket number, i, we have j ≥ i if and only if the consistent hash hasn’t changed by i, that is, if and only if ch(k, i) = ch(k, b+1). Hence, the distribution of j must satisfy
$$
P(j ≥ i) = P( ch(k, i) = ch(k, b+1) )
$$
Fortunately, it is easy to compute that probability. Notice that since P( ch(k, 10) = ch(k, 11) ) is 10/11, and P( ch(k, 11) = ch(k, 12) ) is 11/12, then P( ch(k, 10) = ch(k, 12) ) is 10/11 * 11/12 = 10/12.In general, if n ≥ m, P( ch(k, n) = ch(k, m) ) = m / n. Thus for any i > b,  
$$
P(j ≥ i) = P( ch(k, i) = ch(k, b+1) ) = (b+1) / i
$$

> ​		假设该算法正在跟踪特定键k要跳转的bucket桶号。并且假设b是最后一次跳转的目的桶号，即$ch(k,b)≠ch(k,b+1)$，并且$ch(k,b+1)＝b$。现在我们想找下一跳，即最小的j，使得$ch(k,j+1)≠ch(k,b+1)$,或等价的，最大的j有$ch(k,j)=ch(k,b+1)$,我们将定义一个伪随机变量，其值为j。为了得到关于j的概率约束，注意，对于任意bucket桶号i,当且仅当一致性哈希没有被i改变时，我们有j≥i，即当且仅当$ch(k,i)=ch(k,b+1)$。因此，该分布必须满足$P(j \geq i)=P(ch(k,i)=ch(k,b+1))$，幸运的是，计算该概率很容易。注意，因为$P(ch(k,10)=ch(k,11))$的概率是$\frac{10}{11}$，$P(ch(k,11)=ch(k,12))$的概率是$\frac{11}{12}$，因此$P(ch(k,10)=ch(k,12))$的概率是$\frac{10}{11} \times \frac{11}{12} = \frac{10}{12}$,一般来说，如果n≥m，$P(ch(k,n)=ch(k,m))=\frac{m}{n}$，因此对于任意的i>b，有$P(j ≥ i) = P( ch(k, i) = ch(k, b+1) ) = \frac{b+1}{i}$。

​		Now, we generate a pseudo­random variable, r, (depending on k and j) that is uniformly distributed between 0 and 1. Since we want P(j ≥ i) = (b+1) / i, we set P(j ≥ i) iff r ≤ (b+1) / i. Solving the inequality for i yields P(j ≥ i) iff i ≤ (b+1) / r. Since i is a lower bound on j, j will equal the largest i for which P(j ≥ i), thus the largest i satisfying i ≤ (b+1) / r. Thus, by the definition of the floor function, j = floor((b+1) / r). 

> ​		 现在，我们生成一个伪随机变量r(取决于k和j),它在0和1之间服从均匀分布。因为我们$P(j \ge i) = \frac{b+1}{i}$,我们设置$P(j \ge i)$当且仅当$r \le \frac{b+1}{i}$,求解i的不等式得到$P(j \ge i)$当且仅当$i \le \frac{b+1}{r}$,由于i是j的下界，j将等于$P(j \ge i)$时的最大i，因此满足$i \le \frac{b+1}{r}$的最大i。因此根据flor函数的定义,$j = floor(\frac{b+1}{r})$。

​		Using this formula, jump consistent hash finds ch(key, num_buckets) by choosing successive jump destinations until it finds a position at or past num_buckets. It then knows that the previous jump destination is the answer. 

> ​		使用这个公式，跳转一致性哈希通过选择连续的跳转目的地找到$ch(key,num\_buckets)$，直到找到一个节点或者超过num_buckets。然后得到先前的跳转目的地就是答案。

```python
import random
import numpy

def ch(key, num_buckets):
    random.seed(key)
    b = -1
    j = 0
    while j < num_buckets:
        b = j
        r = random.random()
        j = int(numpy.floor((b + 1) / r))
        
    return b    
```

​		To turn this into the actual code of figure 1, we need to implement random. We want it to be fast, and yet to also to have well distributed successive values. We use a 64­bit linear congruential generator; the particular multiplier we use produces random numbers that are especially well distributed in higher dimensions (i.e., when successive random values are used to form tuples) [2]. We use the key as the seed. (For keys that don’t fit into 64 bits, a 64 bit hash of the key should be used.) The congruential generator updates the seed on each iteration, and the code derives a double from the current seed. Tests show that this generator has good speed and distribution. 

> ​		要将其转化为图1的实际代码，我们需要实现随机性。我们希望算法更快，同时还具有分布良好的连续值。我们使用64位线性同余生成器；我们使用的特定乘法器生成的随机数咋i更改维度上分布的特别好(即， 连续的随机值用于生成元组)[2]，我们使用`key`作为随机种子。（对于不适合64位的`key`，应使用该`key`的64位哈希值。）同余生成器在每次迭代时更新种子，代码从当前种子生成一个双精度值。测试表明，该生成器拥有良好的速度和分布。

​		It is worth noting that unlike the algorithm of Karger et al., jump consistent hash does not require the key to be hashed if it is already an integer. This is because jump consistent hash has an embedded pseudorandom number generator that essentially rehashes the key on every iteration. The hash is not especially good (i.e., linear congruential), but since it is applied repeatedly,  additional hashing of the input key is not necessary. 

> ​		值得注意的是，与Karger等人的算法不同，如果已经是整数，跳转一致性哈希不需要对`key`进行哈希处理。这是因为跳转 一致性哈希具有内在的伪随机数生成器，该生成器本质上会在每次迭代时重新刷新`key`。哈希不是特别的好用(即线性同余),但是由于它是重复应用的，因此不需要输入`key`的附加哈希值。

## Performance Analysis

​		The time complexity of the algorithm is determined by the number of iterations of the while loop. The while loop visits successive jump destinations, which are all less than the number of buckets n except for the last. Thus the expected number of iterations is one more than the the expected number of jumps below n. Since the chance that there is a jump at number of buckets i is 1/i, the expected number of jumps to destinations less than n is just the sum of 1/i for i from 2 to n, which is less than ln(n). So the expected number of iterations is less than ln(n) + 1. 

> **性能分析**
>
> ​		算法的时间复杂度由while循环的迭代次数决定。while循环访问连续的跳跃目的地，除了最后一个之外，所有跳转目的地都小于bucket数n。因此，预期的迭代次数比预期的低于n的跳跃次数多1.由于在bucket编号i处跳转的概率是$\frac{1}{i}$，因此跳转到小于n的目的地的期望跳转次数恰好是i从2到n的$\frac{1}{i}$之和，小于$\ln{(n)}$，所以预计的迭代次数小于 $\ln{(n)}+1$。

​		It is interesting to note that jump consistent hash makes fewer expected jumps (by a constant factor) than the log2(n) comparisons needed by a binary search among n sorted keys. 

> ​		有意思的是，跳跃一致性哈希比n个有序`key`之间的二进制搜索所需要的$\log_2{(n)}$比较产生更少的预期跳转(通常是常数因子)。

## Performance Measurements

### Key Distribution

​		First we investigate how the algorithms compare in terms of distributing the keys uniformly among buckets. Recall that each key is first mapped to an integer hash value, which is then mapped to a corresponding bucket. The first step is common to both algorithms, so we focus on the second step. Ideally, all buckets should receive the same fraction of hash values. We can measure the deviation from this ideal by computing the standard error of the fraction of σ/μ) ( hash values assigned to each bucket. Note that for Karger et al.'s algorithm, this depends on the number of points chosen per bucket. The following table summarizes the results: 

![image-20221128172113087](https://s2.loli.net/2022/11/28/cAgMhX5uTCYmy1V.png)

> **`key`分布**
>
> ​		首先，我们研究算法如何在bucket之间均匀分布`key`方面进行比较。回想一下，首先将每个`key`映射为一个整数哈希值，然后将其映射到相应的bucket。两个算法的第一步相同，因此我们将重点放在第二步上。理想情况下，所有bucket都应接收相同部分的哈希值。我们可以通过计算$\sigma / \mu$的分数(分配给每个bucket的散列值)的标准差来测量与该理想的偏差。请注意，对于Karger等人的是短发，这取决于每个bucket选择的节点数。下表总结了结果。

​		The last column gives a 99% confidence interval for the bucket size compared to the average bucket size. For example, if Karger et al.'s algorithm is used with 10 points per bucket, then approximately 1% of the buckets will be less than 0.37x smaller than average or more than 1.98x  larger than average. This can lead to obvious problems with respect to load balancing. Even with 1000 points per bucket, approximately 1% of buckets will be at least 8% larger or smaller than average. In contrast jump consistent hash divides the key space almost perfectly. 

> ​		最后一列给出了bucket大小与平均bucket大小相比之下99%的置信区间。例如，如果使用Karger等人的算法，每个bucket有10个节点，则大约1%的bucket将比平均值小0.37倍或比平均值大1.98倍。这会导致在负载均衡方面出现明显问题。及时每个bucket有1000个节点，大约1%的bucket将至少8%大于或小于平均水平。相反，跳跃一致性哈希几乎完美的划分了`key`空间。

​		Of course there are also variations in bucket size due to the distribution of the actual keys. For many data storage applications, however, there will typically be millions of keys per bucket (e.g. where each key corresponds to a file, URL, document, etc), in which case the variations due to key distribution are negligible compared with the variations described above. 

> ​		当然，事实上由于`key`分布，bucket的大小也有所不同。然而，对于很多存储应用，通常每个bucket会有百万个`key`。（例如，其中每个key对应于文件、URL、文档等），在这种情况下，由于key分布的变化与上述变化相比是可忽略的。

​		The key distribution also affects the rebalancing workload when the number of buckets changes. When a new bucket is added with jump consistent hash, the new bucket receives an equal fraction of the key space of each existing bucket. For example, if there are 1000 buckets and a new bucket is added, then each existing bucket will transfer almost exactly 0.1% of its key space. With Karger et al.'s algorithm, on the other hand, the only buckets that participate in rebalancing are the ones that previously contained the points chosen to represent the new bucket. For example, if there are 1000 buckets with 10 points per bucket, then at most 10 buckets will send some of their data to the new bucket. The amount of data from each bucket can also vary substantially. This might cause problems, for example, if a bucket is being added in order to relieve a "hot spot" among the existing buckets: the new bucket has no effect unless the hot spot happens to be one of the 10 buckets selected, but on the other hand if the hot spot is selected, then it may not be able to handle the extra workload of transferring a large fraction of its data. This provides another reason for using a large number of points per bucket with the Karger et al. algorithm. 

> ​		当bucket的数量发生变化时，`key`分布也会影响负载再平衡。当新的bucket添加进了跳跃一致性哈希时，新的bucket会接收到每个现有bucket的`key`空间的相等分数。例如，如果有1000个bucket并且添加了一个新的bucket，则每个现有bucket将传输其`key`空间的0.1%。零一方面，使用Karger等人的算法，唯一参与再平衡的bucket是先前包含用于表示新bucket的点的bucket。例如，如果有1000个存储bucket，每个bucket有10个节点，那么最多10个存储bucket会将它们的一些数据发送到新的bucket。来自每个存储bucket的数据量也可能有很大差异。这可能会导致问题，例如，如果为了缓解现有bucket中的“热点”而添加了一个新bucket；除非热点恰好是选择的10个存储bucket之一，否则新的存储bucket没有任何作用，但另一方面，如果选择了“热点”，那么它可能无法处理传输其大部分数据的额外工作量。这为Karger等人的算法在每个存储bucket中使用大量的节点提供了另一个原因。

### Space Requirements 

​		Distributing the keys uniformly among buckets requires using many points per bucket in Karger et al.'s algorithm, but this increases memory requirements significantly. We implemented two variations of Karger et al.'s algorithm in addition to jump consistent hash. All implementations are in C++ and use the Standard Template Library. They were compiled on a 64­bit platform using Gnu C++ and measured on an Intel Xeon E5­1650 CPU with 32GB of memory. 

> 空间要求
>
> ​		在Karger等人的算法中，在存储bucket之间统一分布`key`需要每个存储`bucket`使用许多节点，但这大大增加了内存需求。除了跳跃一致性哈希外，我们还实现了Karger等人算法的两个变体。所有实现都在C++中，并使用了标准模板库。它们是使用GNU C++在64位平台上编译的，并在具有32GB内存的英特尔至强E5 1650 CPU上进行测试。

​		Our first implementation of Karger et al.'s algorithm ("version A") represents the point data as an STL map from a 64­bit hash value to a 32­bit bucket number. This is probably easiest and most natural way to implement the algorithm. Internally the map is represented as a balanced binary tree. This implementation uses 48 bytes per point per bucket. 

> ​		我们首先实现了Karger等人算法("版本A")，将数据节点表示为一个从64位哈希值到32位bucket编号的STL映射。这可能是实现算法最简单、最自然的方法。在内部，映射关系表示为一颗平衡二叉树。该实现每个存储bucket中每个节点使用48字节。

​			The second implementation ("version B") represents the point data as a sorted vector of (hash value, bucket number) pairs, where the hash values are truncated to 32 bits to save space. The bucket corresponding to a given hash value is located using binary search. This implementation uses less space (8 bytes per point per bucket), but unlike the previous implementation, it does not support dynamic updates efficiently: in order to change the number of buckets, the entire data structure must be rebuilt. 

> ​		第二种实现("版本B")将数据节点表示为一个有序的(哈希值、bucket编号)对的有序数组，其中哈希值被压缩到32位以节省空间。给定的哈希值相对应的存储bucket使用二分搜索进行定位。此实现使用更少的空间(每个存储bucket中每个节点使用8字节),但与之前的实现不同，它不支持高效的动态更新；为了改变存储bucket的数量，必须重新建立整个数据结构。

​		The table below presents the total data size for various numbers of buckets, assuming that 1000 points per bucket are used (following the example of Karger et al. in their paper). 

> ​		下图显示了不同数量的存储bucket的总数据大小，假设每个存储bucket使用1000个节点(遵循Karger等人在他们论文中的示例)。

![image-20221128215910180](https://s2.loli.net/2022/11/28/P2my4aYnSor9xJL.png)

​		These relatively large memory requirements are a significant disadvantage when consistent hashing is used to map requests to servers. Any client that wishes to map a request to the correct server must have a copy of the consistent hashing data available locally (or else incur the expense of additional network hops to route the request to its correct destination). This gives jump consistent hash a significant advantage as the number of buckets grows. 

> ​		当使用一致性哈希将请求映射到服务器时，这些相对较大的内存需求是一个明显的缺点。任何希望将请求映射到真确服务器的客户端都必须在本地具有可用的一致性哈希数据的副本（否则会导致额外的网络跳跃将请求路由到正确的目的地的代价）。随着存储bucket的增加，这将使跳跃一致性哈希具有显著优势。

### Execution Time

​		We measured the execution time of both algorithms on the platform described above, using a benchmark that computes the consistent hash values of a pseudorandom sequence of integer keys. 

> 执行时间
>
> ​		我们使用一个基准来计算整数`key`的伪随机序列的一致性哈希值，从而测量了上述平台上两种算法的执行时间。

![image-20221129141455464](https://s2.loli.net/2022/11/29/eOhaq6RAPcg5tbD.png)

​		Figure 4 compares jump consistent hash to the two Karger et al. implementations with k=10, k=100, and k=1000 points per bucket, and the corresponding graph illustrates the k=1000 case only. All times are in nanoseconds and do not include loop overhead. Figure 5 graphs the highlighted columns of figure 4. 

> ​		图4比较了跳跃一致性哈希和Karger等人的两个算法。每个存储bucket k=10、k=100、k=1000个节点的实现，相应的图表说明了k=1000的情况。所有的时间都已纳秒为单位并且不包括循环开销。

![image-20221129145627811](https://s2.loli.net/2022/11/29/lzPpyBTEcLbguKm.png)

A few points are worth noting:

- The cost of jump consistent hash is logarithmic in the number of buckets, even as the number of buckets grows very large (billions). 
- While the Karger et al. implementations also have O(log n) running times, their performance drops off substantially as the data size gets larger due to cache misses (which can increase the running time by a large constant factor). 
- With 1000 points per bucket, jump consistent hash is 3­4x faster for up to 1000 buckets, and perhaps 5­8x faster for up to 100,000 buckets. 
- Jump consistent hash is still faster than the the Karger et al. implementations even when only 10 points per bucket are used (although this is too few to be practical). 

> 有几点值得注意：
>
> - 跳跃一致性哈希的时间代价在存储bucket数量上是对数级别，及时存储bucket的数量非常大(十亿)。
> - 然而Karger等人算法的实现也有$\log{(n)}$的运行时间，但由于缓存未命中(这会使运行时间增加一个很大的常数)，其性能也会随着数据大小变大而大幅下降。
> - 对于每个存储bucket有1000个节点，跳跃一致性哈希在1000个存储bucket时快3-4倍，在100000个存储bucket时快5-8倍。
> - 即使每个存储bucket仅有10个节点（尽管这太少了，不切实际），跳跃一致性哈希仍然比Karger等人的实现快。

​		More significantly, the execution times above assume that there is no work being done besides  consistent hashing. But real applications typically have much other work that needs to be done as well, which competes for the various levels of memory cache. A typical server might receive a request from somewhere, look up some information in its own data structures, and then send off one or more requests to a data storage system to fetch additional data needed to satisfy the request. This last step uses consistent hashing. But for every consistent hashing calculation, the application almost certainly makes quite a few other memory accesses. 

> ​		更重要的是，上面执行的时间假设除了一致性哈希之外没有做任何工作。但实际应用程序通常还需要完成许多其他工作，这些工作会竞争不同级别的内存缓存。典型的服务器通常从某个地方接收到请求，在其自身的数据结构中查找一些信息，然后向存储系统发送一个或多个请求，以获取满足请求所需的额外数据。最后一步使用一致性哈希。但对于每一次一致性哈希的计算，应用程序基本上肯定会进行许多其他内存访问。

​		To simulate the behavior of a typical server, we created a benchmark that allocates an additional 1 GB of memory to correspond to the internal data maintained by a server. For each consistent hash calculation, the benchmark reads 16 random bytes within this memory to simulate hash table lookups, pointer following, etc. It also reads one 64K contiguous block within this memory to simulate access to a large in­memory cache. 

> ​		为了模拟典型服务器行为，我们建立了一个基准，该基准为服务器维护内部数据分配了额外的1GB内存。对于每个一致性哈希计算，基准在这个存储器中读取16个随机字节，以模拟哈希表查找、指针跟踪等。台海读取该内存中一个64K连续块，以模拟对大型内存缓存的访问。

​		The following table and graph show the timings of the consistent hash implementations in this environment. As before, all times are in nanoseconds and loop overhead has been subtracted. 

> ​		下表和图显示了此环境中一致性哈希实现的时间。与之前一样，所有时间都以纳秒未单位，并且循环考校已减去。

![image-20221129154554850](https://s2.loli.net/2022/11/29/s7zw8PfUnv91ZSo.png)

![image-20221129154630345](https://s2.loli.net/2022/11/29/xwQqfWPGChmdaLg.png)

​		The benchmark changes affect jump consistent hash only slightly, but for the Karger implementations the "knee" in the curves is shifted over significantly (i.e., the point where there is a large constant­factor increase in running time due to cache misses). This corresponds to the fact that less cache space is available for consistent hashing because of competition from other code. 

> ​		基准的变化仅稍微影响跳跃一致性哈希，但对于Karger实现，曲线中的“拐点”会大幅偏移(即，由于缓存未命中导致运行时间大幅增加的点)。这与以下事实相对应：即由于来自其他代码的竞争，更少的缓存空间可用于一致性哈希。

### Initialization Time 

​		The Karger et al. algorithm can also require a significant amount of time to build its data structures. The following table shows the time (in seconds) to build the data structures for various numbers of buckets with k=1000 points per bucket. 

> 初始化时间
>
> ​		Karger等人的算法也需要大量时间来构建其他数据结构。下表显示了为每个存储bucket k=1000个节点，不同数量的存储bucket构建数据结构所需要的时间(以秒为单位)。

![image-20221129155459692](https://s2.loli.net/2022/11/29/uDZvtzd4gPFwmnb.png)

​		The main points worth noting are that the balanced­tree implementation (STL map) is relatively slow to initialize, and that both implementations have very significant initialization times as the number of buckets grows large. Also note that with the Karger B implementation, the data structure must be completely rebuilt whenever the number of buckets changes. 

> ​		值得注意的要点是，平衡树实现(STL MAP)的初始化速度相对较慢，并且随着存储bucket数量的增加，这两种实现都具有非常长的初始化时间。还要注意，对于Karger B实现，每当存储bucket数量发生变化，都必须重新构建数据结构。

​		Google has not applied for patent protection for this algorithm, and, as of this writing, has no plans to. Rather, it wishes to contribute this algorithm to the community. 

> ​		注意：Google尚未为此算法申请专利保护，并且截至撰写本文时，还没有计划。相反，它希望将此算法贡献给社区。

### References

参考文献

[1] David Karger, Eric Lehman, Tom Leighton, Matthew Levine, Daniel Lewin and Rina Panigrahy. Consistent hashing and random trees: Distributed caching protocols for relieving hot spots on the World Wide Web. In Proceedings of the 29th ACM Symposium on Theory of Computing (STOC), pages 654­663, 1997. 

[2] P. L'Ecuyer. Tables of Linear Congruential Generators of Different Sizes and Good Lattice Structure. In Mathematics of Computation 68 (225): pages 249–260. 

[3] David G. Thaler and Chinya V. Ravishankar. Using Name­Based Mappings to Increase Hit Rates. In IEEE/ACM Transactions on Networking, Volume 6, pages 1­14, 1997. 

[4] Wei Wang and Chinya V. Ravishankar. Hash­Based Virtual Hierarchies for Scalable Location Service in Mobile Ad­hoc Networks. In Mobile Networks and Applications, Volume 14, Issue 5, pages 625­637, October 2009. 