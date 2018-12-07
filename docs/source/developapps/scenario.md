# The scenario 场景

**Audience**: Architects, Application and smart contract developers, Business
professionals
**读者**： 架构师，应用和智能合约开发者，业务专家

In this topic, we're going to describe a business scenario involving six
organizations who use PaperNet, a commercial paper network built on Hyperledger
Fabric, to issue, buy and redeem commercial paper. We're going to use the
scenario to outline requirements for the development of commercial paper
applications and smart contracts used by the participant organizations.
在本议题中，我们将要描述一个商业场景包括六个组织使用PaperNet, 一个搭建在Hyperledger Fabric上的商业票据网络，来发行，购买和赎回商业票据。我们将使用这个场景来抽取开发一个这些参与方所使用的商业票据应用程序和智能合约需求。

## PaperNet network
## PaperNet网络

PaperNet is a commercial paper network that allows suitably authorized
participants to issue, trade, redeem and rate commercial paper.
PaperNet 是一个商业票据网络，允许合适的被授权的参与者来发行，交易，赎回和评估商业票据。

![develop.systemscontext](./develop.diagram.1.png)

*The PaperNet commercial paper network. Six organizations currently use PaperNet
network to issue, buy, sell, redeem and rate commercial paper. MagentoCorp
issues and redeems commercial paper.  DigiBank, BigFund, BrokerHouse and
HedgeMatic all trade commercial paper with each other. RateM provides various
measures of risk for commercial paper.*

*PaperNet 商业票据网络。六个组织当前使用PaperNet网络来发行，购买，出售，赎回和评估商业票据。MagentoCorp发行并赎回商业票据。DigiBank，BigFund，BrokerHouse和HedgeMatic 都相互交易商业票据。RateM提供多种商业票据风险评估方法*

Let's see how MagnetoCorp uses PaperNet and commercial paper to help its
business.
让我们看看MagnetoCorp是如何使用PaperNet和商业票据来帮助他的业务的。

## Introducing the actors
## 引入角色

MagnetoCorp is a well-respected company that makes self-driving electric
vehicles. In early April 2020, MagnetoCorp won a large order to manufacture
10,000 Model D cars for Daintree, a new entrant in the personal transport
market. Although the order represents a significant win for MagnetoCorp,
Daintree will not have to pay for the vehicles until they start to be delivered
on November 1, six months after the deal was formally agreed between MagnetoCorp
and Daintree.

MagnetoCorp是一个信誉很好的，生产自动驾驶电动汽车的公司。在2020年4月初， MagnetoCorp从Daintree公司赢得了一笔大订单，来生产10，000台D型汽车。Daintree是一个新秀的个人交通市场的。尽管对于MagnetoCorp来说，这个订单代表这一个非常有意义的成功，但Daintree只能在11月1日汽车交付的时候才能支付，也就是交易签署的6个月之后。


To manufacture the vehicles, MagnetoCorp will need to hire 1000 workers for at
least 6 months. This puts a short term strain on its finances -- it will require
an extra 5M USD each month to pay these new employees. **Commercial paper** is
designed to help MagnetoCorp overcome its short term financing needs -- to meet
payroll every month based on the expectation that it will be cash rich when
Daintree starts to pay for its new Model D cars.

为了生产这些汽车，MagnetoCorp将需要聘用超过1000名工人，至少工作6个月。这会对它的财务造成一个短暂的压力————它将还需要5M USD来为新的员工发工资。**商业票据**被设计出来帮助MagetoCorp来战胜它的短期资金需求 ———— 来满足每个月的工资，预期当Daintree开始支付他们的新型D汽车的时候，他们的现金就会充足。

At the end of May, MagnetoCorp needs 5M USD to meet payroll for the extra
workers it hired on May 1. To do this, it issues a commercial paper with a face
value of 5M USD with a maturity date 6 months in the future -- when it expects
to see cash flow from Daintree. DigiBank thinks that MagnetoCorp is
creditworthy, and therefore doesn't require much of a premium above the central
bank base rate of 2%, which would value 4.95M USD today at 5M USD in 6 months
time. It therefore purchases the MagnetoCorp 6 month commercial paper for 4.94M
USD -- a slight discount compared to the 4.95M USD it is worth. DigiBank fully
expects that it will be able to redeem 5M USD from MagnetoCorp in 6 months time,
making it a profit of 10K USD for bearing the increased risk associated with
this commercial paper. This extra 10K means it receives a 2.4% return on
investment -- significantly better than the risk free return of 2%.

五月底，MagnetoCorp需要5M USD来支付5月1日新招入的员工的工资。为了做到这一点，它发行了一种商业票据，面值是5M USD，未来6个月之后到期——届时他们将预期会见到来自Daintree的现金流。 DigiBank认为MagetoCorp是信誉良好的，并且不需要much of a premium above the central bank base rate of 2%, 未来6个月的5M USD现在值4.95M USD。 于是它购买了MeagnetoCorp的6个月的商业票据以4.94M USD的价格 ———— 比4.95M USD有一点折扣。 DigiBank希望6个月之后从MagetoCorp赎回5M USD，利润是10K USD，承担此商业票据的增加的风险。这多出来的10K一位置2.4%的投资回报 ———— 显然比无风险回报率2%要高很多。

At the end of June, when MagnetoCorp issues a new commercial paper for 5M USD to
meet June's payroll, it is purchased by BigFund for 4.94M USD.  That's because
the commercial conditions are roughly the same in June as they are in May,
resulting in BigFund valuing MagnetoCorp commercial paper at the same price that
DigiBank did in May.

到了6月底，当MagnetoCorp发行了一张新的5M USD商业票据 来支付六月的工资是，它被BigFund以4.94M USD的价格购买。这是因为商业环境在六月跟五月是差不多一样的，导致了BigFund对MagnetoCorp的商业票据的估值跟DigiBank在五月份的估值一样。

Each subsequent month, MagnetoCorp can issue new commercial paper to meet its
payroll obligations, and these may be purchased by DigiBank, or any other
participant in the PaperNet commercial paper network -- BigFund, HedgeMatic or
BrokerHouse. These organizations may pay more or less for the commercial paper
depending on two factors -- the central bank base rate, and the risk associated
with MagnetoCorp. This latter figure depends on a variety of factors such as the
production of Model D cars, and the creditworthiness of MagnetoCorp as assessed
by RateM, a ratings agency.

后面的每个月， MagnetoCorp都可以发行一种新的商业票据来满足工资支付，并且这些商业票据会被DigiBank或者其他的PaperNet商业票据网路的参与者所购买 ———— BigFund， HedgeMatic 或者BrokerHouse。 这些组织支付或多或少的钱来买这些商业票据依据两个元素 ———— 中心银行的汇率，和MagetoCorp的风险。后面一个指标依赖于多项元素，诸如D型轿车的生产、和MagnetoCorp被信用机构的信用评估，诸如RateM。

Let's pause the MagnetoCorp story for a moment, and develop the client
applications and smart contracts that PaperNet uses to issue, buy, sell and
redeem commercial paper.  We'll come back to the role of the rating agency,
RateM, a little later.

我们先暂停对MagnetoCorp的故事，来开发一下PaperNet所需要的客户端应用程序和智能合约来发行、购买、出售和赎回的商业票据。我们将之后将讨论评估机构，RateM。

<!--- Licensed under Creative Commons Attribution 4.0 International License
https://creativecommons.org/licenses/by/4.0/ -->
