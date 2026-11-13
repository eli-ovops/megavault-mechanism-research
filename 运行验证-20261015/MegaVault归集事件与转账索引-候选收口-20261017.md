# MegaVault归集事件与转账索引：候选收口

**结论：所读公开代码把余额更新和普通转账分成两条路径。普通转账表及PnL汇总仍不足以把426.065316 USDC归因为协议分成或完整净利润。MegaVault继续保留研究样本，本阶段不再扩展源码追查，下一步改核具体公开实盘状态文件的可得性。** 这不是判断MegaVault没有盈利，而是当前没有满足完整原账及个人复制条件。

读取日北京时间2026-10-17；直接原件回执UTC约18:01—18:04。保存原URL、请求/完成时间、HTTP、原响应和SHA-256。原生web没有精确到秒的独立网络回执，保存时刻不冒读取时刻。只研究；0模型、训练、回测、策略、来源代码/SDK/SQL/EVM执行、账户连接、认证变更、凭证读取、注册、安装、付款、交易、外发、监控修改或删除。

## 1. 实际补齐的代码链

沿用UTC2026-10-01及原上下文块107552133—107696209，不换日期或挑赢家。链源码固定v9.7.0实际tag提交67a3c71670c460ef92ffb15930dda7047a901f64；indexer源码另固定当前提交50d70ca0f151a6a40f9fa62438d6d85b583d710c。未证明这些就是历史线上部署字节。

|步骤|实际证据|边界|
|---|---|---|
|区块结束归集|module.EndBlock→vault.EndBlocker，同一RunCached回调先RefreshAllVaultOrders，再SweepMainVaultBankBalance。|局部静态顺序，不是全应用顺序或运行证明。|
|银行存入子账户|复用已存固定源码，归集主银行全部正USDC余额，调用DepositFundsFromAccountToSubaccount。|并非只含协议费，其他银行入款未排除。|
|普通链上事件|type=sweep_to_megavault，业务attribute=swept_quote_quantums。|十进制最小单位，事件自身没有逐项收入来源。|
|索引余额消息|UpdateSubaccounts发SubtypeSubaccountUpdate，包含子账户ID、更新后的资产/永续仓位。|所读Sweep路径没有调用普通NewDepositEvent/TransferEvent构造器。|
|索引入库|BlockProcessor分开绑定SubaccountUpdate与Transfer的validator/handler；SQL文本分别写资产仓位和transfers。|不证明其他重建流程或实际线上完整覆盖。|

