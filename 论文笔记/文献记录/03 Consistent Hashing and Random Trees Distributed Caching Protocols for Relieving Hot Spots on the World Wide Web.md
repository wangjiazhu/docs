# Consistent Hashing and Random Trees: Distributed Caching Protocols for Relieving Hot Spots on the World Wide Web 

## Abstract

**Abstract**

​		We describe a family of caching protocols for distrib-uted networks that can be used to decrease or eliminate the occurrence of hot spots in the network. Our protocols are particularly designed for use with very large networks such as the Internet, where delays caused by hot spots can be severe, and where it is not feasible for every server to have complete information about the current state of the entire network. The protocols are easy to implement using existing network protocols such as TCP/IP, and require very little overhead. The protocols work with local control, make efficient use of existing resources, and scale gracefully as the network grows. 

> ​		我们描述了分布式网络的一系列缓存协议，这些协议可用于减少或消除网络中热点的发生。我们的协议特别设置用于非常大的网络（例如Internet）,其中热点引起的延迟可能很严重，并且每个服务器都不能获得有关整个网络当前状态的完整信息。这些协议很容易使用现有的网络协议（如TCP/IP）来实现，并且仅需要很少的开销。这些协议与局部控制一起生效，有效的利用现有资源，并随着网络的增长而优雅地扩展。

​		Our caching protocols are based on a special kind of hashing that we call consistent hashing. Roughly speaking, a consistent hash function is one which changes minimally as the range of the function changes. Through the development of good consistent hash functions, we are able to develop caching protocols which do not require users to have a current or even consistent view of the network. We believe that consistent hash functions may eventually prove to be useful in other applications such as distributed name servers and/or quorum systems. 

> ​		我们的缓存协议基于一种特殊的哈希，我们称之为一致性哈希。粗略地说，一致性哈希函数是随着函数范围的变化而变化最小的函数。通过开发良好的一致性哈希函数，我们能够开发不需要用户具有当前甚至一致的是网络视图的缓存协议。我们认为，一致性哈希函数最终可能会被证明在其他应用程序中有用,例如分布式服务器和选举系统。

## 1 Introduction

**介绍**

​		In this paper, we describe caching protocols for distributed networks that can be used to decrease or eliminate the occurrences of “hot spots”. Hot spots occur any time a large number of clients wish to simultaneously access data from a single server. If the site is not provisioned to deal with all of these clients simultaneously, service may be degraded or lost. 

> ​		在这篇论文，我们描述了用于分布式网络的缓存协议，这些协议可以用来减少或消除“热点”问题的出现。每当大量客户端希望同时从单个服务器访问数据时，就会处出现热点。如果站点未提供同时处理这些客户端的配置，服务器可能会降低或丢失。

​		Many of us have experienced the hot spot phenomenon in the context of the Web. A Web site can suddenly become extremely popular and receive far more requests in a relatively short time than  it was originally configured to handle. In fact, a site may receive so many requests that it becomes “swamped,” which typically renders it unusable. Besides making the one site inaccessible, heavy traffic destined to one location can congest the network near it, interfering with traffic at nearby sites. 

> ​		我们中的很多人都经历过网络环境中的热点现象。一个网站可能变得非常受欢迎，并且在相对较短的时间内收到的请求远远超过最初配置的处理数。事实上，一个站点会受到很多的请求，以至于“被淹没”，这通常会导致它不可用。除了使一个站点无法访问之外，发往一个位置的大量流量还会使其附近的网络拥塞，从而干扰附近站点的流量。

​		As use of the Web has increased, so has the occurrence and impact of hot spots. Recent famous examples of hot spots on the Web include the JPL site after the Shoemaker-Levy 9 comet struck Jupiter, an IBM site during the Deep Blue-Kasparov chess tournament, and several political sites on the night of the election. In some of these cases, users were denied access to a site for hours or even days. Other examples include sites identified as “Web-site-of-the-day” and sites that provide new versions of popular software. 

> ​		随着Web使用的增加，热点的发生和影响也随之增加。最近网络上著名的热点历史包括Shoemaker-Levy 9彗星装机木星后的JPL网站、Deep Blue-Kasparos国际象棋比赛期间的IBM网站以及选举当晚的几个政治网站。在某些情况下，用户被拒绝访问数小时或者数天。其他的例子包括被标识为“每日网站”的网站和提供流行软件新版本的网站。

​		Our work was originally motivated by the problem of hot spots on the World Wide Web. We believe the tools we develop may be relevant to many client-server models, because centralized servers on the Internet such as Domain Name servers, Multi cast servers, and Content Label servers are also susceptible to hot spots. 

> ​		我们的工作最初是由万维网的热点问题引起的。我们认为，我们开发的工具可能与许多 客户端-服务端 模型有关，因为互联网上的集中式服务器（如域名服务器，多播服务器和内容标签服务器）也容易受到热点的影响。

### 1.1 Past Work

**过去相关工作**

​		Several approaches to overcoming the hot spots have been proposed. Most use some kind of replication strategy to store copies of hot pages throughout the Internet; this spreads the work of serving a hot page across several servers. In one approach, already in wide use, several clients share a proxy cache. All user requests are forwarded through the proxy, which tries to keep copies of frequently requested pages. It tries to satisfy requests with a cached copy; failing this, it forwards the request to the home server. The dilemma in this scheme is that there is more benefit if more users share the same cache, but then the cache itself is liable to get swamped. 	

> ​		已经提出了几种克服热点问题的方法。大多数人使用某种副本策略在整个互联网上存储热门页面的副本；这将服务一个热门页面的工作粉丝但到多个服务器上。在一种已经广泛使用的方法中，几个客户端共享一个代理缓存。所有用户请求都通过代理转发，该代理尝试保留频繁请求页面的副本。它尝试使用缓存副本来满足请求；如果失败，它将请求转发到主服务器。该方案的难题是，如果更多的用户共享一个缓存，则会有更多的好处，但是缓存本身容易被淹没。

Malpani et al. [6] work around this problem by making a group of caches function as one. A user's request for a page is directed to an arbitrary cache. If the page is stored there, it is returned to the user. Otherwise, the cache forwards the request to all other caches via a special protocol called “IP Multicast”. If the page is cached nowhere, the request is forwarded to the home site of the page. The disadvantage of this technique is that as the number of participating caches grows, even with the use of multicast, the number of messages between caches can become unmanageable. A tool that we develop in this paper, consistent hashing,gives a way to implement such a distributed cache without requiring that the caches communicate all the time. We discuss this in Section 4. 

> ​		Malpani等人[6]通过使一组缓存作为一个缓存来解决这个问题。用户对页面的请求被定向到任意缓存。如果页面存储在那里，它将返回给用户。否则，缓存会通过一个称为“IP多播”的特殊协议将请求转发到其他缓存。如果该页面没有缓存，请求将被转发到该页面的主站点。这种技术的缺点是，随着参与缓存的数量增加，技术使用多播，缓存之间消息的数量也会变得难以管理。我们在本文中提出的一个方法，即一致性哈希，提供了一种实现这种分布式缓存的方法，而无需缓存始终通信。我们将在第4节中对此进行讨论。

​		Chankhunthod et al. [1] developed the Harvest Cache, a more scalable approach using a tree of caches. A user obtains a page by asking a nearby leaf cache. If neither this cache nor its siblings have the page, the request is forwarded to the cache's parent. If a page is stored by no cache in the tree, the request eventually reaches the root and is forwarded to the home site of the page. A cache  retains a copy of any page it obtains for some time. The advantage of a cache tree is that a cache receives page requests only from its children (and siblings), ensuring that not too many requests arrive simultaneously. Thus, many requests for a page in a short period of time will only cause one request to the home server of the page, and won' t overload the caches either. A disadvantage, at least in theory, is that the same tree is used for all pages, meaning that the root receives at least one request for every distinct page requested of the entire cache tree. This can swamp the root if the number of distinct page requests grows too large, meaning that this scheme also suffers from potential scaling problems. 

> ​		Chankhunthod等人提出了收获缓存。这是一种使用缓存树的更具可扩展性的方法。用户通过询问附近的页面缓存来获得页面。如果此缓存及其同级缓存都没有页面，该请求将转发给缓存的父级。如果树中没有缓存存储页面，则请求最终到达根并转发到页面的主站点。缓存会在一段时间内保存其获得的任意页面的副本。缓存树的优点是，缓存仅从其子级（和同级）接收页面请求，确保不会有太多请求同时到达。因此，在短时间内对一个页面的多次请求只会向该页面的主服务器发出一个请求，并且不会使缓存过载。至少在理论上，缺点是对所有页面使用相同的树，这意味着根对整个缓存树请求的每个不同页面至少接收一个请求。如果不同页面请求的数量增长过大，这可能会淹没树根，这意味着该方案也存在潜在的缩放问题。

