# Blockchain network
# 区块链网络
This topic will describe, **at a conceptual level**, how Hyperledger Fabric
allows organizations to collaborate in the formation of blockchain networks.  If
you're an architect, administrator or developer, you can use this topic to get a
solid understanding of the major structure and process components in a
Hyperledger Fabric blockchain network. This topic will use a manageable worked
example that introduces all of the major components in a blockchain network.
After understanding this example you can read more detailed information about
these components elsewhere in the documentation, or try
[building a sample network](../build_network.html).

文本将在**概念层面**上描述Hpeyperledger Fabric如何让组织在区块链形式的网络中写作。如果你是架构师，管理员或者开发者，你可以通过此文 to get a solid understanding of the major structure and process components in a Hyperledger Fabric Blockchain network. 本文使用一个可以管理的实际例子来介绍区块链网络中的所有的主要组成部分。了解了本文的例子之后，you can read more detailed information about these componets eleswhere in the documentation, or try [building a sample network]


After reading this topic and understanding the concept of policies, you will
have a solid understanding of the decisions that organizations need to make to
establish the policies that control a deployed Hyperledger Fabric network.
You'll also understand how organizations manage network evolution using
declarative policies -- a key feature of Hyperledger Fabric. In a nutshell,
you'll understand the major technical components of Hyperledger Fabric and the
decisions organizations need to make about them.

阅读完本文，了解策略（policies）概念之后，你会深入的了解 the decisions that organizations need to make to establish the policies that control a deployed Hyperledger Fabric network. 组织需要做出得决定，来创建策略来控制一个已经部署的Hyperledger Fabric Network。你也将了解组织是如何管理网络的演进使用明确定义的策略——Hyperledger Fabric的一个关键特征。简而言之，你将了解Hyperledger Fabric的关键技术组成，和组织需要针对他们做作出的决定。


## What is a blockchain network?
## 什么是区块链网络？
A blockchain network is a technical infrastructure that provides ledger and
smart contract (chaincode) services to applications. Primarily, smart contracts
are used to generate transactions which are subsequently distributed to every
peer node in the network where they are immutably recorded on their copy of the
ledger. The users of applications might be end users using client applications
or blockchain network administrators.
一个区块链网络是一个技术架构，向应用提供账本（ledger）和 智能合约（chaincode）服务。 通常智能合约是用来产生交易的，
而交易将被分发到网络的各个对等节点上，并且被永久且不可篡改得记录到节点的账本副本上。应用的用户可能是使用客户端程序的终端用户或者是区块链管理员。

