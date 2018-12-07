# Analysis 分析

**Audience**: Architects, Application and smart contract developers, Business
professionals

**读者**： 架构师，应用和智能合约开发者，业务专家

Let's analyze commercial paper in a little more detail. PaperNet participants
such as MagnetoCorp and DigiBank use commercial paper transactions to achieve
their business objectives -- let's examine the structure of a commercial paper
and the transactions that affect it over time. Later we'll focus on how money
flows between buyers and sellers; for now, let's focus on the first paper issued
by MagnetoCorp.

我们来深入一些分析商业票据。PaperNet参与了MagnetoCorp和DigiBank使用商业票据交易来取得他们的商业目标 ———— 让我们来检查一下商业票据的架构，和将一直影响他们的交易。然后我们将关注在在资金是如何从买家流向买家的，现在，我们将关注第一张由MagnetoCorp发行的票据。

## Commercial paper lifecycle
## 商业票据生命周期

A paper 00001 is issued by MagnetoCorp on May 31. Spend a few moments looking at
the first **state** of this paper, with its different properties and values:
票据00001由MagnetoCorp在五月31号发行。先花点时间看看这种票据的状态，有不同的属性和值：

```
Issuer = MagnetoCorp
Paper = 00001
Owner = MagnetoCorp
Issue date = 31 May 2020
Maturity = 30 November 2020
Face value = 5M USD
Current state = issued
```

This paper state is a result of the **issue** transaction and it brings
MagnetoCorp's first commercial paper into existence! Notice how this paper has a
5M USD face value for redemption later in the year. See how the `Issuer` and
`Owner` are the same when paper 00001 is issued. Notice that this paper could be
uniquely identified as `MagnetoCorp00001` -- a composition of the `Issuer` and
`Paper` properties. Finally, see how the property `Current state = issued`
quickly identifies the stage of MagnetoCorp paper 00001 in its lifecycle.

这张票据的状态是**发行**交易的结果，它诞生了MagnetoCorp的第一张商业票据。注意，该票据是拥有5MUSD的6个月之后的兑换面值。 看到 `Issuer` 和 `Owner` 在票据00001发行的时候是相同的。注意该票据可以用`MagnetoCorp00001`唯一标识，

Shortly after issuance, the paper is bought by DigiBank. Spend a few moments
looking at how the same commercial paper has changed as a result of this **buy**
transaction:

发行后不久，该票据就被DigiBank所购买。我们看看同一张商业票据在**购买**交易之后的变成了什么样子。

transaction:

```
Issuer = MagnetoCorp
Paper = 00001
Owner = DigiBank
Issue date = 31 May 2020
Maturity date = 30 November 2020
Face value = 5M USD
Current state = trading
```

The most significant change is that of `Owner` -- see how the paper initially
owned by `MagnetoCorp` is now owned by `DigiBank`.  We could imagine how the
paper might be subsequently sold to BrokerHouse or HedgeMatic, and the
corresponding change to `Owner`. Note how `Current state` allow us to easily
identify that the paper is now `trading`.

最大的改变是`Owner` ———— 最初是被`MagnetoCorp`所有的，现在被 `DigiBank`所有。 我们可以想想一下后续该票据是如何被Broker
House或者HegeMatic购买，并且相应的改变了`Owner`。注意`Current state`是如何允许我们轻松的识别出来该票据正在`trading`

After 6 months, if DigiBank still holds the the commercial paper, it can redeem
it with MagnetoCorp:
6个月之后，如果DigiBank仍然持有该票据，他可以从MagetoCorp赎回。
```
Issuer = MagnetoCorp
Paper = 00001
Owner = MagnetoCorp
Issue date = 31 May 2020
Maturity date = 30 November 2020
Face value = 5M USD
Current state = redeemed
```

This final **redeem** transaction has ended the commercial paper's lifecycle --
it can be considered closed. It is often mandatory to keep a record of redeemed
commercial papers, and the `redeemed` state allows us to quickly identify these.

这个最后的**redeem**交易结束了这张商业票据的生命周期 ———— 它可以被认为是关闭的。保存赎回的商业票据通常是强制的，并且`redeemed`状态可以让我们快速的识别出来。

## Transactions
## 交易

We've seen that paper 00001's lifecycle is relatively straightforward -- it
moves between `issued`, `trading` and `redeemed` as a result of an **issue**,
**buy**, or **redeem** transaction.

我们看到票据00001的生命周期是相对直白的 ———— 它在 `被发行`, `交易`和`被赎回`几个状态中移动，是**issue**，**buy**，或者 **redeem**交易的结果。

These three transactions are initiated by MagnetoCorp and DigiBank (twice), and
drive the state changes of paper 00001. Let's have a look at the transactions
that affect this paper in a little more detail:
这三次交易被MagnetoCorp和DigiBank（两次）发起，并且驱动票据00001的状态迁移。让我们看看影响这张票据的交易多一些细节。

### Issue
### 发行
Examine the first transaction initiated by MagnetoCorp:
看看MagnetoCorp发起的第一个交易

```
Txn = issue          // 交易号=issue
Issuer = MagnetoCorp // 发行者 MagnetoCorp
Paper = 00001        // 票据 00001
Issue time = 31 May 2020 09:00:00 EST //发行时间
Maturity date = 30 November 2020  //到期时间
Face value = 5M USD  //面值
```

See how the **issue** transaction has a structure with properties and values.
This transaction structure is different to, but closely matches, the structure
of paper 00001. That's because they are different things -- paper 00001 reflects
a state of PaperNet that is a result of the **issue** transaction. It's the
logic behind the **issue** transaction (which we cannot see) that takes these
properties and creates this paper. Because the transaction **creates** the
paper, it means there's a very close relationship between these structures.