​		Plaxton and Rajaraman [9] show how to balance the load among all caches by using randomization and hashing. In particular, they use a hierarchy of progressively larger sets of “virtual cache sites” for each page and use a random hash function to assign responsibility for each virtual site to an actual cache in the network. Clients send a request to a random element in each set in the hierarchy. Caches assigned to a given set copy the page to some members of the next, larger set when they discover that their load is too heavy. This gives fast responses even for popular pages, because the largest set that has the page is not overloaded. It also gives good load balancing, because a machine in a small (thus loaded) set for one page is likely to be in a large (thus unloaded) set for another. Plaxton and Rajaraman's technique is also fault tolerant. 

> ​		Plaxton 和 Rajaraman[9]展示了如何通过使用随机化和哈希来平衡所有缓存之间的负载。特别是，他们为每个页面使用逐渐扩大的“虚拟缓存站点”集合的层次结构，并使用随机哈希函数将每个虚拟站点的责任分配给网络中实际的缓存。客户端向层次结构中每个集合中的随机元素发送请求、分配给给定集合的缓存在发现负载太重时，将页面复制给下一个较大集合的某些成员。即使对于受欢迎的页面，这也能够快速响应，因为具有该页面的最大集合不会过载。它还提供了良好的负载平衡，因为在一个页面小（这样加载的）集合中的机器很可能在另一个页面的大（这样卸载的）集合中。 Plaxton 和 Rajaraman的技术也具有容错能力。

​		The Plaxton/Rajaraman algorithm has drawbacks, however. For example, since their algorithm sends a copy of each page request to a random element in every set, the small sets for a popular page are guaranteed to be swamped. In fact, the algorithm uses swamping as a feature since swamping is used to trigger replication. This works well in their model of a synchronous parallel system, where a swamped processor is assumed to receive a subset of the incoming messages, but otherwise continues to function normally. On the Internet, however, swamping has much more serious consequences. Swamped machines cannot be relied upon to recover quickly and may even crash. Moreover, the intentional swamping of large numbers of random machines could well be viewed unfavorably by the owners of those machines. The Plaxton/Rajaraman algorithm also requires that all communications be synchronous and/or that messages have priorities, and that the set of caches available be fixed and known to all users. 

> ​	然而，Plaxton 和 Rajaraman的算法有缺点。例如，由于他们的算法将每个页面请求的副本发送到每个集合中的随机元素，因此热门页面的小集合一定会被淹没。事实上，该算法使用繁忙作为一种功能，因为繁忙用于触发复制。这在他们的同步并行系统模型中运行得很好，在该模型中，假设一个繁忙的处理器接收一部分传入的消息，但在其他情况下继续正常工作。然而，在互联网上，繁忙的后果要严重得多。陷入困境的机器无法快速恢复，甚至可能崩溃。此外，大量随机机器的故意繁忙很可能被这些机器的所有者视为不利的。Plaxton 和 Rajaraman的算法还要求所有通信都是同步的和/或消息具有优先级，并且可用的缓存集是固定的，并且所有用户都知道。

### 1.2 Our Contribution

**我们的贡献**

​		Here, we describe two tools for data replication and use them to give a caching algorithm that overcomes the drawbacks of the preceding approaches and has several additional, desirable properties. 

> ​		在这里，我们描述了两种用于数据复制的工具，并使用它们来给出一种缓存算法，该算法克服了前面方法的缺陷，并具有一些额外的、所需的属性。

​		Our first tool, random cache trees, combines aspects of the structures used by Chankhunthod et al. and Plaxton/Rajaraman. Like Chankhunthod et al., we use a tree of caches to coalesce requests. Like Plaxton and Rajaraman, we balance load by using a different tree for each page and assigning tree nodes to caches via a random hash function. By combining the best features of Chankhunthod et al. and Plaxton/Rajaraman with our own methods, we prevent any server from becoming swamped with high probability, a property not possessed by either Chankhunthod et al. or Plaxton/Rajaraman. In addition, our protocol shows how to minimize memory requirements (without significantly increasing cache miss rates) by only caching pages that have been requested a sufficient number of times. 

> ​		我们的第一个工具-随机缓存树，结合了Chankhunthod等人和Plaxton/Rajaraman等人使用的结构的各个方面。像Chankhunthod等人一样，我们使用缓存树来合并请求。像Plaxton/Rajaraman一样，我们通过为各个页面使用不同的树，并通过随机哈希函数将树节点分配给缓存来平衡负载。通过将Chankhunthod等人和Plaxton/Rajaraman的最佳特性与我们自己的方法相结合，我们可以防止任何服务器被高概率所繁忙，这是Chankhunthod等人和Plaxton/Rajaraman所不具备的特性。此外，我们的协议显示了如何通过仅缓存已请求足够次数的页面来最小化内存需求（而不会显著增加缓存未命中率）。

​		We believe that the extra delay introduced by a tree of caches should be quite small in practice. The time to request a page is multiplied by the tree depth. However, the page request typically takes so little time that the extra delay is not great. The return of a page can be pipelined; a cache need not wait until it receives a whole page before sending data to its child in the tree. Therefore, the return of a page also takes only slightly longer. Altogether, the  added delay seen by a user is small. 

> ​		我们认为，在实际情况下缓存树带来的额外延迟应该最小。请求页面的时间乘以树的深度。然而，页面请求的时间通常花很短的时间，因此额外的延迟不是很大。页面的返回可以流水线化。缓存不需要等到姐搜到整个页面后再将数据发送到树中的子级。伊尼茨，页面的返回也只需要稍长的时间。总之，用户看到的附加延迟很小。

​		Our second tool is a new hashing scheme we call consistent hashing. This hashing scheme differs substantially from that used in Plaxton/Rajaraman and other practical systems. Typical hashing based schemes do a good job of spreading load through a known, fixed collection of servers. The Internet, however, does not have a fixed collection of machines. Instead, machines come and go as they crash or are brought into the network. Even worse, the information about what machines are functional propagates slowly through the network, so that clients may have incompatible “views” of which machines are available to replicate data. This makes standard hashing useless since it relies on clients agreeing on which caches are responsible for serving a particular page. For example, Feeley et al [3] implement a distributed global shared memory system for a network of workstations that uses a hash table distributed among the machines to resolve references. Each time a new machine joins the network, the require a central server to redistribute a completely updated hash table to all the machines. 

> ​		我们的第二个工具是一种新的哈希方案，我们称之为一致性哈希。这种哈希方案与Plaxton/Rajaraman以及其他实际系统中使用的哈希方案有很大不同。典型的基于哈希的方案在通过已知固定服务器集合分散负载方面做得很好。然而，护粮网并没有固定的机器集合。相反，机器在崩溃或加入网络来来往往。更糟糕的是，有关哪些机器可用的信息在网络中传播缓慢，因此客户端可能对哪些机器可用于复制数据有矛盾的“看法”。这使得标准哈希变得无用，因为它依赖于客户端就哪些缓存负责为特定页面提供服务。例如，Feeley 等人[3]为工作站网络实现分布式全局共享内存系统，该系统使用分布在机器之间的哈希表来解析引用。每次有新机器加入网络时，都需要中央服务器将完全更新的哈希表重新分发给所有机器。

​		Consistent hashing may help solve such problems. Like most hashing schemes, consistent hashing assigns a set of items to buckets so that each bin receives roughly the same number of items. Unlike standard hashing schemes, a small change in the bucket set does not induce a total remapping of items to buckets. In addition, hashing items into slightly different sets of buckets gives only slightly different assignments of items to buckets. We apply consistent hashing to our tree-of-caches scheme, and show how this makes the scheme work well even if each client is aware of only a constant fraction of all the caching machines. In [5] Litwin et al proposes a hash function that allows buckets to be added one at a time sequentially. However our hash function allows the buckets to be added in an arbitrary order. Another scheme that we can improve on is given by Devine [2]. In addition, we believe that consistent hashing will be useful in other applications (such as quorum systems [7] [8] or distributed name servers) where multiple machines with different views of the network must agree on a common storage location for an object without communication. 

> ​		一致性哈希可能有助于解决此类问题。与大多数哈希方案一样，一致性哈希将一组items分配给bucket，以使每个bucket接收大致相同数量的items。与标准哈希方案不同，bucket之间微小变化不会导致整个items重新映射到bucket。此外，将items哈希到稍微不同的bucket集合中，只会使items分配给bucket的分配略有不同。我们将一致性哈希应用于我们的缓存树方案，并展示如何使该方案在即使每个客户端只知道所有缓存机器中的一部分情况下也能很好地工作。在[5]中，Litwin 等人提出了一种哈希函数，允许一次按顺序添加一个bucket。然而我们的哈希函数允许以任意顺序添加bucket。另一个我们可以改进的方案是由Devine [2]给出的。此外，我们相信一致性哈希在其他应用程序（如选举系统\[7]\[8]或分布式名称系统）中也很有用。在这些应用程序中，具有不同网络视图的多台机器必须在没有通信的情况下为一个对象的公共存储位置达成一致。

### 1.3 Presentation

**陈述**

​		In Section 2 we describe our model of the Web and the hot spot problem. Our model is necessarily simplistic, but is rich enough to develop and analyze protocols that we believe may be useful in practice. In Section 3, we describe our random tree method and use it in a caching protocol that effectively eliminates hot spots under a simplified model. Independent of Section 3, in Section 4 we present our consistent hashing method and use it to solve hot spots under a different simplified model involving inconsistent views. 

