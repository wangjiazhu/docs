# A Fast, Minimal Memory, Consistent Hash Algorithm(Jump Consistent Hash)

​														John Lamping, Eric Veach

​																			Google

> ​													约翰·兰平，埃里克·维奇
> ​																谷歌

**Abstract**

​		We present jump consistent hash, a fast, minimal memory, consistent hash algorithm that canbe expressed in about 5 lines of code. In comparison to the algorithm of Karger et al., jump
consistent hash requires no storage, is faster, and does a better job of evenly dividing the key
space among the buckets and of evenly dividing the workload when the number of buckets
changes. Its main limitation is that the buckets must be numbered sequentially, which makes it
more suitable for data storage applications than for distributed web caching.

> 摘要
>
> ​		我们提出跳变一致哈希，一个快速，最小内存，一致哈希算法，可以表达在大约5行代码。 与Karger等人的算法相比，跳变一致哈希算法不需要存储，速度更快，并且更好地在桶中均匀分配关键空间，以及在桶数变化时均匀分配工作负载。 它的主要限制是桶必须按顺序编号，这使得它更适合于数据存储应用程序，而不是分布式Web缓存。

**Introduction**

​		Karger et al. [1] introduced the concept of consistent hashing and gave an algorithm to
implement it. Consistent hashing specifies a distribution of data among servers in such a way
that servers can be added or removed without having to totally reorganize the data. It was
originally proposed for web caching on the Internet, in order to address the problem that clients may not be aware of the entire set of cache servers.

> 引言
>
> ​		Karger等 [1] 引入了一致散列的概念，并给出了实现它的算法。一致散列指定数据在服务器之间的分布，这样就可以添加或删除服务器，而不必完全重新组织数据。最初提出用于Internet上的web缓存，以解决客户端可能不知道整个缓存服务器集的问题。

​		Since then, consistent hashing has also seen wide use in data storage applications. Here, it
addresses the problem of splitting data into a set of shards, where each shard is typically
managed by a single server (or a small set of replicas). As the total amount of data changes, we
may want to increase or decrease the number of shards. This requires moving data in order to
split the data evenly among the new set of shards, and we would like to move as little data as
possible while doing so.

> ​	从此，一致性哈希也在数据存储应用程序中得到广泛使用。在这里，它解决了将数据切分为一组分片，其中通常由单个服务器(或一小部分服务器副本)管理每个分片的问题。随着数据量的变化，我们可能希望增加或者减少分片的数量。这需要数据迁移以便在新的分片集之间平均拆分数据，并且我们希望在这样做的时尽可能少的数据迁移。

​		Assume, for example, that data consisting of key-­value pairs is to be split into 10 shards. A
simple way to split the data is to compute a hash, h(key), of each key, and store the
corresponding key­value pair in shard number h(key) mod 10. But if the amount of data grows,
and now needs 12 shards to hold it, the simple approach would now assign each key to shard
h(key) mod 12, which is probably not the same as h(key) mod 10; the data would need to be
completely rearranged among the shards.

> ​	例如，假设由键值对组成的数据被切分为10个分片。分割数据的一个简单方法是计算每个键的哈希值$h(key)$，并将响应的键值对存储在分片号$h(key)\ mod\ 10$ 中。但是如果数据量增加，现在需要12个分片来保存它，那么简单的方法是将每个键分配给分片$h(key)\ mod\ 12$,这可能与$h(key)\ mod\ 10$不同；数据需要在分片之间完全重新排列。

​		But it is only necessary to move 1/6 of the data stored in the 10 shards in order to end up with the data balanced among 12 shards. Consistent hashing provides this. Our jump consistent
hash function takes a key and a number of buckets (i.e., shards), and returns one of the buckets.
The function satisfies the two properties: (1) about the same number of keys map to each
bucket, and (2) the mapping from key to bucket is perturbed as little as possible when the
number of buckets is changed. Thus, the only data that needs to move when the number of buckets changes is the data for the relatively small number of keys whose bucket assignment
changed.

> ​		但是仅需要移动存储在10个分片中$1/6$的数据，以使数据在12个分片之间重新平衡。一致性哈希提供了这一点。我们跳跃一致哈希需要一个键和多个存储桶(即分片)，并返回其中的一个存储桶。该函数满足两个属性：(1) 每个桶映射的键数量大致相同；(2)当存储桶数量变化时，从键到存储桶的映射尽可能少的被扰动。因此，当存储桶的数量改变时，仅需要移动存储桶key分配发生变化的相对较少的数据。

​		The jump consistent hash algorithm is fast and has a large memory advantage over the one
presented in Karger et al. Their algorithm needs thousands of bytes of storage per candidate
shard in order to get a fairly even distribution of keys. In a large data storage application, where
there may be thousands of shards, that means that each client needs megabytes of memory for
its data structures, which must be stored long term for the algorithm to be efficient. In contrast,
jump consistent hash needs no memory beyond what fits in a few registers. Jump consistent
hash also does a better job of splitting the keys evenly among the buckets, and of splitting the
rebalancing workload among the shards. On the other hand, jump consistent hash does not
support arbitrary server names, but only returns a shard number; it is thus primarily suitable for
the data storage case.

> ​		跳跃一致哈希算法速度快，并且比Karger等人提出的算法具有更大的内存优势。他们的算法需要每个候选分片有数千字节的存储空间来使key得到相当均匀的分布。在一个大数据存储应用中可能存在着数千个分片，这意味着每个客户端都需要数兆字节的内存来存储其数据结构，这些数据必须长期存储才能使算法高效运行。相比之下，跳跃一致哈希除了需要几个寄存器之外不需要任何内存。跳跃一致哈希还可以更好地在存储区之间均匀切分keys，并且在分片拆分后重新平衡分片的工作负载。另一方面，跳跃一致哈希不支持随意的服务器名称，而仅仅返回一个分片号；因此它主要用于数据存储情况下。