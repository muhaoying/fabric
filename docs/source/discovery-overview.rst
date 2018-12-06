Service Discovery
=================

Why do we need service discovery?
---------------------------------

In order to execute chaincode on peers, submit transactions to orderers, and to
be updated about the status of transactions, applications connect to an API
exposed by an SDK.

为了能在peer上执行链码，向orderers提交交易，接受交易状态更新，应用程序连接到SDK暴露的API上。

However, the SDK needs a lot of information in order to allow applications to
connect to the relevant network nodes. In addition to the CA and TLS certificates
of the orderers and peers on the channel -- as well as their IP addresses and port
numbers -- it must know the relevant endorsement policies as well as which peers
have the chaincode installed (so the application knows which peers to send chaincode
proposals to).

但是SDK需要很多信息，为了让应用程序来连接到相关的网络节点上。除了channel上的orderer和peers的CA和TLS证书 ———— 加上他们的IP地址和端口号 ———— 它必须知道相关的背书策略和那些peer安装了链码（这样应用程序就知道要往哪个节点上发送交易提案了）

Prior to v1.2, this information was statically encoded. However, this implementation
is not dynamically reactive to network changes (such as the addition of peers who have
installed the relevant chaincode, or peers that are temporarily offline). Static
configurations also do not allow applications to react to changes of the
endorsement policy itself (as might happen when a new organization joins a channel).

V1.2之前，这些信息都是静态硬编码的。但是，这种实现方式不能动态响应网络的变更（例如，增加了peer，且peer上安装了相关的链码，或者peer是临时下线）。静态配置同样也不允许应用程序响应背书策略的更新（比如 当有心的组织加入到channel中时）

In addition, the client application has no way of knowing which peers have updated
ledgers and which do not. As a result, the application might submit proposals to peers whose ledger data is
not in sync with the rest of the network, resulting in transaction being invalidated
upon commit and wasting resources as a consequence.

另外，客户端应用程序是没有办法知道哪个peer更新了账本，哪个peer没有更新账本。结果，应用程序可能把提案提交给了那些没有同步账本的节点，导致交易无效，浪费资源。

The **discovery service** improves this process by having the peers compute
the needed information dynamically and present it to the SDK in a consumable
manner.

 **发现服务**提升了这个过程，通过让节点动态计算所需的信息并把这些信息提交给SDK，以可消费的方式。

How service discovery works in Fabric
-------------------------------------
Fabric的服务发现是如何工作的
------------------------
The application is bootstrapped knowing about a group of peers which are
trusted by the application developer/administrator to provide authentic responses
to discovery queries. A good candidate peer to be used by the client application
is one that is in the same organization.

应用程序启动是就知道一组被应用程序开发者和管理员信任的peer，这些peer对于服务发现请求可以返回可信的记过。可被客户端应用程序信任的候选节点最好就是同一个组织下的节点。

The application issues a configuration query to the discovery service and obtains
all the static information it would have otherwise needed to communicate with the
rest of the nodes of the network. This information can be refreshed at any point
by sending a subsequent query to the discovery service of a peer.

应用程序发起一个配置请求来发现服务，并且获取所有的静态信息它将否则需要与网络中的其他节点通信来获取。此信息可以随时更新，通过向peer发送一个后续的请求来发现服务。

The service runs on peers -- not on the application -- and uses the network metadata
information maintained by the gossip communication layer to find out which peers
are online. It also fetches information, such as any relevant endorsement policies,
from the peer's state database.

该服务在peer上运行 ———— 不是在应用程序上 ————并且使用网络元数据信息，被gossip通信曾维护，来发现哪些peer是在线的。它同时也获取信息，诸如任何相关的背书策略，从peer的state数据库中。

With service discovery, applications no longer need to specify which peers they
need endorsements from. The SDK can simply send a query to the discovery service
asking which peers are needed given a channel and a chaincode ID. The discovery
service will then compute a descriptor comprised of two objects:

有了服务发现，应用程序不用在去说明他们需要哪些peer的背书。SDK可以简单的发送一个请求到发现服务，询问哪些peer是需要的，只哟啊提供一个channel和一个链码ID。发现服务将会计算一个描述，包含两个对象：