In most cases, multiple [organizations](../glossary.html#organization) come
together as a [consortium](../glossary.html#consortium) to form the network and
their permissions are determined by a set of [policies](../glossary.html#policy)
that are agreed by the consortium when the network is originally configured.
Moreover, network policies can change over time subject to the agreement of the
organizations in the consortium, as we'll discover when we discuss the concept
of *modification policy*.

大多数情况下，多个[组织](../glossary.html#organization)一起组成一个[联盟](../glossary.html#consortium)来组成网络，且他们的权限是在网络初始化的时候，用一组由联盟同意的[策略](../glossary.html#policy)来定义的。另外，网络的策略随着时间根据联盟组织的协议来变更，我们将在*修改策略*的话题中讨论。

## The sample network

## 示例网络

Before we start, let's show you what we're aiming at! Here's a diagram
representing the **final state** of our sample network.

我们开始之前先看看我们的目标是什么。下图是表示了我们的示例网络的"最终状态"

Don't worry that this might look complicated! As we go through this topic, we
will build up the network piece by piece, so that you see how the organizations
R1, R2, R3 and R4 contribute infrastructure to the network to help form it. This
infrastructure implements the blockchain network, and it is governed by policies
agreed by the organizations who form the network -- for example, who can add new
organizations. You'll discover how applications consume the ledger and smart
contract services provided by the blockchain network.

看起来有点儿复杂，不要担心！随着我们对该话题的深入探讨，我们一点一点的建立起来该网络，你将看到组织R1, R2, R3和R4将如果向该架构做贡献，to help to form it. 该架构实现了区块链网络，并且由组成该网络的组织统一的策略来治理的。例如，谁可以添加新的组织。 你将看到应用是如何访问区块链网络提供的账本和智能合约服务的。
![network.structure](./network.diagram.1.png)

*Four organizations, R1, R2, R3 and R4 have jointly decided, and written into an
agreement, that they will set up and exploit a Hyperledger Fabric
network. R4 has been assigned to be the network initiator  -- it has been given
the power to set up the initial version of the network. R4 has no intention to
perform business transactions on the network. R1 and R2 have a need for a
private communications within the overall network, as do R2 and R3.
Organization R1 has a client application that can perform business transactions
within channel C1. Organization R2 has a client application that can do similar
work both in channel C1 and C2. Organization R3 has a client application that
can do this on channel C2. Peer node P1 maintains a copy of the ledger L1
associated with C1. Peer node P2 maintains a copy of the ledger L1 associated
with C1 and a copy of ledger L2 associated with C2. Peer node P3 maintains a
copy of the ledger L2 associated with C2.The network is governed according to
policy rules specified in network configuration NC4, the network is under the
control of organizations R1 and R4.Channel C1 is governed according to the
policy rules specified in channel configuration CC1; the channel is under the
control of organizations R1 and R2.  Channel C2 is governed according to the
policy rules specified in channel configuration CC2; the channel is under the
control of organizations R2 and R3. There is an ordering service O4 that
services as a network administration point for N, and uses the system channel.
The ordering service also supports application channels C1 and C2, for the
purposes of transaction ordering into blocks for distribution. Each of the four
organizations has a preferred Certificate Authority.*

*R1、R2、R3和R4，四个组织共同决定创建并使用一个Hyperledger Fabric网络，并写入协议中。R4被指定为该网络的发起者——他被授权来创建该网络的初始版本。R4没有要在网络上进行商业交易的目的。R1和R2需要在整个网络中有私密通信，R2和R3也许要私密通信。R1有个客户端应用可以在Channel C1上发起交易。R2有客户端应用可以在Channel C1和C2上发起交易。 R3的客户端应用可以在C2上发起交易。对等节点P1维护一份Channel C1的账本L1的副本。对等节点P2维护一份Channel C1的账本L1的副本和一份Channel C2的账本L2的副本。对等节点P3维护一份Channel C的账本L2的副本。网络根据网络配置文件NC4来治理，网络在R1和R4的控制之下。Channel C1的治理是依据Channel配置CC1中规定的策略原则；并在R1和R2的控制下。 Channel C2的治理是依据Channel配置CC2中规定的策略原则，且在R2和R3的控制之下。排序服务O4作为网络N的网络管理点，并且使用系统Channel。排序服务同时也支持应用Channel C1和C2，目的是将交易排序打包到区块链中以待分发。每个组织都有自己的认证机构*

## Creating the Network
## 创建网络
Let's start at the beginning by creating the basis for the network:
我们从开始创建网络第一步开始。
![network.creation](./network.diagram.2.png)

*The network is formed when an orderer is started. In our example network, N,
the ordering service comprising a single node, O4, is configured according to a
network configuration NC4, which gives administrative rights to organization
R4. At the network level, Certificate Authority CA4 is used to dispense
identities to the administrators and network nodes of the R4 organization.*

*当一个orderer启动时，网络就形成了。在我们的示例网络N中，排序服务有一个节点O4组成，根据网络配置NC4来配置，其中管理权限被赋予组织R4.在网络层，证书颁发机构CA4用来向网络管理员和组织R4的网络节点颁发证书。*

We can see that the first thing that defines a **network, N,** is an **ordering
service, O4**. It's helpful to think of the ordering service as the initial
administration point for the network. As agreed beforehand, O4 is initially
configured and started by an administrator in organization R4, and hosted in R4.
The configuration NC4 contains the policies that describe the starting set of
administrative capabilities for the network. Initially this is set to only give
R4 rights over the network. This will change, as we'll see later, but for now R4
is the only member of the network.

我们可以看到，定义**网络N**的第一件事情是一个**排序服务,O4**。把排序服务当成网络初始管理点是很有帮助的。正如之前达成的协议那样，O4是由组织R4的一个管理员首次配置和启动的，并且主机由R4管理。NC4的配置中包含了描述该网络的管理功能初始集合的策略。最初只是赋予R4整个网络的权力。这个可以变更，我们后续将会看到，但当前，整个网络中只有R4一个成员。

### Certificate Authorities
### 证书颁发机构
You can also see a Certificate Authority, CA4, which is used to issue
certificates to administrators and network nodes. CA4 plays a key role in our
network because it dispenses X.509 certificates that can be used to identify
components as belonging to organization R4. Certificates issued by CAs
can also be used to sign transactions to indicate that an organization endorses
the transaction result -- a precondition of it being accepted onto the
ledger. Let's examine these two aspects of a CA in a little more detail.

你还看到一个证书颁发机构CA4，用来为管理员和节点颁发证书。 CA4在我们的网络中扮演很重要的角色，因为它颁发X.509证书可以用来识别那些部分是属于组织R4的。 CA颁发的证书也可以用来签发交易来表明有一个组织为该交易的结果背书——交易被账本接受的前置条件。我们接下来详细介绍CA的这两个方面。


Firstly, different components of the blockchain network use certificates to
identify themselves to each other as being from a particular organization.
That's why there is usually more than one CA supporting a blockchain network --
different organizations often use different CAs. We're going to use four CAs in
our network; one of for each organization. Indeed, CAs are so important that
Hyperledger Fabric provides you with a built-in one (called *Fabric-CA*) to help
you get going, though in practice, organizations will choose to use their own
CA.

首先，区块链网络的不同部分用证书来想别人证明他们自己是来自于某个组织的。这也是为什么一般情况下都是由多个的CA来支撑一个区块链网络——不同的组织有不同的CA。在我们的示例网路中我们将用4个CA，每个组织一个。事实上，CA是如此重要以至于Hyperledger Fabric为你提供了一个内置CA（叫 *Fabric-CA*）来帮助你在实践中走通。组织可以选择使用自己的CA。

The mapping of certificates to member organizations is achieved by via
a structure called a
[Membership Services Provider (MSP)](../glossary.html#membership-services).
Network configuration NC4 uses a named
MSP to identify the properties of certificates dispensed by CA4 which associate
certificate holders with organization R4. NC4 can then use this MSP name in
policies to grant actors from R4 particular
rights over network resources. An example of such a policy is to identify the
administrators in R4 who can add new member organizations to the network. We
don't show MSPs on these diagrams, as they would just clutter them up, but they
are very important.

证书和组织机构之间的映射关系被通过一个叫做 [Membership Services Provider(MSP)](../glossary.html#membership-services).
网络配置NC4是使用一个命名的MSP来识别CA4颁发的证书中的属性，用来将证书的所有者与组织R4关联起来。NC4接下来可以在策略中使用该MSP的名字对R4的角色进行授权访问特定的网络资源。例如，一个策略是用来识别R4的管理员，拥有为网络创建组织成员的权限。我们没有把MSP画在图片中， as they would just clutter them up, 但是他们非常重要。

Secondly, we'll see later how certificates issued by CAs are at the heart of the
[transaction](../glossary.html#transaction) generation and validation process.
Specifically, X.509 certificates are used in client application
[transaction proposals](../glossary.html#proposal) and smart contract
[transaction responses](../glossary.html#response) to digitally sign
[transactions](../glossary.html#transaction).  Subsequently the network nodes
who host copies of the ledger verify that transaction signatures are valid
before accepting transactions onto the ledger.

然后，我们将看到CA颁发的证书是如何在交易[transaction](../glossary.html#transaction)的生成和验证的过程中起到核心作用的。尤其是X.509证书被使用在客户端应用程序交易提案[transaction proposals](../glossary.html#proposal)和在智能合约[transaction responses](../glossary.html#response) 中来对交易[transactions](../glossary.html#transaction)进行数字签名。最后记录账本的网络节点在验证交易签名有效之后才接受这笔交易并记入账本。

Let's recap the basic structure of our example blockchain network. There's a
resource, the network N, accessed by a set of users defined by a Certificate
Authority CA4, who have a set of rights over the resources in the network N as
described by policies contained inside a network configuration NC4.  All of this
is made real when we configure and start the ordering service node O4.

我们来概括总结一下我们的示例区块链网络的基本结构。网络中有一个资源，网络N，被一组由证书颁发机构 CA4定义的用户访问， CA4拥有一组在network N之上的权力，被定义成策略，在网络配置NC4中定义。当我们配置并启动排序服务节点 O4时，所有这些都会变成现实。


## Adding Network Administrators
## 增加网络管理员

NC4 was initially configured to only allow R4 users administrative rights over
the network. In this next phase, we are going to allow organization R1 users to
administer the network. Let's see how the network evolves:
NC4初始化配置只允许R4用户有管理整个网络的权力。在下一步中，我们将允许R1用户来管理网络。我们看看网络是如何演进的：

![network.admins](./network.diagram.2.1.png)

*Organization R4 updates the network configuration to make organization R1 an
administrator too.  After this point R1 and R4 have equal rights over the
network configuration.*

*组织R4更新了网络配置使R1也成为网络管理员。之后R1和R4对整个网络配置拥有同等的权力*

We see the addition of a new organization R1 as an administrator -- R1 and R4
now have equal rights over the network. We can also see that certificate
authority CA1 has been added -- it can be used to identify users from the R1
organization. After this point, users from both R1 and R4 can administer the
network.

我们看到了增加了一个新的组织R1作为管理员——R1和4现在对整个网络拥有共同的权力。我们可以看到证书颁发机构CA1也加进来了——它可以用来识别组织R1的用户。这之后，来自R1和R4的用户都可以管理网络。


Although the orderer node, O4, is running on R4's infrastructure, R1 has shared
administrative rights over it, as long as it can gain network access. It means
that R1 or R4 could update the network configuration NC4 to allow the R2
organization a subset of network operations.  In this way, even though R4 is
running the ordering service, and R1 has full administrative rights over it, R2
has limited rights to create new consortia.

尽管排序节点O4是运行在R4的基础设施上，R1分享了对它的管理权限，只要它可以获得网络访问。意味着，R1和R4可以更新网络配置NC4来允许组织R2作为网络操作的子集。如此，及时R4运行排序服务，并且R1拥有全部管理权限，R2只拥有有限的权力来创建新的联盟。

In its simplest form, the ordering service is a single node in the network, and
that's what you can see in the example. Ordering services are usually
multi-node, and can be configured to have different nodes in different
organizations. For example, we might run O4 in R4 and connect it to O2, a
separate orderer node in organization R1.  In this way, we would have a
multi-site, multi-organization administration structure.

在这种最简单的形式下，排序服务在网络中只是一个单独的节点，这就是你在例子中能看到的。排序服务通常是多节点的，并且可以配置到不同组织中的不同节点上。例如，我们可以运行O4在R4上并把它连接到O2上，一个运行在组织R1上的独立的排序节点。通过这种方式我们可以有多站点、多组织管理架构。

We'll discuss the ordering service a little more [later in this
topic](#the-ordering-service), but for now just think of the ordering service as
an administration point which provides different organizations controlled access
to the network.

我们将在后续章节中多讨论一下排序服务 [later in this
topic](#the-ordering-service)，单现在，我们暂且把排序服务作为一个管理点，管理不同组织的控制访问权限。

## Defining a Consortium
## 定义一个联盟
Although the network can now be administered by R1 and R4, there is very little
that can be done. The first thing we need to do is define a consortium. This
word literally means "a group with a shared destiny", so it's an appropriate
choice for a set of organizations in a blockchain network.

尽管网络现在可以被R1和R4管理，他们能做的事情很有限。我们需要首先做一件事情——定义联盟。这个词语字面上的意思是“一个有共同使命的组织”，因此它将是区块链网络中的一组组织的合适选择。

Let's see how a consortium is defined:
让我们看看一个联盟是如何定义的：

![network.consortium](./network.diagram.3.png)

*A network administrator defines a consortium X1 that contains two members,
the organizations R1 and R2. This consortium definition is stored in the
network configuration NC4, and will be used at the next stage of network
development. CA1 and CA2 are the respective Certificate Authorities for these
organizations.*
*一个网络管理员定义了一个联盟X1包含两个成员，组织R1和组织R2. 这个联盟定义是存储在网络配置NC4中，并且将会被使用在下一阶段的网络开发中。CA1和CA2分别是这两个组织的证书颁发机构*

Because of the way NC4 is configured, only R1 or R4 can create new consortia.
This diagram shows the addition of a new consortium, X1, which defines R1 and R2
as its constituting organizations.  We can also see that CA2 has been added to
identify users from R2. Note that a consortium can have any number of
organizational members -- we have just shown two as it is the simplest
configuration.

由于NC4被配置的方式，只有R1和R4才可以创建新的联盟。该图显示了新联盟X1的添加，定义了R1和R2作为组成组织。我们也可以看到CA2被添加进来用来识别R2的用户。注意一个联盟可以有任意个数的成员——我们只加了两个作为最简单的配置。

Why are consortia important? We can see that a consortium defines the set of
organizations in the network who share a need to **transact** with one another --
in this case R1 and R2. It really makes sense to group organizations together if
they have a common goal, and that's exactly what's happening.

为什么说联盟很重要？我们可以看到一个联盟定义了一组组织在网络中可以共享一个需求来与另外一个组织交易**transact** ————本示例中是R1和R2. 如果组织们有共同的目标，把他们联盟起来非常有意义，这也是实际上发生的事情。

The network, although started by a single organization, is now controlled by a
larger set of organizations.  We could have started it this way, with R1, R2 and
R4 having shared control, but this build up makes it easier to understand.

对于这个网络，尽管从一个但一组织发起的，现在被更大一个组织群里管理。我们也可以一开始让R1、R2和R4有共同享有控制权，但是这种创建方式理解起来更容易。

We're now going to use consortium X1 to create a really important part of a
Hyperledger Fabric blockchain -- **a channel**.

我们现在要使用联盟X1来创建一个Hyperledger Fabric blockchain中一个非常重要的部分————***a Channel*

## Creating a channel for a consortium
## 为联盟创建一个Channel

So let's create this key part of the Fabric blockchain network -- **a channel**.
A channel is a primary communications mechanism by which the members of a
consortium can communicate with each other. There can be multiple channels in a
network, but for now, we'll start with one.

所以让我们来创建Fabric 去亏啊链网络中的这个关键部分——**channel**. Channel是一个主要的通信机制，功过channel联盟中的成员可以相互通信。在一个网络中可以有多个channel，但是现在我们只从一个channel开始讲起。

Let's see how the first channel has been added to the network:
让我们看看第一个channel是如何加入网路的。

![network.channel](./network.diagram.4.png)

*A channel C1 has been created for R1 and R2 using the consortium definition X1.
The channel is governed by a channel configuration CC1, completely separate to
the network configuration.  CC1 is managed by R1 and R2 who have equal rights
over C1. R4 has no rights in CC1 whatsoever.*

*一个Channel C1被使用联盟定义X1为R1和R2创建起来。该Channel被channel配置CC1治理，与网络的配置完全分离。CC1被R1和R2管理，他俩对C1有同等的权力。R4在CC1中没有权力*

The channel C1 provides a private communications mechanism for the consortium
X1. We can see channel C1 has been connected to the ordering service O4 but that
nothing else is attached to it. In the next stage of network development, we're
going to connect components such as client applications and peer nodes. But at
this point, a channel represents the **potential** for future connectivity.

Channel C1为联盟X1提供一个私有通信机制。我们可以看到channel C1已经连接到排序服务O4上了，但是没任何其他连接。在该网络发展的下一步中，我们将要连接其他组件，比如客户端应用程序或者对等节点。但是此刻，一个channel代表了未来连接的**可能**

Even though channel C1 is a part of the network N, it is quite distinguishable
from it. Also notice that organizations R3 and R4 are not in this channel -- it
is for transaction processing between R1 and R2. In the previous step, we saw
how R4 could grant R1 permission to create new consortia. It's helpful to
mention that R4 **also** allowed R1 to create channels! In this diagram, it
could have been organization R1 or R4 who created a channel C1. Again, note
that a channel can have any number of organizations connected to it -- we've
shown two as it's the simplest configuration.

尽管C1是网络N的一部分，它跟网络N由非常不同。同样也要注意组织R3和R4也不再这个Channel中 —— 它是R1和R2之间的交易处理。在前面的步骤中，我们看到R4是如何给R1授权权限来创建联盟的。值得一提的是R4**也**允许R1来创建channels。在这幅图中，可能是R1也可能是R4创建的Channel C1。 注意，一个channel可以有很多组织连接————我们这里演示2个，简单起见。

Again, notice how channel C1 has a completely separate configuration, CC1, to
the network configuration NC4. CC1 contains the policies that govern the
rights that R1 and R2 have over the channel C1 -- and as we've seen, R3 and
R4 have no permissions in this channel. R3 and R4 can only interact with C1 if
they are added by R1 or R2 to the appropriate policy in the channel
configuration CC1. An example is defining who can add a new organization to the
channel. Specifically, note that R4 cannot add itself to the channel C1 -- it
must, and can only, be authorized by R1 or R2.

同时，注意Channel C1是如何拥有一个独立的配置 CC1， 与网络配置NC4完全不同。 CC1包含了策略来治理R1和R2对于Channel C1的权力 ———— 同时我们也看到， R3和R4在该通道中并没有权限。 R3和R4和只有当被R1或者R2添加到Channel C1的配置文件中相应的策略中，才能与C1交互。注意，R4不能把自己加到Channel C1中 —— 它必须也只能被R1和R2授权。

Why are channels so important? Channels are useful because they provide a
mechanism for private communications and private data between the members of a
consortium. Channels provide privacy from other channels, and from the network.
Hyperledger Fabric is powerful in this regard, as it allows organizations to
share infrastructure and keep it private at the same time.  There's no
contradiction here -- different consortia within the network will have a need
for different information and processes to be appropriately shared, and channels
provide an efficient mechanism to do this.  Channels provide an efficient
sharing of infrastructure while maintaining data and communications privacy.

为什么channel如此重要？channel很有用，因为它提供了一种机制，是的成员和联盟之间的私有通信和私有数据共享成为可能。Channel提供和channel之间的隐私，以及和网络之间的隐私。Hyperledger Fabric的强大之处就在于让组织既可以共享基础设施同时又可以保证隐私。此处没有矛盾——网络中的不同联盟有需要保证不同的信息和处理过程合理的共享，Channel就提供了一种有效的机制来实现。Channel提供了有效的共享基础设施同时保证数据和通信的隐私。

We can also see that once a channel has been created, it is in a very real sense
"free from the network". It is only organizations that are explicitly specified
in a channel configuration that have any control over it, from this time forward
into the future. Likewise, any updates to network configuration NC4 from this
time onwards will have no direct effect on channel configuration CC1; for
example if consortia definition X1 is changed, it will not affect the members of
channel C1. Channels are therefore useful because they allow private
communications between the organizations constituting the channel. Moreover, the
data in a channel is completely isolated from the rest of the network, including
other channels.

我们也可以看出来，一旦channel被创建，就真正意义上的“free from the network”。 只有在channel配置中明确表明的组织才有控制权，从现在到永远。不同的是，任何对网络配置NC4的更新，从现在起都不会直接影响channel配置CC1；例如，如果联盟定义X1被修改了，不会影响channel C1的成员。Channels因此很重要，因为他们允许本channel所组成的组织之间进行私密通信。更多，channel中的数据是完全被从网络中隔离的，包括其他channel。

As an aside, there is also a special **system channel** defined for use by the
ordering service.  It behaves in exactly the same way as a regular channel,
which are sometimes called **application channels** for this reason.  We don't
normally need to worry about this channel, but we'll discuss a little bit more
about it [later in this topic](#the-ordering-service).

另一方面，有一个特殊的 **系统Channel** 由排序服务定义的。它跟其他一般channel是一样，这些channel往往被称作**应用Channel**。我们不需担心这个channel，我们后续会讨论[later in this topic](#the-ordering-service).


## Peers and Ledgers
## 对等节点和账本

Let's now start to use the channel to connect the blockchain network and the
organizational components together. In the next stage of network development, we
can see that our network N has just acquired two new components, namely a peer
node P1 and a ledger instance, L1.

我们现在使用channel来把区块链网络和组织连接起来。在区块链网络发展的下一步，我们可以看到我们的网络N只需要两个新组成部分，叫做 对等节点P1和账本实例L1.

![network.peersledger](./network.diagram.5.png)

*A peer node P1 has joined the channel C1. P1 physically hosts a copy of the
ledger L1. P1 and O4 can communicate with each other using channel C1.*
*一个对等节点P1加入channel C1. P1物理上保存了一份账本L1的拷贝。P1和O4可以通过Channel C1来通信*

Peer nodes are the network components where copies of the blockchain ledger are
hosted!  At last, we're starting to see some recognizable blockchain components!
P1's purpose in the network is purely to host a copy of the ledger L1 for others
to access. We can think of L1 as being **physically hosted** on P1, but
**logically hosted** on the channel C1. We'll see this idea more clearly when we
add more peers to the channel.

对等节点是网络的组成部分用来保存区块链账本的副本。最后，我们开始看看一些可识别的区块链组件。P1在网络中的目的只是来保存账本L1的拷贝，供其他人访问。我们可以认为L1是**物理存在**于P1的，但是**逻辑存在**Channel C1. 当有更多的peer加入channel的时候，这个idea就可以理解的更清楚了。

A key part of a P1's configuration is an X.509 identity issued by CA1 which
associates P1 with organization R1. Once P1 is started, it can **join** channel
C1 using the orderer O4. When O4 receives this join request, it uses the channel
configuration CC1 to determine P1's permissions on this channel. For example,
CC1 determines whether P1 can read and/or write information to the ledger L1.

P1配置的关键部分是X.509身份，由与P1关联的CA1所颁发，一旦P1启动了，它就可以通过orderer O4 **加入**channel C1。当O4接手到这个加入请求时，它使用Channel配置CC1来确定P1在这个channel上权限。例如，CC1决定P1是否可以读写账本L1上的信息。

Notice how peers are joined to channels by the organizations that own them, and
though we've only added one peer, we'll see how  there can be multiple peer
nodes on multiple channels within the network. We'll see the different roles
that peers can take on a little later.

注意Peer是如何通过他们所在的组织加入Channel的，并且尽管我们只加了一个peer，我们将看到在网络中有多个channel，多个节点的样子。我们后续将看到部门的peer有不同的角色。

## Applications and Smart Contract chaincode
## 应用程序和智能合约链码

Now that the channel C1 has a ledger on it, we can start connecting client
applications to consume some of the services provided by workhorse of the
ledger, the peer!
现在channel C1有一个账本在上面，我们可以我们开始把用户端程序连接上来，来使用一些账本的workhorse--peer提供的服务。

Notice how the network has grown:
注意，网络是如何增长的。

![network.appsmartcontract](./network.diagram.6.png)

*A smart contract S5 has been installed onto P1.  Client application A1 in
organization R1 can use S5 to access the ledger via peer node P1.  A1, P1 and
O4 are all joined to channel C1, i.e. they can all make use of the
communication facilities provided by that channel.*

*智能合约S5安装在P1上。客户端应用程序A1属于组织R1可以使用S5来访问账本，通过P1节点。A1，P1和O4都连接到了Channel C1上，也就是，他们都可以利用C1提供的通信设施*

In the next stage of network development, we can see that client application A1
can use channel C1 to connect to specific network resources -- in this case A1
can connect to both peer node P1 and orderer node O4. Again, see how channels
are central to the communication between network and organization components.
Just like peers and orderers, a client application will have an identity that
associates it with an organization.  In our example, client application A1 is
associated with organization R1; and although it is outside the Fabric
blockchain network, it is connected to it via the channel C1.

网络发展的下一步中，我们将看到客户应用程序A1可以使用channel C1来连接到特定的网络资源上————在这种情况下A1可以连接到对等节点P1和排序服务节点O4上。同时，看看channel是如何在网络和组织组件之间的通信中是如何占据核心地位的。就像Peers和orderers一样，客户端应用程序将拥有一个身份，与某个组织关联。 在我们的例子中，客户端应用程序A1是与组织R1相关联的；并且就算这个应用程序不在Fabric区块链网络中，它是通过channel C1连接到网络上的。

It might now appear that A1 can access the ledger L1 directly via P1, but in
fact, all access is managed via a special program called a smart contract
chaincode, S5. Think of S5 as defining all the common access patterns to the
ledger; S5 provides a well-defined set of ways by which the ledger L1 can
be queried or updated. In short, client application A1 has to go through smart
contract S5 to get to ledger L1!

现在可能看起来A1是通过P1来访问账本L1的，但是，事实上，所有的访问都是通过一中特殊的程序来控制的，它叫做智能合约链码，S5. 可以认为S5是定义了所有的访问账本的公共模式；S5提供了一套well-defined的方式来查询和更新账本L1。简而言之，客户端应用程序A1需要通过智能合约S5来访问账本L1.

Smart contract chaincodes can be created by application developers in each
organization to implement a business process shared by the consortium members.
Smart contracts are used to help generate transactions which can be subsequently
distributed to the every node in the network.  We'll discuss this idea a little
later; it'll be easier to understand when the network is bigger. For now, the
important thing to understand is that to get to this point two operations must
have been performed on the smart contract; it must have been **installed**, and
then **instantiated**.

智能合约链码可以由每个组织的应用开发者来创建来实现被联盟成员共享的商业过程。智能合约是用来产生交易的，交易后续可以分发给网络中的每个节点。我们稍后将讨论这个点；当网络更大一些的时候，就可以更好的理解了。对于目前，最重要的事情是去理解在智能合约上执行这些操作之前，有两件事情需要做：合约必须被“安装”，然后被“实例化”

### Installing a smart contract
### 安装一个智能合约

After a smart contract S5 has been developed, an administrator in organization
R1 must [install](../glossary.html#install) it onto peer node P1. This is a
straightforward operation; after it has occurred, P1 has full knowledge of S5.
Specifically, P1 can see the **implementation** logic of S5 -- the program code
that it uses to access the ledger L1. We contrast this to the S5 **interface**
which merely describes the inputs and outputs of S5, without regard to its
implementation.

智能合约S5被开发完毕之后，R1的管理员必须将它安装[install](../glossary.html#install)到节点P1上。这是一个很直接的操作；安装完毕之后，P1完全知晓S5。尤其是，P1可以看到S5的**实现**逻辑————访问账本L1的代码。我们将这个对比S5的**接口**，主要描述S5的输入和输出，而不管它的实现。

When an organization has multiple peers in a channel, it can choose the peers
upon which it installs smart contracts; it does not need to install a smart
contract on every peer.

当一个组织在一个channel中有多个节点的时候，它可以选择在哪个节点上安装智能合约，不需要在每个节点上都安装。

### Instantiating a smart contract
### 实例化一个智能合约

However, just because P1 has installed S5, the other components connected to
channel C1 are unaware of it; it must first be
[instantiated](../glossary.html#instantiate) on channel C1.  In our example,
which only has a single peer node P1, an administrator in organization R1 must
instantiate S5 on channel C1 using P1. After instantiation, every component on
channel C1 is aware of the existence of S5; and in our example it means that S5
can now be [invoked](../glossary.html#invoke) by client application A1!

尽管如此，因为只有P1安装了S5, 连接到C1的其他组成部分是不知道S5的存在的；它必须首先在C1上实例化[instantiated](../glossary.html#instantiate) 。在我们的例子中，只有一个对等节点P1，组织R1的管理员必须哎channel C1上实例化S5。实例化之后，每个channel C1上的组成部分都会知道S5的存在；并且在我们的例子中，它一位置S5现在可以被客户端程序A1调用了 [invoked](../glossary.html#invoke) 

Note that although every component on the channel can now access S5, they are
not able to see its program logic.  This remains private to those nodes who have
installed it; in our example that means P1. Conceptually this means that it's
the smart contract **interface** that is instantiated, in contrast to the smart
contract **implementation** that is installed. To reinforce this idea;
installing a smart contract shows how we think of it being **physically hosted**
on a peer, whereas instantiating a smart contract shows how we consider it
**logically hosted** by the channel.

注意，尽管现在Channel C1上的每个组件都可以访问S5， 他们却无法看到程序的逻辑。只有安装了该智能合约的节点才可以看到；在我们的例子终究是P1. 概念上，这意味着只有智能合约的**接口**被实例化了，相对智能合约的**实现**被安装。为了强调这一点，安装一个智能合约体现了我们是如何理解被一个节点**物理所有**，而实例化一个智能合约则体现了我们认为它被channel**逻辑所有**

### Endorsement policy
### 背书策略
The most important piece of additional information supplied at instantiation is
an [endorsement policy](../glossary.html#endorsement-policy). It describes which
organizations must approve transactions before they will be accepted by other
organizations onto their copy of the ledger. In our sample network, transactions
can be only be accepted onto ledger L1 if R1 or R2 endorse them.

实例化合约时提供的附加信息中最重要的就是背书策略了[endorsement policy](../glossary.html#endorsement-policy).背书策略描述了那些组织必须批准交易，在交易可以被其他组织记录到他们的账本上之前。在我们的实例网络中，交易可以被L1接受，只有当R1或者R2背书之后。

The act of instantiation places the endorsement policy in channel configuration
CC1; it enables it to be accessed by any member of the channel. You can read
more about endorsement policies in the
[transaction flow topic](../txflow.html).

实例化动作将背书策略放到了channel配置CC1中，它让channel的其他memeber都可以访问该合约。你可以在[transaction flow topic](../txflow.html)中了解更多关于背书策略。

### Invoking a smart contract
### 调用智能合约

Once a smart contract has been installed on a peer node and instantiated on a
channel it can be [invoked](../glossary.html#invoke) by a client application.
Client applications do this by sending transaction proposals to peers owned by
the organizations specified by the smart contract endorsement policy. The
transaction proposal serves as input to the smart contract, which uses it to
generate an endorsed transaction response, which is returned by the peer node to
the client application.

一旦一个智能合约在一个peer上被安装，且在一个channel上被实例化，它就可以被客户端应用程序调用了。客户端应用程序调用智能合约的过程是通过向该合约的背书策略中描述的组织所拥有的节点发送交易提案。交易提案是智能合约的输入，智能合约使用它来产生一个背书的交易响应，由peer节点返回给客户端应用程序。

It's these transactions responses that are packaged together with the
transaction proposal to form a fully endorsed transaction, which can be
distributed to the entire network.  We'll look at this in more detail later  For
now, it's enough to understand how applications invoke smart contracts to
generate endorsed transactions.

交易响应和交易提案一起打包形成一个完全背书的可以全网络分发的交易。我们后续将详细探讨，现在，你已经可以理解应用是如何调用智能合约并生成背书交易的了。

By this stage in network development we can see that organization R1 is fully
participating in the network. Its applications -- starting with A1 -- can access
the ledger L1 via smart contract S5, to generate transactions that will be
endorsed by R1, and therefore accepted onto the ledger because they conform to
the endorsement policy.

网络发展到这一步，我们可以看到组织R1是充分参与的网络中的。它的应用程序 ———— 从A1开始 ———— 可以访问账本L1通过智能合约S5，来产生交易由R1背书，之后被账本接受，因为它们符合背书策略。

## Network completed
## 网络完成

Recall that our objective was to create a channel for consortium X1 --
organizations R1 and R2. This next phase of network development sees
organization R2 add its infrastructure to the network.

回想我们的目标是为联盟X1——组织R1和R2，创建一个channel。网络发展的下一个阶段将见证组织R2将它的设施加入到网络中。

Let's see how the network has evolved:
让我一起来卡看网络是如何演进的：

![network.grow](./network.diagram.7.png)

*The network has grown through the addition of infrastructure from
organization R2. Specifically, R2 has added peer node P2, which hosts a copy of
ledger L1, and chaincode S5. P2 has also joined channel C1, as has application
A2. A2 and P2 are identified using certificates from CA2. All of this means
that both applications A1 and A2 can invoke S5 on C1 either using peer node P1
or P2.*

*网络通过添加组织R2的设施而增长。尤其是R2增加了对等节点P2，也保存了账本L1的部分，和S5.P2也加如了channel C1， 并拥有一个应用程序A2. A2和P2通过CA2发的证书来识别。所有的这些都意味着应用程序A1和A2都可以调用C1上的S5，通过节点P1或者P2*

We can see that organization R2 has added a peer node, P2, on channel C1. P2
also hosts a copy of the ledger L1 and smart contract S5. We can see that R2 has
also added client application A2 which can connect to the network via channel
C1.  To achieve this, an administrator in organization R2 has created peer node
P2 and joined it to channel C1, in the same way as an administrator in R1.

我们看到，组织R2添加了一个节点P2到channel C1上。P2也维护了一份账本L1的拷贝和智能合约S5.我们可以看到R2也添加了客户端应用程序A2到网络中，通过连接到Channel C1上。要完成这些，组织R2的管理员需要创建一个对等节点P2，并把它连接到channel C1上，像R1的管理员一样。

We have created our first operational network! At this stage in network
development, we have a channel in which organizations R1 and R2 can fully
transact with each other.  Specifically, this means that applications A1 and A2
can generate transactions using smart contract S5 and ledger L1 on channel C1.

我们已经创建了我们的第一个可操作网络。网络发展到这一步，我们有一个channel，里面有组织R1和R2可以充分彼此交易。尤其是，这意味着应用程序A1和A2可以产生交易，使用智能合约S5和L1在channel C1上。

### Generating and accepting transactions
### 生成和接受交易

In contrast to peer nodes, which always host a copy of the ledger, we see that
there are two different kinds of peer nodes; those which host smart contracts
and those which do not. In our network, every peer hosts a copy of the smart
contract, but in larger networks, there will be many more peer nodes that do not
host a copy of the smart contract. A peer can only *run* a smart contract if it
is installed on it, but it can *know* about the interface of a smart contract by
being connected to a channel.

与保存账本副本的对等节点相比，我们还可以看到两种节点，一种是维护智能合约的节点，另一种不维护智能合约。在我们的网路哦中，每个节点都维护了一个智能合约的副本，但是在账本网络中，有很多节点也不维护智能合约。 只有安装了智能合约的节点才能*运行*智能合约，但是它可以*知道*一个智能合约的接口，通过连接到一个channel上。

You should not think of peer nodes which do not have smart contracts installed
as being somehow inferior. It's more the case that peer nodes with smart
contracts have a special power -- to help **generate** transactions. Note that
all peer nodes can **validate** and subsequently **accept** or **reject**
transactions onto their copy of the ledger L1. However, only peer nodes with a
smart contract installed can take part in the process of transaction
**endorsement** which is central to the generation of valid transactions.

你不可以认为一个不安装智能合约的节点对比安装了智能合约的节点是一个低级节点。安装了智能合约的节点有一个特殊的能力————帮助**生成**交易。注意所有的对等节点都可以**验证**和后续**接受**或者**拒绝**将交易记录到它们的账本L1的不笨上。但是，只有安装了智能合约的对等节点才能**背书**交易，这一点对产生一个有效的交易非常关键。


We don't need to worry about the exact details of how transactions are
generated, distributed and accepted in this topic -- it is sufficient to
understand that we have a blockchain network where organizations R1 and R2 can
share information and processes as ledger-captured transactions.  We'll learn a
lot more about transactions, ledgers, smart contracts in other topics.

在本讨论正，我们不必纠结一个交易是如何产生的、分发的、接受的细节————我们知道在我们的区块链网络中两个组织R1和R2可以共享信息和过程做为账本记录的交易就足够了。我们将在其他话题中了解更多的交易、账本、智能合约。

### Types of peers
### 节点类型

In Hyperledger Fabric, while all peers are the same, they can assume multiple
roles depending on how the network is configured.  We now have enough
understanding of a typical network topology to describe these roles.
在Hyperledger Fabric中，所有的节点都是一样的，但是依据网路的配置的不同，它们有不同的角色。我们已经有了对网络拓扑结构足够的了解来描述这些角色

  * [*Committing peer*](../glossary.html#commitment). Every peer node in a
    channel is a committing peer. It receives blocks of generated transactions,
    which are subsequently validated before they are committed to the peer
    node's copy of the ledger as an append operation.
  * 提交节点[*Committing peer*](../glossary.html#commitment)。在channel中的每个对等节点都是一个提交节点。它接收生成的交易的     blocks，它们在被提交到节点并添加到账本副本之前会被验证。

  * [*Endorsing peer*](../glossary.html#endorsement). Every peer with a smart
    contract *can* be an endorsing peer if it has a smart contract installed.
    However, to actually *be* an endorsing peer, the smart contract on the peer
    must be used by a client application to generate a digitally signed
    transaction response. The term *endorsing peer* is an explicit reference to
    this fact.
  * 背书节点[*Endorsing peer*](../glossary.html#endorsement).每个安装了智能合约的节点都*可以*作为背书节点。但是，*成为*一个     真正的背书节点，它的智能合约必须被客户端应用程需调用产生一个数字签名的交易响应。术语*endorsing peer*是对这一事实的显示引用。
    An endorsement policy for a smart contract identifies the
    organizations whose peer should digitally sign a generated transaction
    before it can be accepted onto a committing peer's copy of the ledger.
    智能合约的背书策略表明了那些组织的peer需要数字签名一个交易，交易节点才能接受。

These are the two major types of peer; there are two other roles a peer can
adopt:
这是两种类型的节点；一个节点还有两种角色。

  * [*Leader peer*](../glossary.html#leading-peer). When an organization has
    multiple peers in a channel, a leader peer is a node which takes
    responsibility for distributing transactions from the orderer to the other
    committing peers in the organization.  A peer can choose to participate in
    static or dynamic leadership selection.
  * [*Leader peer*](../glossary.html#leading-peer). 当一个组织在一个channel中有多个节点时，首节点需要负责把从orderer接收的交易分发给其他的提交节点。一个节点可以选择参与静态或动态leadership选举。

    It is helpful, therefore to think of two sets of peers from leadership
    perspective -- those that have static leader selection, and those with
    dynamic leader selection. For the static set, zero or more peers can be
    configured as leaders. For the dynamic set, one peer will be elected leader
    by the set. Moreover, in the dynamic set, if a leader peer fails, then the
    remaining peers will re-elect a leader.
    这是有用的，因此想想有抗
    

    It means that an organization's peers can have one or more leaders connected
    to the ordering service. This can help to improve resilience and scalability
    in large networks which process high volumes of transactions.

  * [*Anchor peer*](../glossary.html#anchor-peer). If a peer needs to
    communicate with a peer in another organization, then it can use one of the
    **anchor peers** defined in the channel configuration for that organization.
    An organization can have zero or more anchor peers defined for it, and an
    anchor peer can help with many different cross-organization communication
    scenarios.

Note that a peer can be a committing peer, endorsing peer, leader peer and
anchor peer all at the same time! Only the anchor peer is optional -- for all
practical purposes there will always be a leader peer and at least one
endorsing peer and at least one committing peer.

### Install not instantiate

In a similar way to organization R1, organization R2 must install smart contract
S5 onto its peer node, P2. That's obvious -- if applications A1 or A2 wish to
use S5 on peer node P2 to generate transactions, it must first be present;
installation is the mechanism by which this happens. At this point, peer node P2
has a physical copy of the smart contract and the ledger; like P1, it can both
generate and accept transactions onto its copy of ledger L1.

However, in contrast to organization R1, organization R2 does not need to
instantiate smart contract S5 on channel C1. That's because S5 has already been
instantiated on the channel by organization R1. Instantiation only needs to
happen once; any peer which subsequently joins the channel knows that smart
contract S5 is available to the channel. This fact reflects the fact that ledger
L1 and smart contract really exist in a physical manner on the peer nodes, and a
logical manner on the channel; R2 is merely adding another physical instance of
L1 and S5 to the network.

In our network, we can see that channel C1 connects two client applications, two
peer nodes and an ordering service.  Since there is only one channel, there is
only one **logical** ledger with which these components interact. Peer nodes P1
and P2 have identical copies of ledger L1. Copies of smart contract S5 will
usually be identically implemented using the same programming language, but
if not, they must be semantically equivalent.

We can see that the careful addition of peers to the network can help support
increased throughput, stability, and resilience. For example, more peers in a
network will allow more applications to connect to it; and multiple peers in an
organization will provide extra resilience in the case of planned or unplanned
outages.

It all means that it is possible to configure sophisticated topologies which
support a variety of operational goals -- there is no theoretical limit to how
big a network can get. Moreover, the technical mechanism by which peers within
an individual organization efficiently discover and communicate with each other --
the [gossip protocol](../gossip.html#gossip-protocol) -- will accommodate a
large number of peer nodes in support of such topologies.

The careful use of network and channel policies allow even large networks to be
well-governed.  Organizations are free to add peer nodes to the network so long
as they conform to the policies agreed by the network. Network and channel
policies create the balance between autonomy and control which characterizes a
de-centralized network.

## Simplifying the visual vocabulary

We’re now going to simplify the visual vocabulary used to represent our sample
blockchain network. As the size of the network grows, the lines initially used
to help us understand channels will become cumbersome. Imagine how complicated
our diagram would be if we added another peer or client application, or another
channel?

That's what we're going to do in a minute, so before we do, let's
simplify the visual vocabulary. Here's a simplified representation of the network we've developed so far:

![network.vocabulary](./network.diagram.8.png)

*The diagram shows the facts relating to channel C1 in the network N as follows:
Client applications A1 and A2 can use channel C1 for communication with peers
P1 and P2, and orderer O4. Peer nodes P1 and P2 can use the communication
services of channel C1. Ordering service O4 can make use of the communication
services of channel C1. Channel configuration CC1 applies to channel C1.*

Note that the network diagram has been simplified by replacing channel lines
with connection points, shown as blue circles which include the channel number.
No information has been lost. This representation is more scalable because it
eliminates crossing lines. This allows us to more clearly represent larger
networks. We've achieved this simplification by focusing on the connection
points between components and a channel, rather than the channel itself.

## Adding another consortium definition

In this next phase of network development, we introduce organization R3.  We're
going to give organizations R2 and R3 a separate application channel which
allows them to transact with each other.  This application channel will be
completely separate to that previously defined, so that R2 and R3 transactions
can be kept private to them.

Let's return to the network level and define a new consortium, X2, for R2 and
R3:

![network.consortium2](./network.diagram.9.png)

*A network administrator from organization R1 or R4 has added a new consortium
definition, X2, which includes organizations R2 and R3. This will be used to
define a new channel for X2.*

Notice that the network now has two consortia defined: X1 for organizations R1
and R2 and X2 for organizations R2 and R3. Consortium X2 has been introduced in
order to be able to create a new channel for R2 and R3.

A new channel can only be created by those organizations specifically identified
in the network configuration policy, NC4, as having the appropriate rights to do
so, i.e. R1 or R4. This is an example of a policy which separates organizations
that can manage resources at the network level versus those who can manage
resources at the channel level. Seeing these policies at work helps us
understand why Hyperledger Fabric has a sophisticated **tiered** policy
structure.

In practice, consortium definition X2 has been added to the network
configuration NC4. We discuss the exact mechanics of this operation elsewhere in
the documentation.

## Adding a new channel

Let's now use this new consortium definition, X2, to create a new channel, C2.
To help reinforce your understanding of the simpler channel notation, we've used
both visual styles -- channel C1 is represented with blue circular end points,
whereas channel C2 is represented with red connecting lines:

![network.channel2](./network.diagram.10.png)

*A new channel C2 has been created for R2 and R3 using consortium definition X2.
The channel has a channel configuration CC2, completely separate to the network
configuration NC4, and the channel configuration CC1. Channel C2 is managed by
R2 and R3 who have equal rights over C2 as defined by a policy in CC2. R1 and
R4 have no rights defined in CC2 whatsoever.*

The channel C2 provides a private communications mechanism for the consortium
X2. Again, notice how organizations united in a consortium are what form
channels. The channel configuration CC2 now contains the policies that govern
channel resources, assigning management rights to organizations R2 and R3 over
channel C2. It is managed exclusively by R2 and R3; R1 and R4 have no power in
channel C2. For example, channel configuration CC2 can subsequently be updated
to add organizations to support network growth, but this can only be done by R2
or R3.

Note how the channel configurations CC1 and CC2 remain completely separate from
each other, and completely separate from the network configuration, NC4. Again
we're seeing the de-centralized nature of a Hyperledger Fabric network; once
channel C2 has been created, it is managed by organizations R2 and R3
independently to other network elements. Channel policies always remain separate
from each other and can only be changed by the organizations authorized to do so
in the channel.

As the network and channels evolve, so will the network and channel
configurations. There is a process by which this is accomplished in a controlled
manner -- involving configuration transactions which capture the change to these
configurations. Every configuration change results in a new configuration block
transaction being generated, and [later in this topic](#the-ordering-serivce),
we'll see how these blocks are validated and accepted to create updated network
and channel configurations respectively.

### Network and channel configurations

Throughout our sample network, we see the importance of network and channel
configurations. These configurations are important because they encapsulate the
**policies** agreed by the network members, which provide a shared reference for
controlling access to network resources. Network and channel configurations also
contain **facts** about the network and channel composition, such as the name of
consortia and its organizations.

For example, when the network is first formed using the ordering service node
O4, its behaviour is governed by the network configuration NC4. The initial
configuration of NC4 only contains policies that permit organization R4 to
manage network resources. NC4 is subsequently updated to also allow R1 to manage
network resources. Once this change is made, any administrator from organization
R1 or R4 that connects to O4 will have network management rights because that is
what the policy in the network configuration NC4 permits. Internally, each node
in the ordering service records each channel in the network configuration, so
that there is a record of each channel created, at the network level.

It means that although ordering service node O4 is the actor that created
consortia X1 and X2 and channels C1 and C2, the **intelligence** of the network
is contained in the network configuration NC4 that O4 is obeying.  As long as O4
behaves as a good actor, and correctly implements the policies defined in NC4
whenever it is dealing with network resources, our network will behave as all
organizations have agreed. In many ways NC4 can be considered more important
than O4 because, ultimately, it controls network access.

The same principles apply for channel configurations with respect to peers. In
our network, P1 and P2 are likewise good actors. When peer nodes P1 and P2 are
interacting with client applications A1 or A2 they are each using the policies
defined within channel configuration CC1 to control access to the channel C1
resources.

For example, if A1 wants to access the smart contract chaincode S5 on peer nodes
P1 or P2, each peer node uses its copy of CC1 to determine the operations that
A1 can perform. For example, A1 may be permitted to read or write data from the
ledger L1 according to policies defined in CC1. We'll see later the same pattern
for actors in channel and its channel configuration CC2.  Again, we can see that
while the peers and applications are critical actors in the network, their
behaviour in a channel is dictated more by the channel configuration policy than
any other factor.

Finally, it is helpful to understand how network and channel configurations are
physically realized. We can see that network and channel configurations are
logically singular -- there is one for the network, and one for each channel.
This is important; every component that accesses the network or the channel must
have a shared understanding of the permissions granted to different
organizations.

Even though there is logically a single configuration, it is actually replicated
and kept consistent by every node that forms the network or channel. For
example, in our network peer nodes P1 and P2 both have a copy of channel
configuration CC1, and by the time the network is fully complete, peer nodes P2
and P3 will both have a copy of channel configuration CC2. Similarly ordering
service node O4 has a copy of the network configuration, but in a [multi-node
configuration](#the-ordering-service), every ordering service node will have its
own copy of the network configuration.

Both network and channel configurations are kept consistent using the same
blockchain technology that is used for user transactions -- but for
**configuration** transactions. To change a network or client configuration, an
administrator must submit a configuration transaction to change the network or
channel configuration. It must be signed by the organizations identified in the
appropriate policy as being responsible for configuration change. This policy is
called the **mod_policy** and we'll [discuss it later](#changing-policy).

Indeed, the ordering service nodes operate a mini-blockchain, connected via the
**system channel** we mentioned earlier. Using the system channel ordering
service nodes distribute network configuration transactions. These transactions
are used to co-operatively maintain a consistent copy of the network
configuration at each ordering service node. In a similar way, peer nodes in an
**application channel** can distribute channel configuration transactions.
Likewise, these transactions are used to maintain a consistent copy of the
channel configuration at each peer node.

This balance between objects that are logically singular, by being physically
distributed is a common pattern in Hyperledger Fabric. Objects like network
configurations, that are logically single, turn out to be physically replicated
among a set of ordering services nodes for example. We also see it with channel
configurations, ledgers, and to some extent smart contracts which are installed
in multiple places but whose interfaces exist logically at the channel level.
It's a pattern you see repeated time and again in Hyperledger Fabric, and
enables Hyperledger Fabric to be both de-centralized and yet manageable at the
same time.

## Adding another peer

Now that organization R3 is able to fully participate in channel C2, let's add
its infrastructure components to the channel.  Rather than do this one component
at a time, we're going to add a peer, its local copy of a ledger, a smart
contract and a client application all at once!

Let's see the network with organization R3's components added:

![network.peer2](./network.diagram.11.png)

*The diagram shows the facts relating to channels C1 and C2 in the network N as
follows: Client applications A1 and A2 can use channel C1 for communication
with peers P1 and P2, and ordering service O4; client applications A3 can use
channel C2 for communication with peer P3 and ordering service O4. Ordering
service O4 can make use of the communication services of channels C1 and C2.
Channel configuration CC1 applies to channel C1, CC2 applies to channel C2.*

First of all, notice that because peer node P3 is connected to channel C2, it
has a **different** ledger -- L2 -- to those peer nodes using channel C1.  The
ledger L2 is effectively scoped to channel C2. The ledger L1 is completely
separate; it is scoped to channel C1.  This makes sense -- the purpose of the
channel C2 is to provide private communications between the members of the
consortium X2, and the ledger L2 is the private store for their transactions.

In a similar way, the smart contract S6, installed on peer node P3, and
instantiated on channel C2, is used to provide controlled access to ledger L2.
Application A3 can now use channel C2 to invoke the services provided by smart
contract S6 to generate transactions that can be accepted onto every copy of the
ledger L2 in the network.

At this point in time, we have a single network that has two completely separate
channels defined within it.  These channels provide independently managed
facilities for organizations to transact with each other. Again, this is
de-centralization at work; we have a balance between control and autonomy. This
is achieved through policies which are applied to channels which are controlled
by, and affect, different organizations.

## Joining a peer to multiple channels

In this final stage of network development, let's return our focus to
organization R2. We can exploit the fact that R2 is a member of both consortia
X1 and X2 by joining it to multiple channels:

![network.multichannel](./network.diagram.12.png)

*The diagram shows the facts relating to channels C1 and C2 in the network N as
follows: Client applications A1 can use channel C1 for communication with peers
P1 and P2, and ordering service O4; client application A2 can use channel C1
for communication with peers P1 and P2 and channel C2 for communication with
peers P2 and P3 and ordering service O4; client application A3 can use channel
C2 for communication with peer P3 and ordering service O4. Ordering service O4
can make use of the communication services of channels C1 and C2. Channel
configuration CC1 applies to channel C1, CC2 applies to channel C2.*

We can see that R2 is a special organization in the network, because it is the
only organization that is a member of two application channels!  It is able to
transact with organization R1 on channel C1, while at the same time it can also
transact with organization R3 on a different channel, C2.

Notice how peer node P2 has smart contract S5 installed for channel C1 and smart
contract S6 installed for channel C2. Peer node P2 is a full member of both
channels at the same time via different smart contracts for different ledgers.

This is a very powerful concept -- channels provide both a mechanism for the
separation of organizations, and a mechanism for collaboration between
organizations. All the while, this infrastructure is provided by, and shared
between, a set of independent organizations.

It is also important to note that peer node P2's behaviour is controlled very
differently depending upon the channel in which it is transacting. Specifically,
the policies contained in channel configuration CC1 dictate the operations
available to P2 when it is transacting in channel C1, whereas it is the policies
in channel configuration CC2 that control P2's behaviour in channel C2.

Again, this is desirable -- R2 and R1 agreed the rules for channel C1, whereas
R2 and R3 agreed the rules for channel C2. These rules were captured in the
respective channel policies -- they can and must be used by every
component in a channel to enforce correct behaviour, as agreed.

Similarly, we can see that client application A2 is now able to transact on
channels C1 and C2.  And likewise, it too will be governed by the policies in
the appropriate channel configurations.  As an aside, note that client
application A2 and peer node P2 are using a mixed visual vocabulary -- both
lines and connections. You can see that they are equivalent; they are visual
synonyms.

### The ordering service
### 排序服务
The observant reader may notice that the ordering service node appears to be a
centralized component; it was used to create the network initially, and connects
to every channel in the network.  Even though we added R1 and R4 to the network
configuration policy NC4 which controls the orderer, the node was running on
R4's infrastructure. In a world of de-centralization, this looks wrong!

善于观察的读者已经发现，排序服务节点是是一个核心组件；它是用来最初创建网络的，并且连接到网络中每个channel。即使我们后来添加了R1和R4到网络配置策略NC4控制orderer，节点是运行在R4的设施上。在去中心化的世界中，这看起来是错的。


Don't worry! Our example network showed the simplest ordering service
configuration to help you understand the idea of a network administration point.
In fact, the ordering service can itself too be completely de-centralized!  We
mentioned earlier that an ordering service could be comprised of many individual
nodes owned by different organizations, so let's see how that would be done in
our sample network.

不要担心，在我们的实例网络中，演示了简单的排序服务配置，来帮助你理解网络的管理点。实施上，排序服务自身也可以是非常去中心化的！我们之前提到，一个排序服务可以分解成多个独立节点，分属不同的组织，所以我们看看如何在我们的实例网络中实现。

Let's have a look at a more realistic ordering service node configuration:
让我们看看一个更实际的排序服务节点配置。

![network.finalnetwork2](./network.diagram.15.png)

*A multi-organization ordering service.  The ordering service comprises ordering
service nodes O1 and O4. O1 is provided by organization R1 and node O4 is
provided by organization R4. The network configuration NC4 defines network
resource permissions for actors from both organizations R1 and R4.*

*一个多组织的排序服务。排序服务由排序节点O1和排序节点O4组成。O1是有组织R1提供的，O4是由组织R4组成的。网络配置NC4为R1和R4的角色定义了网络资源权限*

We can see that this ordering service completely de-centralized -- it runs in
organization R1 and it runs in organization R4. The network configuration
policy, NC4, permits R1 and R4 equal rights over network resources.  Client
applications and peer nodes from organizations R1 and R4 can manage network
resources by connecting to either node O1 or node O4, because both nodes behave
the same way, as defined by the policies in network configuration NC4. In
practice, actors from a particular organization *tend* to use infrastructure
provided by their home organization, but that's certainly not always the case.

我们可以看到这个排序服务是完全去重新化的 ———— 它在组织R1和R4中运行。网络配置策略NC4，允许R1和R4可以平等管理网络资源。客户端应用程序和R1和R4的对等节点可以管理网络资源，通过连接到O1或者O4上，因为两个节点的行为是一样的，就是在网络配置NC4的策略中。在实践中，actors from a particular organization *tend* to use infrastructure provided by their home organization, but that's certainly not always the case.

### De-centralized transaction distribution
### 去中心化交易分发

As well as being the management point for the network, the ordering service also
provides another key facility -- it is the distribution point for transactions.
The ordering service is the component which gathers endorsed transactions
from applications and orders them into transaction blocks, which are
subsequently distributed to every peer node in the channel. At each of these
committing peers, transactions are recorded, whether valid or invalid, and their
local copy of the ledger updated appropriately.

作为整个网络的管理点的同时，排序服务同时也提供另外一个关键功能——那就是交易的分发点。排序服务组件从应用程序哪里收集背书的交易并将其排序打包到交易块中，然后把它们分布到channel的各个对等节点中。在每个提交节点，交易被记录，无论有效还是无效，它们的本地账本都得到更新。

Notice how the ordering service node O4 performs a very different role for the
channel C1 than it does for the network N. When acting at the channel level,
O4's role is to gather transactions and distribute blocks inside channel C1. It
does this according to the policies defined in channel configuration CC1. In
contrast, when acting at the network level, O4's role is to provide a management
point for network resources according to the policies defined in network
configuration NC4. Notice again how these roles are defined by different
policies within the channel and network configurations respectively. This should
reinforce to you the importance of declarative policy based configuration in
Hyperledger Fabric. Policies both define, and are used to control, the agreed
behaviours by each and every member of a consortium.



We can see that the ordering service, like the other components in Hyperledger
Fabric, is a fully de-centralized component. Whether acting as a network
management point, or as a distributor of blocks in a channel, its nodes can be
distributed as required throughout the multiple organizations in a network.

### Changing policy
### 变更策略

Throughout our exploration of the sample network, we've seen the importance of
the policies to control the behaviour of the actors in the system. We've only
discussed a few of the available policies, but there are many that can be
declaratively defined to control every aspect of behaviour. These individual
policies are discussed elsewhere in the documentation.
通过我们对整个实例网络的解释，我们已经看到

Most importantly of all, Hyperledger Fabric provides a uniquely powerful policy
that allows network and channel administrators to manage policy change itself!
The underlying philosophy is that policy change is a constant, whether it occurs
within or between organizations, or whether it is imposed by external
regulators. For example, new organizations may join a channel, or existing
organizations may have their permissions increased or decreased. Let's
investigate a little more how change policy is implemented in Hyperledger
Fabric.

They key point of understanding is that policy change is managed by a
policy within the policy itself.  The **modification policy**, or
**mod_policy** for short, is a first class policy within a network or channel
configuration that manages change. Let's give two brief examples of how we've
**already** used mod_policy can be used to manage change in our network!

The first example was when the network was initially set up. At this time, only
organization R4 was allowed to manage the network. In practice, this was
achieved by making R4 the only organization defined in the network configuration
NC4 with permissions to network resources.  Moreover, the mod_policy for NC4
only mentioned organization R4 -- only R4 was allowed to change this
configuration.

We then evolved the network N to also allow organization R1 to administer the
network.  R4 did this by adding R1 to the policies for channel creation and
consortium creation. Because of this change, R1 was able to define the
consortia X1 and X2, and create the channels C1 and C2. R1 had equal
administrative rights over the channel and consortium policies in the network
configuration.

R4 however, could grant even more power over the network configuration to R1! R4
could add R1 to the mod_policy such that R1 would be able to manage change of
the network policy too.

This second power is much more powerful than the first, because now R1 now has
**full control** over the network configuration NC4! This means that R1 can, in
principle remove R4's management rights from the network.  In practice, R4 would
configure the mod_policy such that R4 would need to also approve the change, or
that all organizations in the mod_policy would have to approve the change.
There's lots of flexibility to make the mod_policy as sophisticated as it needs
to be to support whatever change process is required.

This is mod_policy at work -- it has allowed the graceful evolution of a basic
configuration into a sophisticated one. All the time this has occurred with the
agreement of all organization involved. The mod_policy behaves like every other
policy inside a network or channel configuration; it defines a set of
organizations that are allowed to change the mod_policy itself.

We've only scratched the surface of the power of policies and mod_policy in
particular in this subsection. It is discussed at much more length in the policy
topic, but for now let's return to our finished network!

## Network fully formed

Let's recap what our network looks like using a consistent visual vocabulary.
We've re-organized it slightly using our more compact visual syntax, because it
better accommodates larger topologies:

![network.finalnetwork2](./network.diagram.14.png)

*In this diagram we see that the Fabric blockchain network consists of two
application channels and one ordering channel. The organizations R1 and R4 are
responsible for the ordering channel, R1 and R2 are responsible for the blue
application channel while R2 and R3 are responsible for the red application
channel. Client applications A1 is an element of organization R1, and CA1 is
its certificate authority. Note that peer P2 of organization R2 can use the
communication facilities of the blue and the red application channel. Each
application channel has its own channel configuration, in this case CC1 and
CC2. The channel configuration of the system channel is part of the network
configuration, NC4.*

We're at the end of our conceptual journey to build a sample Hyperledger Fabric
blockchain network. We've created a four organization network with two channels
and three peer nodes, with two smart contracts and an ordering service.  It is
supported by four certificate authorities. It provides ledger and smart contract
services to three client applications, who can interact with it via the two
channels. Take a moment to look through the details of the network in the
diagram, and feel free to read back through the topic to reinforce your
knowledge, or go to a more detailed topic.

### Summary of network components

Here's a quick summary of the network components we've discussed:

* [Ledger](../glossary.html#ledger). One per channel. Comprised of the
  [Blockchain](../glossary.html#block) and
  the [World state](../glossary.html#world-state)
* [Smart contract](../glossary.html#smart-contract) (aka chaincode)
* [Peer nodes](../glossary.html#peer)
* [Ordering service](../glossary.html#ordering-service)
* [Channel](../glossary.html#channel)
* [Certificate Authority](../glossary.html#hyperledger-fabric-ca)

## Network summary

In this topic, we've seen how different organizations share their infrastructure
to provide an integrated Hyperledger Fabric blockchain network.  We've seen how
the collective infrastructure can be organized into channels that provide
private communications mechanisms that are independently managed.  We've seen
how actors such as client applications, administrators, peers and orderers are
identified as being from different organizations by their use of certificates
from their respective certificate authorities.  And in turn, we've seen the
importance of policy to define the agreed permissions that these organizational
actors have over network and channel resources.
