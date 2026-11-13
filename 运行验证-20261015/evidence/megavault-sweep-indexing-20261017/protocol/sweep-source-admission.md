# Sweep 调度、事件与索引消息准入

**结论：固定v9.7.0源码可精确识别Sweep及其入账路径。Sweep发Cosmos专用事件并更新子账户资产；这条已读路径没有调用普通Transfer/Deposit索引事件构造器。不能据此直接把全部账户残差归协议分成，也不直接推生产Indexer实际覆盖。**

版本固定`67a3c71670c460ef92ffb15930dda7047a901f64`，来自此前官方`protocol/v9.7.0`实际tag响应。本轮6次GH GET均200，每次25秒/1MiB上限，未用满8次即停；各URL/UTC/原HTTP保存bytes/SHA、源码bytes/SHA及验证Gitblob见read-receipts.json。HTTP保存bytes含headers，不冒传输流量。旧当前tree仅定位路径，不证明该tree就是v9.7.0；实际内容均显式固定旧commit。model/effort实际UNKNOWN；PERMISSIONS-2026-10-14-v1、ROUTING-2026-10-13-v4未变。

| 固定源码位置 | 已核事实 |
|---|---|
| vault/module.go L156–175 | AppModule BeginBlock/EndBlock调用该模块BeginBlocker/EndBlocker。未核应用所有模块注册顺序。 |
| vault/abci.go L12–48 | BeginBlocker做DecommissionNonPositiveEquityVaults；EndBlocker在RunCached同一回调中**先RefreshAllVaultOrders、后SweepMainVaultBankBalance**。错误/恢复处理存在，不等于每块必有正量成功Sweep。 |
| 已存sweep_funds.go L10–53 | 查询主银行USDC余额，非正返回；正量调用DepositFundsFromAccountToSubaccount，若错误返回，不发成功Sweep事件；成功后CosmosEventManager.EmitEvent。扫全部该币正银行余额，不仅协议分成。 |
| vault/types/events.go L23–24、L69–77 | 精确type=`sweep_to_megavault`，唯一业务attribute=`swept_quote_quantums`，通过fmt十进制uint64表示。该构造器无from/to、无分成来源/期间/费率字段；不能由它直接区分协议收入与其他银行入款。 |
| 已存subaccounts/keeper/transfer.go L73–159 | DepositFundsFromAccountToSubaccount先bank.SendCoins(mainBank→collateralPool)，再UpdateSubaccounts(...,types.Deposit)。该已读函数没有调用NewDepositEvent或NewTransferEvent。 |
| subaccounts/keeper/subaccount.go L358–491，尤其L438–461 | 更新成功后SetSubaccount并通过GetIndexerEventManager().AddTxnEvent发`SubtypeSubaccountUpdate`/`SubaccountUpdateEventVersion`，内容为NewSubaccountUpdateEvent。不是在这个函数中发普通Transfer subtype。另按资金费结算发Cosmos事件，不能混作Sweep金额。 |
| indexer/events/subaccount_update.go L15–36 | 更新消息含SubaccountId、UpdatedPerpetualPositions、UpdatedAssetPositions（含相关quote合并逻辑）；它表示更新仓位，不自带Sweep来源标签、支付方或fee份额。 |
| indexer/events/transfer.go L10–67 | NewTransferEvent是子账户→子账户；另NewDepositEvent是wallet地址→子账户，两者返回TransferEventV1。构造器存在不等于Sweep调用它。 |

因此源码层可区分三类记录：bank付款（协议份额先入主银行）、Sweep专用Cosmos事件、子账户更新消息。普通子账户资本调拨另用Transfer语义。是否还有应用级或Indexer ingestion从银行/专用事件重建普通transfer，需其下游证据；本任务未读，不作必然漏记的全局断言，也不把当前源码当生产部署字节。AddTxnEvent的存储/EndBlock消息封装实现未补读；不能把它等同普通CometBFT tx事件，或保证在block_results中可见其解码内容。

**既有固定block_results可采用的准确检查：** 直接保留原明文strings，在txs_results[].events及finalize_block_events中精确匹配type=`sweep_to_megavault`，读取`swept_quote_quantums`。不要启发式Base64解明文，不把普通bank `transfer`或子账户`NewDepositEvent`当同一事件。既有107688409原返回经精确匹配为0个Sweep，只是这一个块该提供商返回中未见，不推期间无收入。普通账户资产增减仍应与Sweep、银行流、普通转账分别对账；银行付款和随后Sweep不能重复计收入。

本轮无节点/RPC或新账数据请求，无来源代码/SDK/SQL/EVM/模型/回测/策略/交易执行、安装、认证、账号凭证、付款或外发。未新增许可结论、私人bot或未来盈利门槛；精确首次公开时间、运行身份、期间实际分成金额与净利润仍未由此证明。此为窄源准入PASS，可指导根代理仅对已保存原件作精确事件定位。

## 原8GET预算内补读（总计8次，现已停网）

第7、8次按同commit取得`protocol/indexer/indexer_manager/event_manager.go`与`on_chain_updates.go`，原6次及全部输入保留。event_manager.go L60–76、L101–111显示：当Enabled时，AddTxnEvent把原subtype/version/dataBytes及TransientStoreKey交给`addTxnEvent`；ProduceBlock委托`produceBlock`。注释明确后者应在EndBlocker、收齐待提交块onchain事件后调用。SendOnchainData用CreateIndexerBlockEventMessage封装；on_chain_updates.go L15–29通过proto.Marshal序列化整个IndexerTendermintBlock作为Message.Value。

这两份完整小文件没有把SubaccountUpdate转换/重建为普通Transfer，公开wrapper也没有为Sweep另造NewDepositEvent。**但helper `addTxnEvent` / `produceBlock` 的实现不在这两文件，未在原8GET预算内读取。因此EndBlock产生的AddTxnEvent如何归属tx_index/事件顺序、TransientStore具体键、实际最终打包顺序仍UNKNOWN。** 不拿wrapper注释代实现，也不要求额外远程证明。此前关于未补读AddTxnEvent封装的UNKNOWN现仅按这段收窄，不追改旧读取事实。

本轮根代理另核下游handler/SQL两路；本文件不冒充独立读取这些原件。固定源码规则与生产部署、应用全模块顺序及真实期间收入仍分别判断。预算8次已用完，不新增读取或数据RPC。
