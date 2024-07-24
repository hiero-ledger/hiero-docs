---
description: 加入Hedera Mainnet
---

# Mainnet

## 概览

Hedera mainnet(主要网络的短暂)是运行生产应用程序的地方，支付交易费用为 [HBAR](https://www.hedera.com/hbar)。 交易由任何应用程序或零售用户提交给Hedera Mainnet。它们自动被协商一致的时间戳和公平订购。&#x20

任何Hedera账户都可以查询与Hedera的服务和链上储存的数据资料。 每笔交易都需要以 _**交易费** 的形式支付（100,000,000吨位=1 个位）。 您可以了解更多有关交易费用的信息，并估计您的应用程序费用 [here](https://www.hedera.com/fees).&#x20

如果您要测试您的应用程序(或只是实验)，请访问 [Testnet Access](../testnet/testnet-access.md)。 Hedera 测试网使开发人员能够在模拟主网环境中使用测试_HBAR_来支付交易费用的原型和测试应用。

{% hint style="warning" %}
**交易 Throttles**\
Hedera Mainnet上的交易目前已经被拖放。 如果提交到网络的交易数量超过阈值，您将收到一个 "BUSY" 响应。
{% endhint %}

#### 网络阈值

| 网络请求类型 | Throtle (tps)                                                                                                                                            |
| ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 加密货币交易 | <p>AccountCreateTransaction: 2 tps</p><p>AccountBalanceQuery: 无限</p><p>TransferTransaction (inc. tokens): 10,000 tps<br>其他: 10,000 tps</p>                                  |
| 共识交易   | <p>主题创建交易: 5 tps</p><p>其他: 10,000 tps</p>                                                                                                                                   |
| 代币交易   | <p>令牌：</p><ul><li>125 TPS 用于可互换的迷你</li><li>50 TPS for NFT mint</li></ul><p>TokenAssociateTransaction: 100 tps<br>TransferTransfer交易 令牌：10 000个tps</p><p>其他：3 000个tps</p>    |
| 交易计划   | <p>ScheduleSignTransaction: 100 tps<br>ScheduleCreateTransaction: 100 tps</p>                                                                                               |
| 文件交易   | 10 tps                                                                                                                                                                      |
| 智能合同交易 | <p>合同执行交易：350 tps<br>合同创建交易：350 tps</p>                                                                                                                                     |
| 查询     | <p>ContractGetInfo: 700 tps<br>ContractGetBytecode: 700 tps<br>ContractCallLocal: 700 tps<br><br>FileGetInfo: 700 tps<br>FileGetContents: 700 tps<br><br>其他: 10, 00 tps</p> |
| 收款情况   | 无限制(无节点)                                                                                                                                                 |