> ​		在第2节中，我们描述了我们的Web模型和热点问题。我们的模型必然是简单的，但足够丰富，可以开发和分析我们认为在实际中可能有用的协议。在第3节中，我们描述了我们的随机树方法，并将其用于缓存协议中，该协议在简化的模型下有效地消除了热点。独立于第3节，第4节，我们介绍了一致性哈希方法，并用它来解决涉及不一致视图的不同简化模型下的热点。

​		In Section 5 we show how our two techniques can be effectively combined. In Section 6 we propose a simple delay model that captures hierarchical clustering of machines on the Internet. We show that our protocol can be easily extended to work in this more realistic delay model. In Sections 7 and 8 we consider faults and the behavior of the protocol over time, respectively. In Section 9 we discuss some extensions and open problems. 

> ​		在第5节中，我们将展示如何将两种技术有效的结合起来。在第6节中，我们提出了一个简单的延迟模型，用于捕获Internet上机器的分层集群。 我们表明我们的协议可以很容易地扩展到这种更现实的延迟模型中。在第7节和第8节，我们分别考虑了故障和协议随时间的行为。在第9节，我们讨论了一些扩展和开放性问题。

### 1.4 A Note on Randomization and HASHING

**关于随机性哈希的注释**

​		In several places we make use of hash functions that map objects into a range. For clarity we assume that these functions map objects in a truly random fashion, i.e. uniformly and independently. In practice, hash functions with limited independence are more plausible since they economize on space and randomness. We have proven all theorems of this paper with only limited independence using methods similar to those in [11]. However, in this extended abstract we only state the degree of independence required for results to hold. Proofs assuming limited independence will appear in the full version of this paper. 

> ​		在一些地方，我们使用哈希函数将对象映射到一个范围内。为了清楚起见，我们假设这些函数以真正随机的方式映射对象，即一致且独立地映射对象。实际上，具有有限独立性的哈希函数更合理，因为他们节省了空间和随机性。我们已经使用类似于[11]中的方法证明了本文的所有定义都只有有限的独立性。然而，在这个扩展的摘要中，我们只陈述结果所需要的独立成都。假设独立性有限的证明出现在本文的完整版本中。

## 2 Model

**模型**

​		This section presents our model of the Web and the hotspot problem. 

> ​		本节介绍了我们的Web模型和热点问题。

​		We classify computers on the Web into three categories. All requests for Web pages are initiated by browsers. The permanent homes of Web pages are servers. Caches are extra machines which we use to protect servers from the barrage of browser requests. Throughout the paper, the set of caches is C and the number of caches is C.

> ​		我们将网络上的计算机分为三类。所有对网页的请求都由浏览器发起。网页的持久化存储是服务器。缓存是我们用来保护服务器免受浏览器请求攻击的额外机器。在整篇论文中，缓存的集合是c，缓存的数量是C。

​		Each server is home to a fixed set of pages. Caches are also able to store a number of pages, but this set may change over time as dictated by a caching protocol. We generally assume that the content of each page is unchanging, though Section 9 contains a discussion of this issue. The set of all pages is denoted P . 

> ​		每个服务器都有一组固定的也米娜。缓存也能够存储多个页面，但是此设置可能会随着时间的推移而变化，这取决于缓存协议。我们通常假设每一页的内容是不变的，尽管第9节包含了对这个问题的讨论。所有页面的集合表示为P。

​		Any machine can send a message directly to any other with the restriction that a machine may not be aware of the existence of all caches; we require only that each machine is aware of a 1/t fraction of the caches for some constant t . The two typical types of messages are requests for pages and the pages themselves. A machine which receives too many messages too quickly ceases to function properly and is said to be “swamped”. 

> ​		任何机器都可以直接向任何其他机器发送消息，但限制是机器可能不知道所有缓存的存在；我们只要求每台机器知道某个常数t的缓存的$\frac{1}{t}$部分。两种典型的消息类型是对页面的请求和页面本身。一台快速接收太多消息的机器无法正常工作被称为“繁忙”。

​		Latency measures the time for a message from machine m 1 to arrive at machine m 2 . We denote this quantity $\delta(m 1,m 2 )$ .In practice, of course, delays on the Internet are not so simply characterized. The value of $\delta$ should be regarded as a “best guess” that we optimize on for lack of better information; the correctness of a protocol should not depend on values of $\delta$ (which could actually measure anything such as throughput, price of connection or congestion) being exactly accurate. Note that we do not make latency a function of message size; this issue is discussed in Section 3.2.1. 

> ​		延迟度量是消息从机器m1到达机器m2的时间。我们将其表示为$\delta(m1,m2)$。当然，实际上互联网的延迟并不是那么简单。 $\delta$的值应被视为“最佳猜测”，我们会针对缺乏的信息进行优化；协议的正确性不应取决于$\delta$（实际上可以测量吞吐量、连接代价或拥塞等任何东西）的值是否准确。注意，我们没有将延时作为消息大小的函数；该问题将在3.2.1节讨论。

​		All cache and server behavior and some browser behavior is specified in our protocol. In particular, the protocol specifies how caches and servers respond to page requests and which pages are stored in a cache. The protocol also specifies the cache or server to which a browser sends each page request. All control must be local; the behavior of a machine can depend only on messages it receives. 

> ​		在我们的协议中制定了所有缓存、服务器和一些浏览器的行为。特别是，该协议指定了缓存和服务器如何响应页面请求以及哪些页面存储在缓存中。该协议还指定浏览器将每个页面发送到的缓存或服务器。所有控制必须是本地的；机器的行为只能取决于其接收到的消息。

​		An adversary decides which pages are requested by browsers. However, the adversary cannot see random values generated in our protocol and cannot adapt his requests based on observed delays in obtaining pages. We consider two models. First, we consider a static model in which a single “batch” of requests is processed, and require that the number of page requests be at most $R=\rho C$ where $\rho$ is a constant and $C$ is the number of caches. We then consider a temporal model in which the adversary may initiate new requests for pages at rate at most  $\gamma$; that is, in any time interval  $\tau > 1$ ,he may initiate at most $\rho \tau$ requests. 

> ​		对抗决定浏览器请求哪些页面。但是，对手无法看到我们协议中生成的随机值，也无法根据观察到的页面获取延迟来调整他们的请求。我们考虑两种模型。首先，我们考虑一个静态模型，其中处理单个“批”请求，并且要求页面请求的数量最多为$R=\rho C$,其中$\rho$是常数，$C$是缓存的数量。然后，我们考虑一个时间模型，在该模型中，对手最多可以发起新的页面请求的最大速率是$\gamma$;也就是说，在任意时间间隔$\tau > 1$，它最多可以发起$\gamma \tau$个请求。

### Objective

**目标**

​		The “hot spot problem” is to satisfy all browser page requests while ensuring that with high probability no cache or server is swamped. The phrase “with high probability” means “with probability at least $1-\frac{1}{N}$”, where N is a confidence parameter used throughout the paper. 

> ​		“热点问题”是为了满足所有浏览器页面请求，同时确保高速缓存或服务器很可能不会被淹没。短语"高概率"的意思是“概率比至少为 $1 - \frac{1}{N}$，其中$N$是整篇论文中使用的置信度参数。

​		While our basic requirement is to prevent swamping, we also have two additional objectives. The first is to minimize cache memory requirements. A protocol should work well without requiring any cache to store a large number of pages. A second objective is, naturally, to minimize the delay a browser experiences in obtaining a page. 

> ​		虽然我们基本要求是防止淹没，但是我们还有两个额外的目标。第一个目标是最小化缓存内存需求。协议应该在不需要任何缓存来存储大量页面的情况下运行良好，第二个目标自然是尽量减少浏览器在获取页面时遇到的延迟。

## 3 Random Trees

**随机树**

​		In this section we introduce our first tool, random trees. To simplify the presentation, we give a simple caching protocol that would work well in a simpler world. In particular, we make the following simplifications to the model: 

1. All machines know about all caches. 
2. $\delta (m_i,m_j)=1$ for all $i \ne j$.
3. All requests are made at the same time. 

> ​		在本机中，我们介绍我们第一个工具，随机树。为了简化演示，我们给出一个简单的缓存协议，该协议可以在更简单的环境中运行良好。特别是，我们对模型进行以下简化：
>
> 1. 所有汲取i都知道所有缓存。
> 2. $\delta (m_i,m_j)=1$ 当$i \ne j$
> 3. 同时提出所有请求。

​		This restricted model is “static” in the sense that there is only one batch of requests; we need not consider the long-term stability of the network. 

> ​		这种受限模型是“静态的”，因为只有一批请求；我们不需要考虑网络的长期稳定性。

​		Under these restrictions we show a protocol that has good behavior. That is, with high probability no machine is swamped. We achieve a total delay of $\Theta (\log{C})$ and prove that it is optimal. We use total cache space which is a fraction of the number of requests, and evenly divided among the caches. In subsequent sections we will show how to extend the protocol so as to preserve the good behavior without the simplifying assumptions. 

