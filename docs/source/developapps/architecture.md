# Process and Data Design
# 处理过程和数据设计

**Audience**: Architects, Application and smart contract developers, Business
professionals
**读者**： 架构师，应用程序和智能合约开发者，业务专家

This topic shows you how to design the commercial paper processes and their
related data structures in PaperNet. Our [analysis](./analysis.html) highlighted
that modelling PaperNet using states and transactions provided a precise way to
understand what's happening. We're now going to elaborate on these two strongly
related concepts to help us subsequently design the smart contracts and
applications of PaperNet.

本文向你展示如何设计商业票据过程和与他们相关的数据结构在PaperNet中，我们的[analysis](./analysis.html)强调了使用状态和交易建模PaperNet提供了一之中精确的方式来理解发生了什么。 我们现在要届时这两个强相关的概念来帮我们后续在PaperNet上设计智能合约和应用

## Lifecycle
## 生命周期

As we've seen, there are two important concepts that concern us when dealing
with commercial paper; **states** and **transactions**. Indeed, this is true for
*all* blockchain use cases; there are conceptual objects of value, modelled as
states, whose lifecycle transitions are described by transactions. An effective
analysis of states and transactions is an essential starting point for a
successful implementation.

我们看到，当我们在交易商业票据是，有两个重要的概念要我们考虑；**状态**和**交易**。实际上，这个适用所有的区块链用例；有概念上的对象和值，被建模成状态，他们的生命周期变迁被描述为交易。对状态和交易的有解释是一个成功是实施的最有意义的开始。

We can represent the life cycle of a commercial paper using a state transition
diagram:
我们可以用用一个状态迁移图来表示商业票据的生命周期。

![develop.statetransition](./develop.diagram.4.png) *The state transition
diagram for commercial paper. Commercial papers transition between **issued**,
**trading** and **redeemed** states by means of the **issue**, **buy** and
**redeem** transactions.*

*商务票据的生命周期变迁图。商务票据在**已发行**，**正在交易中**和**被赎回**状态中变迁，通过**发行**，**购买**和**赎回**交易来进行*

See how the state diagram describes how commercial papers change over time, and
how specific transactions govern the life cycle transitions. In Hypledger
Fabric, smart contracts implement transaction logic that transition commercial
papers between their different states. Commercial paper states are actually held
in the ledger world state; so let's take a closer look at them.

看看状态图是如何描述商务票据随着时间变迁的，有些交易是如何主宰生命周期变迁的。在Hyperledger Fabric，智能合约实现交易的逻辑让商业票据在不同的状态之间变迁。商业票据状态事实上是在账本世界状态中保存的，所以让我们仔细看看他们。

## Ledger state
## 账本状态

Recall the structure of a commercial paper:
回一下商业票据的结构：

![develop.paperstructure](./develop.diagram.5.png) *A commercial paper can be
represented as a set of properties, each with a value. Typically, some
combination of these properties will provide a unique key for each paper.*

*一个商业票据可以表示为一组属性，每个属性都有一个值。一般情况下，某些属性的组合会为每个票据提供一个唯一的键值*

