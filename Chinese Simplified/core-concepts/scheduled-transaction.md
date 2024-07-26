# 交易计划

## 概览

**schedule交易** 是一种能够在Hedera网络上收集所需签名以便执行的交易。 不同于赫代拉人的其他交易， 这允许您在您没有为网络立即处理交易所需的所有签名的情况下排队执行交易。 计划交易用于创建计划交易。 此功能对于需要多个签名的交易来说是理想的。

当用户创建计划交易时，网络创建计划实体。 计划实体收到的实体ID与帐户、令牌等类似，称为scheduleID。 schedule ID用于引用创建的计划交易。 正在计划的交易被计划的交易 ID。&#x20

签名通过提交`ScheduleSign`交易而附加到计划交易中。 `ScheduleSign`交易要求签名将附加到的交易计划ID。 在当前设计中， 预定的交易有30分钟的时间来收集所有所需的签名，然后才能执行预定的交易或从网络中删除。 您可以通过设置管理员密钥来删除预定的交易，然后在网络执行或删除之前删除预定的交易。

您可以通过查询“ScheduleGetInfo”网络请求当前交易的状态。 这项请求将提供以下资料：

- 计划 ID
- 创建预定交易的帐户 ID
- 支付创建预定交易的账户 ID
- 内部交易的交易内容
- 内部事务的交易 ID
- 当前签名列表
- 管理员密钥(如果有)
- 过期时间
- 如果是的话，当交易被删除时的时间戳

The design document for this feature can be referenced [here](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/revised-spec.md).

**Schedule 交易 ID**

Hedera Transaction ID由提交交易的账户ID和有效的交易开始时间组成，时间以秒为单位(`0.0.1234@1615422161.673238162`)。 预定交易的交易ID将包括“”？ 交易ID结束时的chedule`，它将交易确定为计划中的交易。 a. `0.0.1234@16154161.673238162?scheduled\`。 预定(内部)交易的事务ID继承了预定(外部)交易的有效启动时间和账户ID。

**安排交易收据**

创建的交易计划的交易收据包含新的交易计划实体ID和预定的交易ID。 计划中的事务 ID用于在成功执行时请求内部交易记录。

**Schedule 交易记录**

为每个附加的签名创建预定的交易记录时创建交易记录 当计划交易被执行时，如果计划交易被用户删除。 交易记录包括交易计划的参考属性，它是记录关联的交易计划的ID。 为了在成功执行后获得内部交易记录，您可以做以下操作：

1. 投票指定的交易记录ID的网络。 一旦预定交易成功执行预定交易，请使用预定交易ID记录预定交易。
2. 查询预定交易ID的 Hedera 镜像节点。
3. 运行您自己的镜像节点并查询计划的交易 ID。

## 常见问题

<details>

<summary>What is the difference between a schedule transaction and scheduled transaction?</summary>

一个 _**schedule 交易**_ 是一种能够在Hedera 网络上收集所需签名以便执行的交易。

一个 _**计划交易**_ 是已经排定的交易。

</details>

<details>

<summary>是否有实体ID分配到schedule交易?</summary>

是的，实体ID是指在收到ScheduleCreate 交易时返回的scheduleID。

</details>

<details>

<summary>What transactions can be scheduled?</summary>

在早期的迭代中，一小部分交易将是可以计划的。 You check out [this](../sdks-and-apis/sdks/schedule-transaction/create-a-schedule-transaction.md) page for a list of transaction types that are supported today. 所有其他交易类型都可以在未来的发行版中排列。 用户将来可以安排的交易的完整列表可以在这里找到。

</details>

<details>

<summary>How can I find a schedule transaction that requires my signature?</summary>

- 预定交易的创建者可以向您提供您在ScheduleSign交易中指定的scheduleID来提交您的签名。

<!---->

- 您可以查询镜像节点来返回所有与您的公钥相关联的交易计划。 这个选项今天不可用，但是计划将来使用。

</details>

<details>

<summary>What happens if the scheduled transaction does not have sufficient balance?</summary>

如果预定的交易(内部交易)费用支付者没有足够的余额，那么内部交易将会失败，而计划交易(外部交易)将会成功。

</details>

<details>

<summary>Can you delay a transaction once it has been scheduled?</summary>

不，您不能在提交到网络后延迟或修改预定的交易。 您需要删除交易计划并创建一个新的修改计划。

</details>

<details>

<summary>如果多个用户创建相同的交易计划会发生什么？。</summary>

- 达成共识的第一笔交易将创建交易计划并提供交易计划实体 ID
- 其他用户将在收到提交的交易时获得scheduleID。 接收状态将导致`IDENTICAL_SCHEDULE_ALREADY_CREATED`。 这些用户需要提交一份Schedule签名交易以便在交易计划中附上他们的签名。

</details>

<details>

<summary>When does the scheduled transaction execute?</summary>

当收到最后一个签名时，预定的交易将执行。

</details>

<details>

<summary>How often does the network check to see if the scheduled transaction (inner transaction) has met the signature requirement?</summary>

每次计划交易都签名。

</details>

<details>

<summary>How do you get information about a schedule transaction?</summary>

您可以向网络提交[schedule信息查询](../sdks-andapis/sdks/schedule-transaction/get-schedule-info.md) 请求。

</details>

<details>

<summary>预定的交易何时到期？。</summary>

计划交易将在 30 分钟后到期。 在今后的实施中，我们将允许用户设定预定交易的执行时间 并且交易届时将到期。

</details>

<details>

<summary>交易收据包含什么？</summary>

创建的交易计划的交易收据包含新的交易计划实体ID和预定的交易ID。

</details>
