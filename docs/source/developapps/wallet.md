# Wallet
# 钱包
**Audience**: Architects, application and smart contract developers
**读者**：架构师，应用程序和智能合约开发者

A wallet contains a set of user identities. An application run by a user selects
one of these identities when it connects to a channel. Access rights to channel
resources, such as the ledger, are determined using this identity in combination
with an MSP.

一个钱包包含了一组用户的身份。用户运行的应用程序在连接到一个channel的时候，选择其中一个身份。访问channel资源，例如账本，的权限是由用这个身份结合MSP来确定的。

In this topic, we're going to cover:
本文将包含一下话题：

* [Why wallets are important](#scenario)
* 为什么钱包很重要
* [How wallets are organized](#structure)
* 钱包是如何组织的
* [Different types of wallet](#types)
* 不同类型的钱包
* [Wallet operations](#operations)
* 钱包操作

## Scenario
## 场景

When an application connects to a network channel such as PaperNet, it selects a
user identity to do so, for example `ID1`. The channel MSPs associate `ID1` with
a role within a particular organization, and this role will ultimately determine
the application's rights over channel resources. For example, `ID1` might
identify a user as a member of the MagnetoCorp organization who can read and
write to the ledger, whereas `ID2` might identify an administrator in
MagnetoCorp who can add a new organization to a consortium.

当一个应用程序连接到网络，例如PaperNet，的channel上时，它选择一个用户身份来使用，例如`ID1`。Channel的MPS将`ID1`与某个组织内的角色结合起来，这个角色最终会确定应用程序访问channel资源的权限。例如 `ID1`可能识别一个用作是MagenetoCorp组织的成员，可以读和写账本，而`ID2`可能是比额一个MagnetoCorp的管理员，可以向一个联盟添加一个组织。

![wallet.scenario](./develop.diagram.10.png) *Two users, Isabella and Balaji
have wallets containing different identities they can use to connect to
different network channels, PaperNet and BondNet.*
*两个用户，Isabella 和 Balaji 拥有钱包包含不同的身份他们可以用来连接到不同的网络chennel，PaperNet和BondNet*


Consider the example of two users; Isabella from MagnetoCorp and Balaji from
DigiBank.  Isabella is going to use App 1 to invoke a smart contract in PaperNet
and a different smart contract in BondNet.  Similarly, Balaji is going to use
App 2 to invoke smart contracts, but only in PaperNet. (It's very
[easy](./application.html#construct-request) for applications to access multiple
networks and multiple smart contracts within them.)

考虑这两个用例子；Isabella来自Magnetocorp，Balaji来自DigiBank。Isabella将使用App1来调用智能合约在PaperNet上，和另一个智能合约在BondNet上。同样，Balaji将使用App2来调用智能合约，但是只能是PaperNet上的（让用户访问不同网络中的多个智能合约是很容易[easy](./application.html#construct-request)的事情。）

See how:

* MagnetoCorp uses CA1 to issue identities and DigiBank uses CA2 to issue
  identities. These identities are stored in user wallets.

* Balaji's wallet holds a single identity, `ID4` issued by CA2. Isabella's
  wallet has many identities, `ID1`, `ID2` and `ID3`, issued by CA1. Wallets
  can hold multiple identities for a single user, and each identity can be
  issued by a different CA.

* Both Isabella and Balaji connect to PaperNet, and its MSPs determine that
  Isabella is a member of the MagnetoCorp organization, and Balaji is a member
  of the DigiBank organization, because of the respective CAs that issued their
  identities. (It is
  [possible](../membership/membership.html#mapping-msps-to-organizations) for an
  organization to use multiple CAs, and for a single CA to support multiple
  organizations.)

* Isabella can use `ID1` to connect to both PaperNet and BondNet. In both cases,
  when Isabella uses this identity, she is recognized as a member of
  MangetoCorp.

* Isabella can use `ID2` to connect to BondNet, in which case she is identified
  as an administrator of MagnetoCorp. This gives Isabella two very different
  privileges: `ID1` identifies her as a simple member of MagnetoCorp who can
  read and write to the BondNet ledger, whereas `ID2` identities her as a
  MagnetoCorp administrator who can add a new organization to BondNet.

* Balaji cannot connect to BondNet with `ID4`. If he tried to connect, `ID4`
  would not be recognized as belonging to DigiBank because CA2 is not known to
  BondNet's MSP.

## Types
## 类型

There are different types of wallets according to where they store their
identities:
依据身份存储的方式的不同，有不同种类的浅薄

![wallet.types](./develop.diagram.12.png) *The four different types of wallet:
File  system, In-memory, Hardware Security Module (HSM) and CouchDB.*
*有四类钱包：文件系统，内存，硬件安全模块和CouchDB*

* **FileSystem**: This is the most common place to store wallets; file systems
  are pervasive, easy to understand, and can be network mounted. They are a good
  default choice for wallets.
* **文件系统**： 这是最常见的存储钱包的地方；文件系统是无处不在的，容易理解的，可以连接到网络上。这是钱包的最好的默认选项。
  Use the `FileSystemWallet`
  [class](https://fabric-sdk-node.github.io/master/module-fabric-network.FileSystemWallet.html)
  to manage file system wallets.
  使用`FileSystemWallet`[class](https://fabric-sdk-node.github.io/master/module-fabric-network.FileSystemWallet.html)类来管理文件系统钱包

* **In-memory**: A wallet in application storage. Use this type of wallet when
  your application is running in a constrained environment without access to a
  file system; typically a web browser. It's worth remembering that this type of
  wallet is volatile; identities will be lost after the application ends
  normally or crashes.
  
  * **驻内存**：一个钱包在应用程序存储中。使用这类钱包，当你的应用程序运行在受限的环境，访问不了文件系统，一般是浏览器。要知道这类钱包是易逝的，当应用程序正常结束或崩溃时，身份就丢失了。

  Use the `InMemoryWallet`
  [class](https://fabric-sdk-node.github.io/master/module-fabric-network.InMemoryWallet.html)
  to manage in-memory wallets.
使用`InMemoryWallet`
  [class](https://fabric-sdk-node.github.io/master/module-fabric-network.InMemoryWallet.html)来管理驻内存钱包。

* **Hardware Security Module**: A wallet stored in an
  [HSM](https://en.wikipedia.org/wiki/Hardware_security_module). This
  ultra-secure, tamper-proof device stores digital identity information,
  particularly private keys. HSMs can be locally attached to your computer or
  network accessible. Most HSMs provide the ability to perform on-board
  encryption with private keys, such that the private key never leave the HSM.
* **硬件安全模块**：一个钱包存储在[HSM](https://en.wikipedia.org/wiki/Hardware_security_module).这是非常安全、防篡改设备存储数字身份信息，尤其是私钥。HSMs可以是本地连接到你的电脑或者是网络可以访问。大多数HSM提供on-board的使用私钥加密的功能呢，以此保证私钥永远不离开HSM。

  Currently you should use the `FileSystemWallet`
  [class](https://fabric-sdk-node.github.io/master/module-fabric-network.FileSystemWallet.html)
  in combination with the
  [HSMWalletMixin](https://fabric-sdk-node.github.io/master/module-fabric-network.HSMWalletMixin.html)
  class to manage HSM wallets.


* **CouchDB**: A wallet stored in Couch DB. This is the rarest form of wallet
  storage, but for those users who want to use the database back-up and restore
  mechanisms, CouchDB wallets can provide a useful option to simplify disaster
  recovery.

  Use the `CouchDBWallet`
  [class](https://fabric-sdk-node.github.io/master/module-fabric-network.CouchDBWallet.html)
  to manage CouchDB wallets.

## Structure
## 结构

A single wallet can hold multiple identities, each issued by a particular
Certificate Authority. Each identity has a standard structure comprising a
descriptive label, an X.509 certificate containing a public key, a private key,
and some Fabric-specific metadata. Different [wallet types](#types) map this
structure appropriately to their storage mechanism.

一个钱包可以保存多个身份，每个是由不同的证书办法机构办法的。每个身份都有一个标准的结构包括一个描述性标签，一个X.509证书包含一个弓腰，一个私钥，可以一个Fabric特定的元数据。不同的[wallet types](#types)钱包都能将这个结构映射到他们的存储机制上。

![wallet.structure](./develop.diagram.11.png) *A Fabric wallet can hold multiple
identities with certificates issued by a different Certificate Authority.
Identities comprise certificate, private key and Fabric metadata.*

*一个Fabric钱包可以保存多个身份带有不同的证书被不同的证书颁发机构办法。身份由证书、私钥和FAbric元数据组成*

There's a couple of key class methods that make it easy to manage wallets and
identities:
这里有一组关键的类方法使得管理钱包和身份变得容易

```JavaScript
const identity = X509WalletMixin.createIdentity('Org1MSP', certificate, key);

await wallet.import(identityLabel, identity);
```

 See how the `X509WalletMixin.createIdentity()`
[method](https://fabric-sdk-node.github.io/master/module-fabric-network.X509WalletMixin.html)
creates an `identity` that has metadata `Org1MSP`, a `certificate` and a private
`key`. See how `wallet.import()` adds this identity to the wallet with a
particular `identityLabel`.

The `Gateway` class only requires the `mspid` metadata to be set for an identity
-- `Org1MSP` in the above example. It *currently* uses this value to identify
particular peers from a [connection profile](./connectionprofile.html), for
example when a specific notification [strategy](./connectoptions.html) is
requested. In the DigiBank gateway file `networkConnection.yaml`, see how
`Org1MSP` notifications will be associated with `peer0.org1.example.com`:

```yaml
organizations:
  Org1:
    mspid: Org1MSP

    peers:
      - peer0.org1.example.com
```

You really don't need to worry about the internal structure of the different
wallet types, but if you're interested, navigate to a user identity folder in
the commercial paper sample:

```
magnetocorp/identity/user/isabella/
                                  wallet/
                                        User1@org1.example.com/
                                                              User1@org.example.com
                                                              c75bd6911aca8089...-priv
                                                              c75bd6911aca8089...-pub
```

You can examine these files, but as discussed, it's easier to use the SDK to
manipulate these data.

## Operations
## 操作

The different wallet classes are derived from a common
[Wallet](https://fabric-sdk-node.github.io/master/module-fabric-network.Wallet.html)
base class which provides a standard set of APIs to manage identities. It means
that applications can be made independent of the underlying wallet storage
mechanism; for example, File system and HSM wallets are handled in a very
similar way.

不同的钱包类被同一个钱包基类驱动，该基类提供磊哥标准的API集合来管理身份。这意味着应用程序可以与钱包的地秤存储机制分离，例如，文件系统和HSM钱包都是相同的方式操作。

![wallet.operations](./develop.diagram.13.png) *Wallets follow a
lifecycle: they can be created or opened, and identities can be read, added,
deleted and exported.*
*钱包遵循一个生命周期：他们可以创建或者打开，并且身份可以读取，添加，删除和导出*

An application can use a wallet according to a simple lifecycle. Wallets can be
opened or created, and subsequently identities can be added, read, updated,
deleted and exported. Spend a little time on the different `Wallet` methods in
the
[JSDOC](https://fabric-sdk-node.github.io/master/module-fabric-network.Wallet.html)
to see how they work; the commercial paper tutorial provides a nice example in
`addToWallet.js`:

一个应用程序可以使用钱包，根据这个简单的生命周期。钱包可以先

```JavaScript
const wallet = new FileSystemWallet('../identity/user/isabella/wallet');

const cert = fs.readFileSync(path.join(credPath, '.../User1@org1.example.com-cert.pem')).toString();
const key = fs.readFileSync(path.join(credPath, '.../_sk')).toString();

const identityLabel = 'User1@org1.example.com';
const identity = X509WalletMixin.createIdentity('Org1MSP', cert, key);

await wallet.import(identityLabel, identity);
```

Notice how:

* When the program is first run, a wallet is created on the local file system at
  `.../isabella/wallet`.

* a certificate `cert` and private `key` are loaded from the file system.

* a new identity is created with `cert`, `key` and `Org1MSP` using
  `X509WalletMixin.createIdentity()`.

* the new identity is imported to the wallet with `wallet.import()` with a label
  `User1@org1.example.com`.

That's everything you need to know about wallets. You've seen how they hold
identities that are used by applications on behalf of users to access Fabric
network resources. There are different types of wallets available depending on
your application and security needs, and a simple set of APIs to help
applications manage wallets and the identities within them.

<!--- Licensed under Creative Commons Attribution 4.0 International License
https://creativecommons.org/licenses/by/4.0/ -->