1. **Layouts**: a list of groups of peers and a corresponding amount of peers from
   each group which should be selected.
   **布局**: 一个列表，包含一些peer的group，和一个相对应的peer的数量，每组应该选出。
2. **Group to peer mapping**: from the groups in the layouts to the peers of the
   channel. In practice, each group would most likely be peers that represent
   individual organizations, but because the service API is generic and ignorant of
   organizations this is just a "group".
   **group和peer的映射**：从布局中的group到channel上的peer。 在实践中，每个组都是peers表示一个独立的组织，但是因为服务API是一般化的，并忽视组织概念，这就是一个“group组”

The following is an example of a descriptor from the evaluation of a policy of
``AND(Org1, Org2)`` where there are two peers in each of the organizations.

.. code-block:: JSON

   Layouts: [
        QuantitiesByGroup: {
          “Org1”: 1,
          “Org2”: 1,
        }
   ],
   EndorsersByGroups: {
     “Org1”: [peer0.org1, peer1.org1],
     “Org2”: [peer0.org2, peer1.org2]
   }

In other words, the endorsement policy requires a signature from one peer in Org1
and one peer in Org2. And it provides the names of available peers in those orgs who
can endorse (``peer0`` and ``peer1`` in both Org1 and in Org2).

换句话说，背书策略要求一个org1的peer和一个org2的peer来签名。 并且它提供了有效的peer在这些org中，可以背书（在org1和org2中都是peer0和peer1）

The SDK then selects a random layout from the list. In the example above, the
endorsement policy is Org1 ``AND`` Org2. If instead it was an ``OR`` policy, the SDK
would randomly select either Org1 or Org2, since a signature from a peer from either
Org would satisfy the policy.

SDK然后就可以从列表中随机选择出一个布局。在上面的例子中，背书策略是Org1 “AND” Org2。如果是OR策略，SDK就随机选择Org1或者Org2，因为任何一个组织的任何一个peer的签名都满足这个策略。

After the SDK has selected a layout, it selects from the peers in the layout based on a
criteria specified on the client side (the SDK can do this because it has access to
metadata like ledger height). For example, it can prefer peers with higher ledger heights
over others -- or to exclude peers that the application has discovered to be offline
-- according to the number of peers from each group in the layout. If no single
peer is preferable based on the criteria, the SDK will randomly select from the peers
that best meet the criteria.

SDK可以选择一个布局之后，它会依据客户端的一个原则来从布局总选择peer（SDK可以做这个，因为它可以访问诸如账本高度等的元数据）。例如，他可以选择peer有比别人高的账本高度 ———— 或者把那些在服务发现是得知是下线的节点。根据布局中每个组织的节点的数目。如果根据这些原则没有节点被选中，SDK就会随机选一个，最能满足这些原则的。

Capabilities of the discovery service
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

发现服务的能力
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


The discovery service can respond to the following queries:
发现服务可以响应以下查询：

* **Configuration query**: Returns the ``MSPConfig`` of all organizations in the channel
  along with the orderer endpoints of the channel.
  **配置查询** 返回channel中所有组织的``MSPConfig``，和orderer的endpoints。
* **Peer membership query**: Returns the peers that have joined the channel.
  **Peer成员查询**: 返回所有的加入channel的peer
* **Endorsement query**: Returns an endorsement descriptor for given chaincode(s) in
  a channel.
  **背书查询** 对于一个channel上的某个链码，返回背书描述。
* **Local peer membership query**: Returns the local membership information of the
  peer that responds to the query. By default the client needs to be an administrator
  for the peer to respond to this query.
  **本地peer memebership 查询**：返回peer的本地的membership信息 相应查询的。 默认情况下客户端需要是peer的一个管理员才能相应这个查询。

Special requirements
~~~~~~~~~~~~~~~~~~~~~~

特殊需求
~~~~~~~~~~~~~~~~~~~~~~

When the peer is running with TLS enabled the client must provide a TLS certificate when connecting
to the peer. If the peer isn't configured to verify client certificates (clientAuthRequired is false), this TLS certificate can be self-signed.
当peer运行是带有TLS的，客户端必须提供一个TLS证书才能连接到peer上。如果peer没有配置成验证客户端证书的，这个TLS证书可以是自签名的。

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