See how a commercial paper `Paper` property has value `00001`, and the `Face
value` property has value `5M USD`. Most importantly, the `Current state`
property indicates whether the commercial paper is `issued`,`trading` or
`redeemed`. In combination, the full set of properties make up the **state** of
a commercial paper. Moreover, the entire collection of these individual
commercial paper states constitutes the ledger
[world state](../ledger/ledger.html#world-state).



All ledger state share this form; each has a set of properties, each with a
different value. This *multi-property* aspect of states is a powerful feature --
it allows us to think of a Fabric state as a vector rather than a simple scalar.
We then represent facts about whole objects as individual states, which
subsequently undergo transitions controlled by transaction logic. A Fabric state
is implemented as a key/value pair, in which the value encodes the object
properties in a format that captures the object's multiple properties, typically
JSON. The [ledger
database](../ledger/ledger.html#ledger-world-state-database-options) can support
advanced query operations against these properties, which is very helpful for
sophisticated object retrieval.

所有的账本状态都贡献这张表；每个都有一个属性集合，每个都有不同的值。这个状态的*多属性*特点是一个非常有力的特征 ———— 它允许我们认为Fabric状态是一个向量而不是一个简单的纯量。我们把整个对象的facts表示单独的状态，会被经历被交易控制的变迁。一个Fabric状态使用key/value 对儿来实现，value以一种编码形式保存对象的多个属性，一般是JSON。 [ledger
database账本数据库](../ledger/ledger.html#ledger-world-state-database-options) 可以支持针对属性的高级查询操作，这对复杂对象的读取非常有帮助。

See how MagnetoCorp's paper `00001` is represented as a state vector that
transitions according to different transaction stimuli:
看看MagnetoCorp的票据 `00001` 是如何使用一个状态向量来表示的，以及不同交易刺激到什么样的变迁。


![develop.paperstates](./develop.diagram.6.png) *A commercial paper state is
brought into existence and transitions as a result of different transactions.
Hyperledger Fabric states have multiple properties, making them vectors rather
than scalars.*

*一个商业票据的状态被创建以及变迁 是不同交易导致的结果。Hyperledger Fabric状态有多个属性，让他们成为了一个向量而不是一个纯量*

Notice how each individual paper starts with the empty state, which is
technically a [`nil`](https://en.wikipedia.org/wiki/Null_(SQL)) state for the
paper, as it doesn't exist! See how paper `00001` is brought into existence by
the **issue** transaction, and how it is subsequently updated as a result of the
**buy** and **redeem** transactions.

注意每个单独的票据最初都是空状态，技术上使用 [`nil`](https://en.wikipedia.org/wiki/Null_(SQL))状态来表示，因为它根本不存在。看票据`00001`是如何被**issue**交易创造出来的，和后来如何被变更作为**购买**和**赎回**交易的结果。

Notice how each state is self-describing; each property has a name and a value.
Although all our commercial papers currently have the same properties, this need
not be the case for all time, as Hyperledger Fabric supports different states
having different properties. This allows the same ledger world state to contain
different forms of the same asset as well as different types of asset. It also
makes it possible to update a state's structure; imagine a new regulation that
requires an additional data field. Flexible state properties support the
fundamental requirement of data evolution over time.

注意每个状态是如何自明的，每个属性都有一个名字和值。尽管我们所有的商业票据当前有相同的属性，这不必须永远都这样，因为Hypeerledger Fabric支持不同的状态有不同的属性。这允许相同的账本世界状态包含不同形式的相同资产和不同资产。也使得能够更新状态的机构；想想一个新的法案需要更多的数据字段。弹性的属性支持数据随时间烟花的基础需求。

## State keys
## 状态键

In most practical applications, a state will have a combination of properties
that uniquely identify it in a given context -- it's **key**. The key for a
PaperNet commercial paper is formed by a concatenation of the `Issuer` and
`paper` properties; so for MagnetoCorp's first paper, it's `MagnetoCorp00001`.



A state key allows us to uniquely identify a paper; it is created as a result
of the **issue** transaction and subsequently updated by **buy** and **redeem**.
Hyperledger Fabric requires each state in a ledger to have a unique key.

When a unique key is not available from the available set of properties, an
application-determined unique key is specified as an input to the transaction
that creates the state. This unique key is usually with some form of
[UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier), which
although less readable, is a standard practice. What's important is that every
individual state object in a ledger must have a unique key.

## Multiple states

As we've seen, commercial papers in PaperNet are stored as state vectors in a
ledger. It's a reasonable requirement to be able to query different commercial
papers from the ledger; for example: find all the papers issued by MagnetoCorp,
or: find all the papers issued by MagnetoCorp in the `redeemed` state.

To make these kinds of search tasks possible, it's helpful to group all related
papers together in a logical list. The PaperNet design incorporates the idea of
a commercial paper list -- a logical container which is updated whenever
commercial papers are issued or otherwise changed.

### Logical representation

It's helpful to think of all PaperNet commercial papers being in a single list
of commercial papers:

![develop.paperlist](./develop.diagram.7.png) *MagnetoCorp's
newly created commercial  paper 00004 is added to the list of existing
commercial papers.*

New papers can be added to the list as a result of an **issue** transaction, and
papers already in the list can be updated with **buy** or **redeem**
transactions. See how the list has a descriptive name: `org.papernet.papers`;
it's a really good idea to use this kind of [DNS
name](https://en.wikipedia.org/wiki/Domain_Name_System) because well-chosen
names will make your blockchain designs intuitive to other people. This idea
applies equally well to smart contract [namespaces](./namespace.html).

### Physical representation

While it's correct to think of a single list of papers in PaperNet --
`org.papernet.papers` -- lists are best implemented as a set of individual
Fabric states, whose composite key associates the state with its list. In this
way, each state's composite key is both unique and supports effective list query.

![develop.paperphysical](./develop.diagram.8.png) *Representing a list of
PaperNet commercial papers as a set of distinct Hyperledger Fabric states*

Notice how each paper in the list is represented by a vector state, with a
unique **composite** key formed by the concatenation of `org.papernet.paper`,
`Issuer` and `Paper` properties. This structure is helpful for two reasons:

  * It allows us to examine any state vector in the ledger to determine which
    list it's in, without reference to a separate list. It's analogous to
    looking at set of sports fans, and identifying which team they support by
    the colour of the shirt they are wearing. The sports fans self-declare their
    allegiance; we don't need a list of fans.


  * Hyperlegder Fabric internally uses a concurrency control
    [mechanism](../arch-deep-dive.html#the-endorsing-peer-simulates-a-transaction-and-produces-an-endorsement-signature)
    to update a ledger, such that keeping papers in separate state vectors vastly
    reduces the opportunity for shared-state collisions. Such collisions require
    transaction re-submission, complicate application design, and decrease
    performance.

This second point is actually a key take-away for Hyperledger Fabric; the
physical design of state vectors is **very important** to optimum performance
and behaviour. Keep your states separate!

In the next topic, we will show you how to combine these design concepts to
implement the PaperNet commercial paper smart contract, and then an application
in exploits it!

<!--- Licensed under Creative Commons Attribution 4.0 International License
https://creativecommons.org/licenses/by/4.0/ -->