> ​		在这些限制下，我们展示了一个具有良好行为的协议。也就是说，很可能没有机器被淹没。我们实现了$\Theta (\log{C})$d的总延迟并证明它是最优的。我们使用的总缓存空间是3请求数的一小部分，并且在缓存之间平均分配。在接下来的章节中，我们将展示如何扩展协议，以便在不简化假设的情况下保持良好的行为。

​		The basic idea of our protocol is an extension of the “tree of caches” approach discussed in the introduction. We use this tree to ensure that no cache has many “children” asking it for a particular page. As discussed in the introduction, levels near the root get many requests for a page even if the page is relatively unpopular, so being the root for many pages causes swamping. Our technique, similar to Plaxton/Rajaraman's, is to use a different, randomly generated tree for each page. This ensures that no machine is near the root for many pages, thus providing good load balancing. Note that we cannot make use of the analysis given by Plaxton/Rajaraman, because our main concern is to prevent swamping, whereas they allow machines to be swamped. 

> ​		我们协议的基本思想是引言中讨论的“缓存树”方法的扩展。我们使用此树来确保没有缓存有许多“子级”向其请求特定页面。如简介中所述，即使页面相对不受欢迎，根目录附近也会收到许多页面请求，因此，作为很多页面的根目录也会被淹没。我们的技术与Plaxton/Rajaraman的类似，是为每个页面使用不同的随机生成树。这确保没有任何机器在许多页面的根目录附近，从而提供良好的负载平衡。请注意，我们不能利用Plaxton/Rajaraman给出的分析，因为我们主要关注的是防止机器被淹没，而他们允许机器被淹没。

​		In Section 3.1 below, we define our protocol precisely. In Section 3.2, we analyze the protocol, bounding the load on any cache, the storage each cache uses, and the delay a browser experiences before getting the page. 

> ​		在下面的3.1节中，我们精确地定义了我们的协议。在第3.2节中，我们分析了协议、限制任何缓存上的负载、每个缓存使用的存储，以及浏览器在获得页面之前所经历的延迟。

### 3.1 Protocol

**协议**

​		We associate a rooted d-ary tree, called an abstract tree, with each page. We use the term nodes only in reference to the nodes of these abstract trees. The number of nodes in each tree is equal to the number of caches, and the tree is as balanced as possible (so all levels but the bottom are full). We refer to nodes of the tree by their rank in breadth-first search order. The protocol is described as running on these abstract trees; to support this, all requests for pages take the form of a 4-tuple consisting of the identity of the requester, the name of the desired page, a sequence of nodes through which the request should be directed, and a sequence of caches that should act as those nodes. To determine the latter sequence, that is, which cache actually does the work for a given node, the nodes are mapped to machines. The root of a tree is always mapped to the server for the page. All the other nodes are mapped to the caches by a hash function $h:P \times [1 ... C]->c$ , which must be distributed to all browsers and caches. In order not to create copies of pages for which there are few requests, we have another parameter, $q$ , for how many requests a cache must see before it bothers to store a copy of the page. 

> ​		我们将一个有根的d-ary树(成为抽象树)与每个页面相关联。我们使用属于“节点”仅指代这些抽象树的节点。每棵树的节点数等于缓存数，并且树尽可能的平衡（因此除了底部外的所有层都是满的）。我们根据树的节点在广度优先搜索顺序中的排名来引用他们。协议被描述为运行在这些抽象树上；为了支持这一点，所有对页面的请求都采用4元组的像是，包括请求者的身份、所需页面的名称、请求应该通过的节点序列，以及充当这些节点的缓存序列。为了确定后一个序列，也就是说，哪个缓存实际上为给定的节点完成工作，该节点被映射到机器。树的根始终映射到页面的主服务器。所有其他节点都通过哈希函数$h:P \times [1...C]$映射到缓存$c$，他必须分发给所有浏览器和缓存。为了不创建请求数很少的页面副本，我们有另一个参数$q$,用于缓存在存储页面副本之前必须看到有多少请求数。

​		Now, given a hash function h , and parameters d and q , our protocol is as follows: 

> ​		现在，给定一个哈希函数h，以及参数d和q，我们的协议如下：

**Browser**	When a browser wants a page, it picks a random leaf to root path, maps the nodes to machines with h , and asks the leaf node for the page. The request includes the name of the browser, the name of the page, the path, and the result of the mapping. 

> **浏览器**  当浏览器想要一个页面时，他会选择一个随机的叶子到根路径，将节点映射到具有h的机器，并向叶子节点询问页面。请求包括浏览器的名称、页面名称、路径和映射结果。

**Cache**	When a cache receives a request, it first checks to see if it is caching a copy of the page or is in the process of getting one to cache. If so, it returns the page to the requester (after it gets its copy, if necessary). Otherwise it increments a counter for the page and the node it is acting as, and asks the next machine on the path for the page. If the counter reaches q ,it caches a copy of the page. In either case the cache passes the page on to the requester when it is obtained. 

> **缓存**	当缓存接收到请求，它首先检查时候正在缓存页面的副本或者是否正在获取一个要缓存的页面。如果是，他会将页面返回给请求者（在它获得其副本后，如果需要的话）。否则，他会为页面及其充当的节点增加一个计数器，并向路径上的下一个机器请求页面。如果计数器到达q，他将缓存页面的副本，在任何一种情况下，缓存在获得页面时，缓存都会将页面传递给请求者。

**Server**	When a server receives a request, it sends the requester a copy of the page. 

> **服务器**	当服务器收到请求时，他会像请求者发送该页面的副本。

### 3.2 Analysis

**分析**

​		The analysis is broken into three parts. We begin by showing that the latency in processing a request is likely to be small, under the assumption that no server is swamped. We then show that no machine is likely to be swamped. We conclude by showing that no cache need store too many pages for the protocol to work properly. 

> ​		分析分为三个部分。我们首先展示，在没有服务器被淹没的假设下，处理请求的延迟可能很小。然后我们证明没有机器可能被淹没。最后，我们结论是，任何缓存都不需要存储太多的页面，协议也能正常工作。

​		The analysis of swamping runs much the same way, except that the “weights” on our abstract nodes are now the number of requests arriving at those nodes. As above, the number of requests that hit a machine is bounded by the weight of nodes mapped to it. 

> ​		淹没分析的运行方式大致相同，只是抽象节点上的“权重”现在是到达这些节点的请求数。如上所述，命中机器的请求数受映射到机器的节点权重限制。

#### 3.2.1 Latency

**延迟**

​		Under our protocol, the delay a browser experiences in obtaining a page is determined by the height of the tree. If a request is forwarded from a leaf to the root, the latency is twice the length of the path, $2 \log_d{C}$ . If the request is satisfied with a cached copy, the latency is only less. If a request stops at a cache that is waiting for a cache copy, the latency is still less since a request has already started up the tree. Note that $d$ can probably be made large in practice, so this latency will be quite small. 

> ​		根据我们的协议，浏览器获取页面是所经历的延迟由树的高度决定。如果请求从叶子转发到根，则延迟是路径长度的两倍，即$2 \log_d{C}$。如果缓存副本满足请求，则延迟只会更小。如果请求在等待缓存副本的缓存处停止，则延迟仍然更短，因为请求已经在树上启动，因此延迟仍然较小。注意，在实际情况下$d$可能会变大，因此延迟会非常小。

​		Note that in practice, the time required to obtain a large page is not multiplied by the number of steps in a path over which it travels. The reason is that the page can be transmitted along the path in a pipelined fashion. A cache in the middle of the path can start sending data to the next cache as soon as it receives some; it need not wait to receive the whole page. This means that although this protocol will increase the delay in getting small pages, the overhead for large pages is negligible. The existence of tree schemes, like the Harvest Cache, suggests that is acceptable in practice. 

> ​		请注意，在实践中，获取大页面所需要的时间不会乘以它所经过的路径中的步数。原因是页面可以以流水线的方式沿路径传输。路径中间的缓存可以在收到数据后立即开始将数据发送到下一个缓存；它不需要等待接收整个页面。这意味着虽然这个协议会增加获取小页面的延迟，但大页面的开销可以忽略不计。树状方案的存在（如Harvest Cache）表明这在实践中是可以接受的。

​		Our bound is optimal (up to constant factors) for any protocol that forbids swamping. To see this, consider making $C$ requests for a single page. Look at the graph with nodes corresponding to machines and edges corresponding to links over which the page is sent. Small latency implies that this graph has small diameter, which implies that some node must have high degree, which implies swamping. 

> ​		对于任何禁止淹没的协议，我们的限制都是最优的（直到常数因子）。要看到这一点，请考虑对单个页面发出$C$请求。查看图，其中节点对应于机器，边对应于发送页面的链接。小延迟意味着该图具有最小路径，这意味着某些节点必须具有高的深度，这也意味着淹没。

#### 3.2.2 Swamping

**淹没**

​		The intuition behind our analysis is the following. First we analyze the number of requests directed to the abstract tree nodes of various pages. These give “weights” to the tree nodes. We then analyze the outcome when the tree nodes are mapped by a hash function onto the actual caching machines: a machine gets as many requests as the total weight of nodes mapped to it. To bound the projected weight, we first give a bound for the case where each node is assigned to a random machine. Our analysis gives a bound with an exponential tail. We can therefore argue as in [11] that it applies even when the balls are assigned to bins only $k = O(\log{N})$ way independently. This can be achieved by using a $k$-universal hash function to map the abstract tree nodes to machines. 