看一下 **issue**交易的属性和值的结构。这个交易的结构跟票据00001的结构不一样，但是很像。这是因为他们是不同的东西 ———— 票据0001反应的是PaperNet的状态，作为**issue**交易的一个结果。是**issue**交易背后的逻辑带有这些属性，并创建了这张票据。因为交易**创建**了票据，它意味着，他们之间会有非常相似的结构。
### Buy
### 购买

Next, examine the **buy** transaction which transfers ownership of paper 00001
from MagnetoCorp to DigiBank:
接下来看看**购买**交易，它会把票据00001的所有权从MagnetoCorp转移到DigiBank：

```
Txn = buy  //交易号
Issuer = MagnetoCorp //发行商
Paper = 00001  // 票据
Current owner = MagnetoCorp//当前所有者
New owner = DigiBank //新所有者
Purchase time = 31 May 2020 10:00:00 EST //购买时间
Price = 4.94M USD //价格
```

See how the **buy** transaction has fewer properties that end up in this paper.
That's because this transaction only **modifies** this paper. It's only `New
owner = DigiBank` that changes as a result of this transaction; everything else
is the same. That's OK -- the most important thing about the **buy** transaction
is the change of ownership, and indeed in this transaction, there's an
acknowledgement of the current owner of the paper, MagnetoCorp.

看看**购买**交易有一些新的属性添加到这张票据中。这是因为这个交易只是**修改**了该票据。这笔交易的结果只是`New
owner = DigiBank`变了，其他都是新的。这就对了 ———— 关于**buy**交易最重要的事情就是它改变了所有权，并且事实上在这笔交易中，也承认了该票据的当前所有者是MagnetoCorp。

You might ask why the `Purchase time` and `Price` properties are not captured in
paper 00001? This comes back to the difference between the transaction and the
paper. The 4.94 M USD price tag is actually a property of the transaction,
rather than a property of this paper. Spend a little time thinking about
this difference; it is not as obvious as it seems. We're going to see later
that the ledger will record both pieces of information -- the history of all
transactions that affect this paper, as well its latest state. Being clear on
this separation of information is really important.

你也许会问，为什么  `Purchase time` 和 `Price`  在票据00001中是没有的？ 这又回来到交易和票据的不同之处。4.94M USD价格标签是交易的属性，而不是票据的属性。花点儿时间想想这二者之间的不同；并不是看起来那样的明显。我们将要看到，账本将记录两种信息 ———— 所有影响这张票据的历史，和最新的状态。清晰的知道这些信息的区别非常重要。

It's also worth remembering that paper 00001 may be bought and sold many times.
Although we're skipping ahead a little in our scenario, let's examine what
transactions we **might** see if paper 00001 changes ownership.

同样记住票据00001会被买卖多次也很有价值。尽管我们在我们的场景中向前跳了记不，我们看看什么交易**可能**盖面票据00001的所有权

If we have a purchase by BigFund:
如果我们有一个BigFund发起的购买：

```
Txn = buy  //交易号
Issuer = MagnetoCorp //发行商
Paper = 00001 //票据
Current owner = DigiBank //当前所有者
New owner = BigFund //新所有者
Purchase time = 2 June 2020 12:20:00 EST //购买时间
Price = 4.93M USD //价格
```
Followed by a subsequent purchase by HedgeMatic:
之后又被HedgeMatic购买
```
Txn = buy
Issuer = MagnetoCorp
Paper = 00001
Current owner = BigFund
New owner = HedgeMatic
Purchase time = 3 June 2020 15:59:00 EST
Price = 4.90M USD
```

See how the paper owners changes, and how in out example, the price changes. Can
you think of a reason why the price of MagnetoCorp commercial paper might be
falling?

看到票据所有者是如何变更的了，在我们的例子中，价格是如何变更的。你能才到为什么这张MagnetoCorp的商业票据的价格降了吗？

### Redeem
### 赎回

The **redeem** transaction for paper 00001 represents the end of its lifecycle.
In our relatively simple example, DigiBank initiates the transaction which
transfers the commercial paper back to MagnetoCorp:

票据00001的**赎回**交易代表着它生命周期的结束。在我们这个相对简单的例子中， DigiBank发起了交易把这张商业票据transfer会了MagnetoCorp：

```
Txn = redeem //交易编号
Issuer = MagnetoCorp //发行商
Paper = 00001 // 票据 
Current owner = HedgeMatic // 当前所有者
Redeem time = 30 Nov 2020 12:00:00 EST //赎回时间 
```

Again, notice how the **redeem** transaction has very few properties; all of the
changes to paper 00001 can be calculated data by the redeem transaction logic:
the `Issuer` will become the new owner, and the `Current state` will change to
`redeemed`. The `Current owner` property is specified in our example, so that it
can be checked against the current holder of the paper.

再一次注意，**赎回**交易只有非常少的几个属性；所有的票据00001的编程都可以被赎回交易的逻辑来计算 



## The Ledger

In this topic, we've seen how transactions and the resultant paper states are
the two most important concepts in PaperNet. Indeed, we'll see these two
fundamental elements in any Hyperledger Fabric distributed
[ledger](../ledger/ledger.html) -- a world state, that contains the current
value of all objects, and a blockchain that records the history of all
transactions that resulted in the current world state.

在本文中，我们看到交易是如何

You're now in a great place translate these ideas into a smart contract. Don't
worry if your programming is a little rusty, we'll provide tips and pointers to
understand the program code. Mastering the commercial paper smart contract is
the first big step towards designing your own application. Or, if you're a
business analyst who's comfortable with a little programming, don't be afraid to
keep dig a little deeper!

<!--- Licensed under Creative Commons Attribution 4.0 International License
https://creativecommons.org/licenses/by/4.0/ -->
