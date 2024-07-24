---
description: Hedera 网络费用
---

# 费用

下面的Hedera测试网费用表提供了所有网络服务的低端交易和查询费用。 下表列出每个API调用的 USD 、HBAR和 Tinybar (tsig)值。 Hedera 测试网上的所有操作费用都是通过测试性六溴环十二烷支付的，该测试免费提供，并且只用于开发目的。

费用估计数是根据对指定的 API 调用细节的假设计算的。 例如， HBAR 加密货币传输(CryptoTransfer) 的费用假定交易上有一个单独的签名，储存文件的费用假定是一个48字节大小的文件，存储时间为30天。 超过这些基本假设的交易将更加昂贵；我们建议增加您的最高允许费用，以适应更多的复杂性。

### Mainnet费用

主流交易和查询费用可以使用 [Hedera Fee Estimator](https://hedera.com/fees)估算。 费用估算器允许您确定费用 (美元和 HBAR) 使用基本上为个别交易和基于其特点的查询使用目前的实际汇率， 以及根据这些交易的预期数量计算的预计费用。 估计可能不会百分之百准确，未经事先通知就会改变基本价格。

## HBAR 提名和简称

| 提名      | 简称             | HBAR加密货币        |
| ------- | -------------- | --------------- |
| gigabar | 1 Għ           | = 1 000 000 000 |
| megabar | 1兆字节           | = 100,000       |
| kilobar | 1千克            | = 1 000秒        |
| hbar    | 1秒             | = 1 秒           |
| 毫巴      | 1,000毫秒        | = 1 秒           |
| 微栏      | 1,000,000 μħ   | = 1 秒           |
| tinybar | 100,000 tasser | = 1 秒           |

## 交易和查询费

所有费用都会有变化。 下面的费用反映交易或查询的基本价格。 交易特性可能会从下面显示的基本价格增加价格。 交易特性包括一个以上的签名、一个备忘录字段等。 请参考[Hedera fee estimatator](https://hedera.com/fees)估算交易或查询费用。

### 加密货币服务

<table><thead><tr><th width="482">业务活动</th><th>美元 ($)</th></tr></thead><tbody><tr><td>Crypto创建</td><td>$0.05</td></tr><tr><td>CryptoAccountAutoRenew</td><td>$0.00022</td></tr><tr><td>CryptoDeletealledance</td><td>$0.05</td></tr><tr><td>加密批准津贴</td><td>$0.05</td></tr><tr><td>CryptoUpdate</td><td>$0.00022</td></tr><tr><td>加密传输</td><td>$0.0001</td></tr><tr><td>CryptoTransfer (自定义费用)</td><td>$0.002</td></tr><tr><td>加密删除</td><td>$0.005</td></tr><tr><td>CryptoGetAccount记录</td><td>$0.0001</td></tr><tr><td>加密账户余额</td><td>$0.00</td></tr><tr><td>CryptoGetInfo</td><td>$0.0001</td></tr><tr><td>CryptoGetStaters</td><td>$0.0001</td></tr></tbody></table>

### 信息和通信技术司

<table><thead><tr><th width="484">业务活动</th><th>美元 ($)</th></tr></thead><tbody><tr><td>共识创建主题</td><td>$0.01</td></tr><tr><td>共识更新主题</td><td>$0.00022</td></tr><tr><td>共识删除主题</td><td>$0.005</td></tr><tr><td>共识提交消息</td><td>$0.0001</td></tr><tr><td>ConsensusGetTopicInfo</td><td>$0.0001</td></tr></tbody></table>

### 令牌服务

<table><thead><tr><th width="486">业务活动</th><th>美元 ($)</th></tr></thead><tbody><tr><td>创建令牌</td><td>$1.00</td></tr><tr><td>TokenCreate (自定义费用)</td><td>$2.00</td></tr><tr><td>TokenUpdate</td><td>$0.001</td></tr><tr><td>TokenFeeSchedule更新</td><td>$0.001</td></tr><tr><td>TokenDelete</td><td>$0.001</td></tr><tr><td>令牌关联</td><td>$0.05</td></tr><tr><td>TokenDissociate</td><td>$0.05</td></tr><tr><td>TokenMint (fungible)</td><td>$0.001</td></tr><tr><td>TokenMint (不可互换)</td><td>$0.02</td></tr><tr><td>TokenMint (bulk mint 10k NFTs)</td><td>$200</td></tr><tr><td>TokenBurn</td><td>$0.001</td></tr><tr><td>TokenGrantKyc</td><td>$0.001</td></tr><tr><td>TokenRevokekyc</td><td>$0.001</td></tr><tr><td>TokenFreeze</td><td>$0.001</td></tr><tr><td>TokenUnfreeze</td><td>$0.001</td></tr><tr><td>令牌暂停</td><td>$0.001</td></tr><tr><td>TokenUnpuser</td><td>$0.001</td></tr><tr><td>TokenWipe</td><td>$0.001</td></tr><tr><td>TokenGetInfo</td><td>$0.0001</td></tr><tr><td>TokenGetNftInfo</td><td>$0.0001</td></tr><tr><td>TokenGetNftInfos</td><td>$0.0001</td></tr><tr><td>TokenGetAccountNftInfos</td><td>$0.0001</td></tr><tr><td>TokenUpdateNfts (更新1个NFT元数据)</td><td>$0.001</td></tr><tr><td>TokenUpdateNfts (在单次通话中更新多个NFT)</td><td>N * 0.001</td></tr></tbody></table>

### 交易计划

<table><thead><tr><th width="491">业务活动</th><th>美元 ($)</th></tr></thead><tbody><tr><td>计划创建</td><td>$0.01</td></tr><tr><td>计划签名</td><td>$0.001</td></tr><tr><td>ScheduleDelete</td><td>$0.001</td></tr><tr><td>调度信息</td><td>$0.0001</td></tr></tbody></table>

### 文件服务

<table><thead><tr><th width="495">业务活动</th><th>美元 ($)</th></tr></thead><tbody><tr><td>创建文件</td><td>$0.05</td></tr><tr><td>文件更新</td><td>$0.05</td></tr><tr><td>文件删除</td><td>$0.007</td></tr><tr><td>附加文件</td><td>$0.05</td></tr><tr><td>文件内容</td><td>$0.0001</td></tr><tr><td>文件信息</td><td>$0.0001</td></tr></tbody></table>

### Smart Contracting Service

<table><thead><tr><th width="501">业务活动</th><th>美元 ($)</th></tr></thead><tbody><tr><td>合同创建</td><td>$1.0</td></tr><tr><td>合同已更新</td><td>$0.026</td></tr><tr><td>合同删除</td><td>$0.007</td></tr><tr><td>合同电话</td><td>$0.05</td></tr><tr><td>ContractCallLocal</td><td>$0.001</td></tr><tr><td>合同GetByteCode</td><td>$0.05</td></tr><tr><td>GetBySolidityID</td><td>$0.0001</td></tr><tr><td>ContractGetInfo</td><td>$0.0001</td></tr><tr><td>合同资料记录</td><td>$0.0001</td></tr><tr><td>合同自动更新</td><td>$0.026</td></tr></tbody></table>

### 其他事项

<table><thead><tr><th width="508">业务活动</th><th>美元 ($)</th></tr></thead><tbody><tr><td>以太坊交易</td><td>$0.0001</td></tr><tr><td>PrngTransaction</td><td>$0.001</td></tr><tr><td>获取版本</td><td>$0.001</td></tr><tr><td>获取字节</td><td>$0.0001</td></tr><tr><td>交易getrecipt</td><td>$0.0000</td></tr><tr><td>交易GetRecords</td><td>$0.0001</td></tr></tbody></table>