> ​		我们分析背后的直觉如下。首先我们分析了指向不同页面的抽象树节点的请求数。这些树节点赋予“权重”。然后我们分析了树节点通过哈希函数映射到实际缓存机器时的结果：一台机器收到的请求数与映射到它的节点的总权重一样多。为了限制预计的权重，我们首先为每个节点分配给随机机器的情况给出一个界限。我们的分析给出了一个带指数尾的界。因此，我们可以像[11]中那样的论证，即使当球仅以$k = O(\log{N})$的方式独立地分配给箱时，它也适用。这可以通过使用k通用哈希函数将抽象树节点映射到机器来实现。

​		We will now analyze our protocol under the simplified model. In this “static” analysis we assume for now that caches have enough space that they never have to evict pages; this means that if a cache has already made q requests for a page it will not make another request for the same page. In Theorem 3.1 we provide high probability bounds on the number of requests a cache gets, assuming that all the outputs of the function h are independent and random. Theorem 3.4 extends our high probability analysis to the case when h is a k -way independent function. In particular we show that it suffices to have k logarithmic in the system parameters to achieve the same high probability bounds as with full independence. 

> ​		我们将在简化模型下分析我们的协议，在这个“静态”分析中，我们现在假设缓存有足够的空间，他们永远不需要换出页面；这意味着如果一个缓存已经对一个页面发出$q$个请求，他就不会再对同一个页面发出另一个请求。在定理3.1中，假设函数$h$的所有输出都是独立且随机的，我们提供了缓存获得的请求数量的高概率界限。定理3.4将我们的高概率分析扩展到$h$是$k$路独立函数的情况。特别的是，我们表明系统参数中具有$k$个对数就足以实现与完全独立相同的高概率边界。

##### Analysis for Random h 

**随机h分析**

**Theorem 3.1 **	 *If h is chosen uniformly and at random from the space of functions* $P \times [1...C]->c$ *then  with probability at least* $1-\frac{1}{N}$, *where N is a parameter,the number of requests a given cache gets is no more than*
$$
\rho(2\log_d{C} + O(\frac{\log N}{\log \log N})) + O(\frac{dq \log N}{log(\frac{dq}{\rho} \log N)} + \log N)
$$

> **定理3.1**	如果 h 是从函数空间$P \times [1...C]$映射到 $c$， 则概率至少是$1 - \frac{1}{N}$，其中 $N$ 是一个参数，给定缓存的请求数不超过：$\rho(2\log_d{C} + O(\frac{\log N}{\log \log N})) + O(\frac{dq \log N}{log(\frac{dq}{\rho} \log N)} + \log N)$。

​		Note that $\rho \log_d C$ is the average number of requests per cache since each browser request could give rise to $\log_d C$ requests up the trees. The $\frac{\rho \log N}{\log \log N}$ term arises because at the leaf nodes of a tree's page some cache could occur log $\frac{\log N}{\log \log N}$ times (balls-in-bins) and the adversary could choose to devote all $R$ requests to that page. We prove the above Theorem in the rest of the section. 

> ​		请注意，$\rho \log_d C$是每个缓存的平均请求数，因为每个浏览器请求可能会在书上产生$\log_d C$ 请求。出现$\frac{\rho \log N}{\log \log N}$项是因为在树的页面的叶节点处，某些缓存可能会出现$\frac{\log N}{\log \log N}$次，并且对手可以选择将所有的$R$请求都投入到该页面。我们在本节的其余部分证明了上述定理。

​		We split the analysis into two parts. First we analyze the requests to a cache due to its presence in the leaf nodes of the abstract trees and then analyze the requests due to its presence at the internal nodes and then add them up. 

> ​		我们将分析分为两个部分。首先我们分析由于缓存在抽象树的叶节点中而导致的对缓存的请求，然后分析由于缓存存在于内部节点而产生的请求，然后将他们相加。

##### Requests to Leaf Nodes

**对叶节点的请求**

​		Due to space limitations, we give a proof that only applies when $N >> R$. Its extension to small $N$ is straightforward but long. Observe that the requests for each page are being mapped randomly onto the leaf nodes of its abstract tree. And then these leaf nodes are mapped randomly onto the set of caches. Look at collection of all the leaf nodes and the number of requests (weight) associated with each one of them. The variance among the “weights” of the leaf nodes is maximized when all the requests are made for one page. This is also the case which maximizes the number of leaf node requests on a cache. 

> ​		限于篇幅，我们给出了仅适用于$N >> R$的证明。他对小$N$的扩展很简单但很长。观察每个页面的请求被随机映射到其他抽象树的叶节点上。然后将叶节点随机映射到缓存集上。查看所有叶节点的集合以及每个叶节点关联的请求数（权重）。当所有请求都针对一个页面时，叶节点的“权重”之间的差异最大。这也是最大化缓存上叶节点请求数的情况。

​		Each page's tree has about $C(1 - \frac{1}{d})$ leaf nodes. Since a machine m has a $\frac{1}{C}$ chance of occurring at a particular leaf node, with probability $1 - \frac{1}{N}$ it will occur in $O(\frac{\log N}{\log \log N})$ leaf nodes. In fact, since there are at most $R$ requests, m will occur $O(\frac{\log N}{\log \log N})$ times in all those requested pages' trees with probability $1 - \frac{R}{N}$ .

> ​		每个页面的树大约有 $C(1 - \frac{1}{d})$ 个叶节点。由于机器m在特定叶节点处发生的概率为$\frac{1}{C}$，因此他将以$1 - \frac{1}{N}$的概率出现在$O(\frac{\log N}{\log \log N})$叶节点上。实际上，由于最多有$R$个请求，m将在所有请求的页面树中处出现$O(\frac{\log N}{\log \log N})$次，概率为$1 - \frac{R}{N}$。

​		Given an assignment of machines to leaf nodes so that $m$ occurs $O(\frac{\log N}{\log \log N})$ times in each tree, the expected number of requests m gets is $R \frac{1}{C} O(\frac{\log N}{\log \log N})$ which is $O(\frac{\rho \log N}{\log \log N})$ . Also, once the assignment of machine to leaf nodes is fixed, the number of requests m gets is a sum of independent Bernoulli variables. So by Chernoff bounds $m$ gets $O(\frac{\rho \log N}{\log \log N} + \log N)$ requests with probability $1 - \frac{1}{N}$ . So we conclude that $m$ gets $O(\frac{\rho \log N}{\log \log N} + \log N)$ with probability at least $1 - \frac{(1 + R)}{N}$ . Replacing $N$ by $N^2$ and assuming $N > R$ we can say that the same bound holds with probability $1 - \frac{1}{N}$。 It is easy to extend this proof so that the bound holds even for $N < R$ 。

> ​		给定机器分配给叶节点，使得$m$在每棵树中处出现$O(\frac{\log N}{\log \log N})$次，$m$得到的预期请求数为$R \frac{1}{C} O(\frac{\log N}{\log \log N})$，即$O(\frac{\rho \log N}{\log \log N})$。此外，一旦机器到叶节点的分配固定，m得到的请求数就是独立伯努利变量的总和。所以通过切尔诺夫边界，$m$得到$O(\frac{\rho \log N}{\log \log N} + \log N)$个请求，概率为$1 - \frac{1}{N}$。因此我们得出结论，$m$得到$O(\frac{\rho \log N}{\log \log N} + \log N)$的概率至少是$1 - \frac{(1 + R)}{N}$。将$N$替换为$N^2$并假设$N > R$，我们可以说相同的界限以概率$1 - \frac{1}{N}$成立。很容易扩展此证明，即使对于$N < R$，该限也成立。

##### Requests to Internal Nodes

**对内部节点的请求**

​		Again we think of the protocol as first running on the abstract trees. Now no abstract internal node gets more than $dq$ requests because each child node gives out at most $q$ requests for a page. Consider any arbitrary arrangement of paths for all the $R$ requests up their respective trees. Since there are only $R$ requests in all we can bound the number of abstract nodes that get $dq$ requests. In fact we will bound the number of abstract nodes over all trees which receive between $2^j$ and $2^{j+1}$ requests where $0 \le qj \le \log{dq} -1$ .Let $n_j$ denote the number of abstract nodes that receive between  $2^j$ and $2^{j+1}$ requests. Let $r_p$ be the number of requests for page $p$ . Then $\sum{r_p} \le R$. Since each of the $R$ requests gives rise to at most $\log_d{C}$ requests up the trees, the total number of requests is no more than $R\log_d{C}$. So, 
$$
\sum_{j = 0}^{\log(dq) - 1}{2^jn_j} \le R \log_d{C}\ \ \ \ \ \ \ \ \ (1)
$$