[链module](https://github.com/dydxprotocol/v4-chain/blob/67a3c71670c460ef92ffb15930dda7047a901f64/protocol/x/vault/module.go)、[EndBlock顺序](https://github.com/dydxprotocol/v4-chain/blob/67a3c71670c460ef92ffb15930dda7047a901f64/protocol/x/vault/abci.go)、[归集事件](https://github.com/dydxprotocol/v4-chain/blob/67a3c71670c460ef92ffb15930dda7047a901f64/protocol/x/vault/types/events.go)、[子账户更新](https://github.com/dydxprotocol/v4-chain/blob/67a3c71670c460ef92ffb15930dda7047a901f64/protocol/x/subaccounts/keeper/subaccount.go)。

## 2. 余额变动和转账表为什么不能混用

所读当前indexer将SUBACCOUNT_UPDATE交给SubaccountUpdateValidator/Handler，将TRANSFER交给TransferValidator/Handler。前者SQL更新subaccounts、perpetual_positions、asset_positions，全文没有transfers表引用；后者SQL第93行明确INSERT INTO transfers。均为只读文本核验，未执行SQL或连接数据库。

> 银行归集 → 子账户余额更新 → 资产/仓位表
>
> 普通Transfer消息 → 普通转账表
>
> 两者共同影响净值/PnL，但需要分别解释资金来源。

这是局部代码示意，不是完整线上架构或所有银行入款都不进入transfers的证明。wrapper在Enabled时传递原subtype、version和bytes；ProduceBlock注释要求EndBlocker收齐事件后打包，SendOnchainData封装IndexerTendermintBlock发送。具体addTxnEvent/produceBlock helper、EndBlock时tx-index归属、全模块顺序和其他重建仍未读，不称已发现线上漏账。

[分派](https://github.com/dydxprotocol/v4-chain/blob/50d70ca0f151a6a40f9fa62438d6d85b583d710c/indexer/services/ender/src/lib/block-processor.ts)、[余额更新SQL文本](https://github.com/dydxprotocol/v4-chain/blob/50d70ca0f151a6a40f9fa62438d6d85b583d710c/indexer/services/ender/src/scripts/handlers/dydx_subaccount_update_handler.sql)、[转账SQL文本](https://github.com/dydxprotocol/v4-chain/blob/50d70ca0f151a6a40f9fa62438d6d85b583d710c/indexer/services/ender/src/scripts/handlers/dydx_transfer_handler.sql)、[事件wrapper](https://github.com/dydxprotocol/v4-chain/blob/67a3c71670c460ef92ffb15930dda7047a901f64/protocol/indexer/indexer_manager/event_manager.go)。

因此426.065316仍为资产变化减已索引普通净转账的观察残差，不能叫纯交易利润。PnL相同差额来自同类定义，不是另一份独立盈利证明。原5%两点历史配置、783条转账及505.661256赎回证据保留，不能由新增源码自动升级为全部手续费收入或完整净收益。

## 3. 原区块读取的实际结果

精确复查已存107688409全部交易事件及finalize_block_events，sweep_to_megavault匹配0。这只是原选赎回区块，不推整个上下文没有归集。

CometBFT v0.38.19官方OpenAPI区分tx_search与搜索FinalizeBlock事件的block_search。知道Sweep在EndBlock后，本次对后者做一次不同的公开数据查询：原上下文、精确事件EXISTS、升序首条、per_page1。目的是找最早机制事件，不挑最高利润，也没有重发旧银行tx_search。

实际25秒超时，HTTP及正文UNKNOWN，保存0字节不是0条匹配。停止该方法，不重试或换host/代理/身份/认证；本阶段没有继续其他数据GET或用样例补原件。方法在官方规范存在，不证明该提供商配置与历史索引能完成查询。[官方原始OpenAPI](https://github.com/cometbft/cometbft/blob/v0.38.19/rpc/openapi/openapi.yaml)。

## 4. 个人可复制部分与本候选判断

**当前可复制的是核账方法，尚未证明可复制的正净利润。** 公开报价代码能帮助研究库存约束和收入分拆，却不会给个人地址自动附上主金库5%分成配置。持有金库份额也涉及准入、分成稀释、成本和风险，不能当独立预测能力。

本阶段不继续扩展该候选源码：补更多wrapper或表结构无法替代原收款、全历史持仓/成交/费用及个人收入权。若后续取得对应原账可恢复；目前不准入完整扣费盈利案例，不推出收益率、资金容量或财富目标可达性。查询超时不是案例不盈利或原件不存在的证明。

## 5. 下一候选：只确认线索

找到Trading Strategy的ETH-BTC-USDC momentum公开实盘策略线索。运营方2024-08-15文章称2024-04-01至08-05表现+7.87%，同文另一策略为负。这是运营方历史自报，未独立核成扣费净结果，不叫本轮成功案例。[原始文章](https://tradingstrategy.ai/blog/the-first-strategy-performance-review)。

官方State文档称JSON包含仓位、成交、存取款和估值；模拟与实盘共用结构，因此找到JSON本身也不能证明真实交易。[State文档](https://tradingstrategy.ai/docs/api/execution/help/tradeexecutor.state.html)。

下一唯一准入问题：这一个历史策略，是否可免费合法取得绑定实盘身份的原状态文件及事前公开规则版本，包含完整持仓、成交、费用和外部资金流？先核原件可得性、版本日期、实盘/模拟标识和个人复用许可；不运行模型、不回测、不连接钱包、不交易。只有营销曲线或模拟账则不通过，不替运营方补利润。

线索来自正表现公开筛选，不称完全盲选；实际状态文件、地址/版本绑定、完整费用和独立净利尚未取得。本阶段没有执行该策略、下载大包或新建项目。

证据：evidence/megavault-sweep-indexing-20261017/。原件及有用派生保留，原FAILED/9400/50/188、AAPL与Issue85及监控不变。新增机制/索引证据改变下一行动，属progress；财富目标未达，goal active。