> ​		再次，我们认为该协议首先运行在抽象树上。现在，没有抽象的内部节点能得到超过$dq$个请求，因为每个子节点最多给出一个页面的$q$个请求。考虑所有$R$请求在其各自树上的任意路径排列。由于总共只有$R$个请求，因此我们可以限制获得$dq$请求的抽象节点的数量。实际上，我们将在接收$2^j$和$2^{j+1}$请求的所有树上限制抽象节点的数量，其中$0 \le qj \le \log{dq} -1$。令$n_j$表示接收$2^j$和$2^{j+1}$个请求之间的抽象节点的数量。设$rp$为页面p的请求数。然后是 $\sum{r_p} \le R$。由于每个$R$请求最多在树上引起$R \log_d{C}$个请求，因此请求总数不超过$R\log_d{C}$，所以：$\sum_{j = 0}^{\log(dq) - 1}{2^jn_j} \le R \log_d{C}\ \ \ \ \ \ \ \ \ (1)$

**Lemma 3.2**	The total number of internal nodes which receive at least $qx$ requests is at most $\frac{2R}{x}$ if $x > 1$

**引理 3.2**	如果x大于1，则至少接收$qx$请求的内部节点总数最多为$\frac{2R}{x}$

**Proof (sketch):**	Look at the tree induced by the request paths, contract out degree 1 nodes, and count internal nodes. 

> **证明：**	查看请求路径导出的树，收缩出度数为1的节点，并计算内部节点。

​		For x = 1 there can clearly be no more than $R \log_d{C}$ requests. The preceding lemma tells us that $n_j$, the number of abstract nodes that receive between $2^j$ and $2^{j+1}$ requests, is at most $\frac{2R}{2^j}$ except for $j = 0$ .For $j = 0$ , $n_j$ will be at most $R \log_d{C}$ . Now the probability that machine $m$ assumes a given one of these $n_j$ nodes is $\frac{1}{C}$ . Since assignments of nodes to machines are independent the probability that a machine $m$ is receives more than $z$ of these nodes is at most $(n_j z)(\frac{1}{C})^z$ . In order for the right hand side to be as small as $\frac{1}{N}$ we must have $z = \Omega (\frac{n_j}{C} + \frac{\log N}{\log{(\frac{C}{n_j} \log{N})}})$ . Note that the latter term will be present only if $\frac{C}{n_j}\log{N} > 2$.So $z$ is $O(\frac{n_j}{C} +  \frac{\log N}{\log{(\frac{C}{n_j} \log{N})}})$ with probability at least $1 - \frac{1}{N}$ . 

> ​		对于x=1，显然不会有超过$R\log_dC$的请求。前面的引理告诉我们，$n_j$是接收$2^j$和$2^{j+1}$个请求之间的抽象节点的数量，除了j = 0之外最多为$\frac{2R}{2^j}$。对于j = 0,最多为$R\log_dC$。现在机器$m$，假设这$n_j$个节点中给定一个节点的概率是$\frac{1}{C}$。由于将节点分配给机器是独立的，因此机器$m$接收到的这些节点多于z个的概率最多为$(n_j z)(\frac{1}{C})^z$，为了使右侧小到$\frac{1}{N}$，我们必须有$z = \Omega (\frac{n_j}{C} + \frac{\log N}{\log{(\frac{C}{n_j} \log{N})}})$。请注意，只有当$\frac{C}{n_j}\log{N} > 2$时，后一个术语才会处出现。所以z是$O(\frac{n_j}{C} +  \frac{\log N}{\log{(\frac{C}{n_j} \log{N})}})$，概率至少为$1 - \frac{1}{N}$。

​		So with probability at least $1 - \log{(dq)}/N$ the total number of requests received by $m$ due to internal nodes will be of the order of 
$$
\sum_{j=0}^{\log{(dq)}-1} 2^{j+1}(\frac{n_j}{C} + \frac{\log N}{\log{(\frac{C}{n_j} \log N)}}) = 2\rho \log_dC + O(\frac{dq\log N}{log(\frac{dq}{\rho} \log N)} + \log N)
$$
​		By combining the high probability bounds for internal and leaf nodes, we can say that a machine gets
$$
\rho (2 \log_d{C} + O(\frac{\log{N}}{\log{\log{N}}})) + O(\frac{dq \log{N}}{\log{(\frac{dq}{\rho} \log{N})}} + \log{N})
$$
requests with probability at least $1 - O(\frac{\log{dq}}{N})$ . Replacing $N$ by $N \log{(dq)}$ and ignoring $\log{\log{(dq)}}$ in comparision with $dq$ we get Theorem 3.1. 

> ​		因此，概率至少为$1 - \log{(dq)}/N$，m由于内部节点而的请求总数将为$\sum_{j=0}^{\log{(dq)}-1} 2^{j+1}(\frac{n_j}{C} + \frac{\log N}{\log{(\frac{C}{n_j} \log N)}}) = 2\rho \log_dC + O(\frac{dq\log N}{log(\frac{dq}{\rho} \log N)} + \log N)$
>
> ​		通过结合内部节点和叶节点的高概率边界，我们可以得到$\rho (2 \log_d{C} + O(\frac{\log{N}}{\log{\log{N}}})) + O(\frac{dq \log{N}}{\log{(\frac{dq}{\rho} \log{N})}} + \log{N})$，概率至少为$1 - O(\frac{\log{dq}}{N})$的请求。将$N$ 替换为 $N \log{dq}$，并忽略$\log{\log{(dq)}}$和$dq$进行比较，得到定理3.1。

**Tightness of the high probability bound**	In this section we show that the high probability bound we have proven for the number of requests received by a machine m is tight. 

> **高概率界的紧度**	在本节中，我们证明了机器$m$接收到的请求数量的高概率界限是严格的。

**Lemma 3.3** There exists a distribution of R requests to pages so that a given machine m gets $\Omega(\rho \log_d{C} + \rho \frac{\log{N}}{\log{\log{N}}} + \frac{dq \log{N}}{\log{(\frac{dq}{\rho} \log{N})}})$ requests with probability at least $\frac{1}{N}$ . 

> **引理 3.3**	存在对页面的R个请求的分布，使得机器m得到 $\Omega(\rho \log_d{C} + \rho \frac{\log{N}}{\log{\log{N}}} + \frac{dq \log{N}}{\log{(\frac{dq}{\rho} \log{N})}})$请求的概率至少为$\frac{1}{N}$。

**Proof**: Full paper. 

 **Analysis for k-way Independent h**	We now extend our high probability analysis to functions h that are chosen at random from a k -universal hash family. 

> **h的k路独立性行分析**	现在，我们将高概率分析扩展到k-通过用哈希族中随机选择的函数h。

**Theorem 3.4** If h is chosen at random from a k -universal hash family then with probability at least $1-\frac{1}{N}$ a given cache receives no more than $2 \rho \log_d{C} + O((kqN(d+\rho))^{\frac{1}{k}}(1+ \frac{\rho}{k} + \frac{dq}{\log(\frac{kdq}{p})} + \frac{\rho}{\log{k}}))$ requests.  

> **定理3.4**	如果h是从$k-universal$随机选择的，则给定缓存接收的概率至少为$1-\frac{1}{N}$的数据不超过$2 \rho \log_d{C} + O((kqN(d+\rho))^{\frac{1}{k}}(1+ \frac{\rho}{k} + \frac{dq}{\log(\frac{kdq}{p})} + \frac{\rho}{\log{k}}))$请求。

**Proof:** 	The full proof is deferred to the final version of the paper. This result does not follow immediately from the results of [11], but involves a similar argument. 

> **证明：**	完整的证明在论文的最终版。该结果并非直接来自于[11]的结果，但涉及类似的论点。

​		Setting k = log N we get the following corollary. 

> ​		设置 $k = \log N$我们得到如下推论。

**Corollary 3.5**	The high probability bound proved in theorem 3.1 for the number of requests a cache gets holds even if h is selected from a log N -universal hash family. 

> **推论3.5**	定理3.1中证明的缓存获得的请求数的高概率界限，即使h是从一个对数N通用哈希族中选择的也成立。

​		In fact, this can be shown to be true for all the bounds that we will prove later, i.e., it suffices k to be logarithmic in the system size. 

> ​		事实上，对于我们稍后证明的所有边界，这都是正确的，即 k 满足系统大小的对数。

#### 3.2.3 Storage

**存储**

​		In this section, we discuss the amount of storage each cache must have in order to make our protocol work. The amount of storage required at a cache is simply the number of pages for which it receives more than q requests. 

> ​		在本节中，我们将讨论每个缓存必须具有的存储量，以使我们的协议正常工作。缓存所需要的存储量就是它接收到超过$q$个请求的页面数。

**Lemma 3.6** The total number of cached pages, over all machines, is $O(\log R \log_d{C} +\frac{R}{q})$ with probability at least $1 - \frac{1}{R^{\Omega(1)}}$ .A given cache m has $O(\frac{\rho}{q} + \log{R})$ cached copies with high probability. 

>  **引理3.6**	所有机器上缓存的页面的总数为$O(\log R \log_d{C} + \frac{R}{q})$,概率至少为$1 - \frac{1}{R^{\Omega(1)}}$，给定的缓存m具有$O(\frac{\rho}{q} + \log{R})$个缓存的副本的可能性很高。

​	Proof (sketch): The analysis is very similar to that in proof of Theorem 3.1. We again play the protocol on the abstract trees. Since a page is cached only if it requested q times, we assign each abstract node a weight of one if it gets more than q requests and zero otherwise. These abstract nodes are then mapped randomly onto the set of caches. We can bound the total weight received by a particular cache, which is exactly the number of pages it caches. 

> **证明：**	分析与定理3.1的证明非常相似。我们再次在抽象树上运行协议。由于页面仅在请求q时才被缓存，如果每个抽象节点收到超过q次请求，我们为每个抽象节点分配权重，非则为零。然后将这些抽象节点随机映射到缓存集上。我们可以限制特定缓存接收的总权重，这正好是它缓存的页面数。

## 4 Consistent Hashing

**一致性哈希**

​		In this section we define a new hashing technique called consistent hashing. We motivate this technique by reference to a simple scheme for data replication on the Internet. Consider a single server that has a large number of objects that other clients might want to access. It is natural to introduce a layer of caches between the clients and the server in order to reduce the load on the server. In such a scheme, the objects should be distributed across the caches, so that each is responsible for a roughly equal share. In addition, clients need to know which cache to query for a specific object. The obvious approach is hashing. The server can use a hash function that evenly distributes the objects across the caches. Clients can use the hash function to discover which cache stores a object. Consider now what happens when the set of active caching machines changes, or when each client is aware of a different set of caches. (Such situations are very plausible on the Internet.) If the distribution was done with a classical hash function (for example, the linear congruential function $x \mapsto ax + b (mod \ p)$), such inconsistencies would be catastrophic. When the range of the hash function ( p in the example) changed, almost every item would be  hashed to a new location. Suddenly, all cached data is useless because clients are looking for it in a different location. 

> ​		在本节中，我们定义了一种新的哈希技术，称为一致性哈希。我们通过引用Internet上数据复制的简单方案来推动这一技术。考虑具有大量其他客户端可能想要访问的对象是单个服务器。为了减轻服务器的负载，在客户端和服务器之间引入一层缓存是很自然的。在这种方案中，对象应该分布在缓存中，以便使每个缓存负责大致相等的份额。此外，客户端需要知道要查询哪个缓存以查找特定对象，这显然用哈希。服务器可以使用哈希函数，将对象均匀分布在缓存中。客户端可以使用哈希函数来发现对象存储在哪个缓存上。现考虑一组活动缓存机器发生变化时，或者每个客户端都知道一组不同的缓存时，会发生什么 。（这种情况在互联网上很合理）.如果使用经典哈希函数（例如：线性同余函数 $x \mapsto ax + b (mod \ p)$）进行分布，那么这种不一致行将是灾难性的。当哈希函数的范围（示例中是p）发生变化时，几乎每个项都会哈希到一个新的位置。突然间，所有缓存的数据都变得没用了，因为客户端正在不同的位置查找数据。

​		Consistent hashing solves this problem of different “views.” We define a view to be the set of caches of which a particular client is aware. We assume that while views can be inconsistent, they are substantial: each machine is aware of a constant fraction of the currently operating caches. A client uses a consistent hash function to map a object to one of the caches in its view. We analyze and construct hash functions with the following consistency properties. First, there is a “smoothness” property. When a machine is added to or removed from the set of caches, the expected fraction of objects that must be moved to a new cache is the minimum needed to maintain a balanced load across the caches. Second, over all the client views, the total number of different caches to which a object is assigned is small. We call this property “spread”. Similarly, over all the client views, the number of distinct objects assigned to a particular cache is small. We call this property “load”. 

> ​		一致性哈希解决了不同“视图”的问题。我们将视图定义为特定客户端所知道的缓存集。我们假设虽然视图可能不一致，但他们是实质性的；每台机器都知道当前正在运行的缓存的一部分。客户端使用一致性哈希函数将对象映射到其视图中的缓存之一。我们分析并构造具有以下一致性属性的哈希函数。首先，有一个“平滑”属性。当一台机器被添加到缓存组或从缓存组中删除时，必须移动到新缓存的预期对象数是保持缓存间负载平衡所需要的最小值。其次，在所有客户端视图中，分配给对象的不同缓存的总数很少。我们称此属性为“传播”。同样，在所有客户端视图中，分配给特定缓存的不同对象的数量是最小的。我们称这个属性为“负载”。

​		Consistent hashing therefore solves the problems discussed above. The “spread” property implies that even in the presence of inconsistent views of the world, references for a given object are directed only to a small number of caching machines. Distributing a object to this small set of caches will insure access for all clients, without using a lot of storage. The “load” property implies that no one cache is assigned an unreasonable number of objects. The “smoothness” property implies that smooth changes in the set of caching machines are matched by a smooth evolution in the location of cached objects. 

> ​		因此，一致性哈希解决了上面讨论的问题。“传播”属性意味着即使在整个视图不一致的情况下，给定对象的引用也指会指向少数缓存机器。将对象分发到这一小组缓存将确保所有的客户端都可访问，而无需使用大量存储空间。“负载”属性意味着没有一个缓存分配了不合理数量的对象。“平滑”意味着缓存机器集中的平滑变化与缓存对象位置的平滑变化相匹配/

​		Since there are many ways to formalize the notion of consistency as described above, we will not commit to a precise definition. Rather, in Section 4.4 we define a “ranged hash function” and then precisely define several quantities that capture different aspects of “consistency”. In Section 4.2 we construct practical hash functions which exhibit all four to some extent. In Section 4.4, we discuss other aspects of consistent hashing which, though not germane to this paper, indicate some of the richness underlying the theory. 

> ​		由于有很多方法可以将上述一致性的概念形式化，因此我们不会提供一个精确的定义。相反，在4.4节中，我们定义了一个“范围哈希函数”，然后精确地定义了几个捕获“一致性”不同方面的量。在4.2节中，我们构建了实际的哈希函数，在一定程度上展示了这四个函数。在4.4节中，我们讨论了一致性哈希的其他方面，虽然与本文无关，但表明了该理论背后的一些丰富性。

### 4.1 Definitions

**定义**

In this section, we formalize and relate four notions of consistency. 

> ​		在本节中，我们将一致性的四个概念形式化并联系起来。

​		Let $I$ be the set of items and $B$ be the set of buckets. Let I$ = |I|$ be the number of items. A view is any subset of the buckets $B$ . 

> ​		令$I$表示项目集，$B$是存储桶集。令I$=|I|$为项目数。视图是存储桶$B$的任何子集。

​		A ranged hash function is a function of the $f = 2^{B} \times I \mapsto B$ . Such a function specifies an assignment of items to buckets for every possible view. That is, $f(\upsilon,i)$ is the bucket to which item $i$ is assigned in view $\upsilon$ . (We will use the notation $f_{\upsilon}(i)$ in place $f(\upsilon,i)$ from now on.) Since items should only be assigned to usable buckets, we require $f_{\upsilon}(I) \subseteq \upsilon$ for every view $\upsilon$ . 

> ​		范围哈希函数是$f = 2^{B} \times I \mapsto B$的函数形式。这样的函数为每个可能的视图指定一个项到桶的分配。也就是说，$f(\upsilon,i)$是视图$\upsilon$中项$i$分配到的桶。（从西安载器，我么能将用$f_{\upsilon}(i)$来代替$f(\upsilon,i)$）。由于项只应分配给可用的桶，因此对于每个视图，我们需要$f_{\upsilon}(I) \subseteq \upsilon$.

 		A ranged hash family is a family of ranged hash functions. A random ranged hash function is a function drawn at random from a particular ranged hash family. 

> ​		范围哈希族是一系列范围函数。随机范围哈希函数是从特定范围哈希组中随机抽取的函数。

​		In the remainder of this section, we state and relate some reasonable notions of consistency regarding ranged hash families. Throughout, we use the following notational conventions: $F$ is a ranged hash family, $f$ is a ranged hash function, $\upsilon$ is a view, $i$ is an item, and $b$ is a bucket. 

> ​		在本节的剩余部分，我们阐述并联系了关于范围哈希族的一些合理的一致性概念。在整个过程中，我们使用以下符号约定：$F$是一个范围哈希族，$f$是一个随机范围哈希函数。$\upsilon$是一个视图，$i$是一个项，$b$是一个存储桶。

**Balance:**	A ranged hash family is balanced if, given a particular view $\upsilon$ a set of items, and a randomly chosen function selected from the hash family, with high probability the fraction of items mapped to each bucket is $O(\frac{1}{\upsilon})$ . 

> **平衡性：**	如果给定一个特定视图$\upsilon$，一组项和从哈希族中随机选择的函数，且映射到每个存储桶的项的分数既有可能是$O(\frac{1}{\upsilon})$，则范围哈希函数是平衡的。	

​		The balance property is what is prized about standard hash functions: they distribute items among buckets in a balanced fasion. 

> ​		平衡属性是标准哈希函数所重视的，他们以均衡的方式在桶之间分配项。

**Monotonicity：**	A ranged hash function $f$ is monotone if for all views $\upsilon_1 \subseteq \upsilon_2 \subseteq B$, implies $f_{\upsilon_{1}}{(i)} = f_{\upsilon_{2}}{(2)}$. A ranged hash family is monotone if every ranged  hash function in it is.

> **单调性：**	对于所有视图$\upsilon_1 \subseteq \upsilon_2 \subseteq B$,一个范围哈希函数是单调的，意味着$f_{\upsilon_{1}}{(i)} = f_{\upsilon_{2}}{(2)}$.如果其中的每个范围哈希函数都是单调的，那么它就是单调的。

​		This property says that if items are initially assigned to a set of buckets $\upsilon_{1}$ and then some new buckets are added to form $\upsilon_{2}$ , then an item may move from an old bucket to a new bucket, but not from one old bucket to another. This reflects one intuition about consistency: when the set of usable buckets changes, items should only move if necessary to preserve an even distribution. 

> ​		此属性表示，如果最初将一组项分配给自于存储桶$\upsilon_{1}$，然后再添加一些新桶形成存储桶组$\upsilon_{2}$，则项可以从旧存储桶移动到新的存储桶，但是不能从一个旧存储桶移动到另一个。这反映了一种关于一致性的直觉：当可用桶的集合发生变化时，只有在必要时项才能移动，以保持均匀分布。

**Spread:** Let $\upsilon_{1} ... \upsilon_{v}$ be a set of views, altogether containing $C$ distinct buckets and each individually containing at least $\frac{C}{t}$ buckets. For a ranged hash function and a particular item $i$ ,the spread $\sigma(i)$ is the quantity $|\{ f_{\upsilon_{j}}(i)\}^{V}_{j=1}|$ .The spread of a hash function $\sigma(f)$ is the maximum spread of an item. The spread of a hash family is $\sigma$ if with high probability, the spread of a random hash function from the family is  $\sigma$. 

> **分布：**	$\upsilon_{1} ... \upsilon_{v}$是一组视图，总共包含$C$个不同的桶，每个视图至少包含$\frac{C}{t}$个存储桶。对于一个范围内的哈希函数和一个特定特定项$i$，分布$\sigma(i)$的数量是$|\{ f_{\upsilon_{j}}(i)\}^{V}_{j=1}|$，哈希函数$\sigma(f)$的分布是项的最大分布。哈希族的分布是$\sigma$具有很高的概率，哈希族中的随机哈希函数的分布就是$\sigma$。

​		The idea behind spread is that there are $V$ people, each of whom can see at least a constant fraction ($\frac{1}{t}$) of the buckets that are visible to anyone. Each person tries to assign an item $i$ to a bucket using a consistent hash function. The property says that across the entire group, there are at most $\sigma(i)$ different opinions about which bucket should contain the item. Clearly, a good consistent hash function should have low spread over all items. 

> ​		分布背后的想法是，有$V$个人，每个人至少都可以看到任何人都能看到的桶的$\frac{1}{t}$。每个人都试图用一致性哈希函数将项$i$分配给一个桶。该属性表示，在整个组中，最多有$\sigma(i)$个关于哪个存储桶应该包含项的不同意见。显然，一个良好的一致性哈希函数应该在所有项上岗会员体验低的分布律。

**Load:** Define a set of $V$ views as before. For a ranged hash function $f$ and bucket $b$ ,the load $\lambda(b)$ is the quantity  $|\cup_{\upsilon} f_{\upsilon}^{-1}(b)|$ . The load of a hash function is the maximum load of a bucket. The load of a hash family is $\lambda$ if with high probability, a randomly chosen hash function has load $\lambda$ . (Note that $f_{\upsilon}^{-1}(b)$ is the set of items assigned to bucket $b$ in view $V$ .) The load property is similar to spread. The same $V$ people are back, but this time we consider a particular bucket $b
$ instead of an item. The property says that there are at most $\lambda(b)$ distinct items that at least one person thinks belongs in the bucket. A good consistent hash function should also have low load.  

> **负载：**	像以前一样定义一组$V$视图。对于值域散列函数$f$和存储桶$b$，负载$\lambda(b)$是数量$|\cup_{\upsilon} f_{\upsilon}^{-1}(b)|$。哈希函数的最大负载是桶的最大负载。如果哈希族的负载很高概率是$\lambda$，则随机选择的哈希函数具有$\lambda$的负载。（注意，$f_{\upsilon}^{-1}(b)$是视图$V$中分配给存储桶$b$的项集。）负载属性类似与分布属性。同样的$V$个人回来了，但是这次我们考虑的是一个特定的桶$b$而不是一个项。该属性表示至少有一个人认为最多有$\lambda(b)$个不同的项属于存储桶$b$。一个好的一致性哈希函数还应该具有低负载。

​	Our main result for consistent hashing is Theorem 4.1 which shows the existence of an efficiently computable monotonic ranged hash family with logarithmic spread and balance. 

> ​		我们一致性哈希的主要结果是定理4.1,该定理表明了具有对数分布和平衡的有效可计算单调范围哈希族的存在。

### 4.2 Construction

**解释**

​		We now give a construction of a ranged hash family with good properties. Suppose that we have two random functions $\gamma_{B}$ and $\gamma_{I}$ . The function $\gamma_{B}$ maps buckets randomly to the unit interval, and $\gamma_{I}$ does the same for items. $f_\upsilon(i)$ is defined to be the bucket $b \in \upsilon$ that minimizes $| \gamma_{B}(b) - \gamma_{I}(i) |$.In other words, $i$ is mapped to the bucket “closest” to $i$ . For reasons that will become apparent, we actually need to have more than one point in the unit interval associated with each bucket. Assuming that the number of buckets in the range is always less than $C$ , we will need $k\log(C)$ points for each bucket for some constant $k$ . The easiest way to view this is that each bucket is replicated $k\log(C)$ times, and then $\gamma_{B}$ maps each replicated bucket randomly. In order to economize on the space to represent a function in the family, and on the use of random bits, we only demand that the functions $\gamma_{b}$ and $\gamma_{I}$ map points $\log(C)$ way independently and uniformly to $[0,1]$ . Note that for each point we pick in the unit interval, we need only pick enough random bits to distinguish the point from all other points. Thus it is unlikely that we need more than log( number of points ) bits for each point. Denote the above described hash family as $ F$ . 

> ​		我们给出了一个具有良好性质的有限哈希族的构造。假设我们有两个随机函数$\gamma_{B}$和$\gamma_{I}$，函数$\gamma_{B}$将存储桶随机映射到单位区间内，函数$\gamma_{I}$对项也是如此。$f_\upsilon(i)$被定义为使$|\gamma_{B}(b) - \gamma_{I}(i)|$最小的存储桶b，且b属于$\upsilon$。换句话说，项$i$映射到离$i$最近的存储桶。出于显而易见的原因，我们实际上需要在与每个桶关联的单位间隔中具有多余一个的节点。假设存储桶数量范围总是小于$C$，对于某个常数$k$，我们每个存储桶需要$k\log(C)$节点。看这一点的简单方法是每个存储桶被复制$k\log(C)$次，然后$\gamma_{B}$随机映射到每个复制的桶。为了节省空间表示来表示函数族中的一个函数，以及使用随机位，我们只要求函数$\gamma_{B}$和$\gamma_{I}$独立并且一直的将点$\log(C)$映射到区间$[0,1]$。注意，对于我们在单位间隔中选择的每个点，我们只需要选择足够的随机位来将节点与其他所有节点区分开来。因此，对于每个节点，我们不太可能需要超过对数(节点数)位。将上述哈希族表示为$F$。

**Theorem 4.1**	The ranged hash family $F$ described above has the following properties: 

1. $F$ is monotone.
2. Balance: For a fixed view $V$，$P_r[f_v(i) = b] \le \frac{O(1)}{|\upsilon|}$ for $i \in I$ and $b \in V$, and, conditioned on the choice of $\gamma_B$,the assignments of items to buckets are log(C)-way independent.
3. Spread: If the number if views $V = \rho C$ for some constant $\rho$,and the number items $I=C$,then for $i \in I$, $\rho(i)$ is $O(t\log(C))$ with probability greater than $1 - \frac{1}{C^{\Omega(1)}}$.
4. Load: If $V$ and $I$ are as above, then for $b \in B$, $\lambda(b)$ is $O(t\log(C))$ with probability greater than $1 - \frac{1}{C^{\Omega(1)}}$. 

> **定理4.1**	上述有限哈希族$F$具有以下性质：
>
> 1. $F$ 是单调的。
> 2. 平衡性：对一个固定的视图$V$,当$i \in I$ 并且 $b \in V$,有$P_r[f_v(i) = b] \le \frac{O(1)}{|\upsilon|}$，以$\gamma_{B}$的选择为条件，项到桶的分配是$\log(C)$是独立的。
> 3. 分布性：如果视图$V=\rho C$对于某个常数$\rho$，以及项数$I=C$,则对于$i \in I$，$\sigma(i)$是$O(t\log(C))$的概率大于$1 - \frac{1}{C^{\Omega(1)}}$。
> 4. 负载性：如果$V$和$I$和上述一样，对于$b \in B$,$\lambda(b)$是$O(t\log(C))$的概率超过$1 - \frac{1}{C^{\Omega(1)}}$。

