# MegaVault BTC：固定窗口原账核验

**结论：身份和免费历史状态入口已经打通，但这个BTC样本没有提供可复制的扣费盈利证据。** 实际观察到的是BTC子金库把资金调回主金库：上下文两块余额下降1.742203 USDC，恰好被四笔内部转账解释。不能把主金库收到这笔钱当新增利润，也不能把子金库余额下降当交易亏损。本轮没有完整成交账，不将余额核对残差0写成已核净利润或收益率0%。

本轮先固定BTC-USD、UTC2026-10-01 00:00:00含至10-02 00:00:00不含，再读取该子账户数据；理由是参考市场和完整日历日，不依据个别收益。此前已经看过整个MegaVault汇总，保留这一事实，不称完全盲选。没有按正收益换币、换时段。本轮仅研究：0模型、回测、策略或来源代码/SDK/SQL/EVM执行，0账号认证凭证、安装、付款、交易、外发或监控变更。

来源读取日期：北京时间2026-10-17，直接API实际UTC取得时间17:18—17:26，逐件在回执；网页原生抽取请求秒数UNKNOWN，保存时间不冒取得时间。保存原响应和SHA-256，不依赖动态链接未来仍返回相同内容。

**1．真实身份，不是猜地址。**

|身份环节|实际证据|
|---|---|
|市场|官方indexer对BTC-USD返回clobPairId=0。|
|金库|官方文档列出的Polkachu归档REST返回vault_id={VAULT_TYPE_CLOB,0}。|
|账户|相同记录明确给出owner=`dydx1c0m5x87llaunl5sgv3q5vd7j5uha26d2q2r2q0`、子账户0。|
|主金库|官方固定constants源码字面地址`dydx18tkxrnrkqc2t0lr3zxr5g6a4hdvqksylxqje4r`，与四笔转账收款方一致。|

没有运行地址派生或SDK；内部数据库UUID不作公开链地址。来源：[BTC市场元数据](https://indexer.dydx.trade/v4/perpetualMarkets?ticker=BTC-USD%29[官方查询schema](https://github.com/dydxprotocol/v4-chain/blob/50d70ca0f151a6a40f9fa62438d6d85b583d710c/proto/dydxprotocol/vault/query.proto)[固定地址常量](https://github.com/dydxprotocol/v4-chain/blob/50d70ca0f151a6a40f9fa62438d6d85b583d710c/indexer/packages/postgres/src/constants.ts)[官方节点与升级资源页](https://docs.dydx.xyz/nodes/resources)

Polkachu是官方文档列出的公共节点提供者，不因此称基金会亲自运行或给财务数据担保。返回AllVaults为195条、next_key=null；未请求count_total时total字段0不当“没有金库”。indexer的268条历史/当前快照与这里195条不能仅凭数量差推导资金消失。

**2．两份真正取到的历史块状态。**

使用Cosmos公开历史查询header指定高度，响应metadata均与请求高度一致；并非只下载了当前状态后改日期。对应blockTime来自独立取得的indexer历史tick，未另外取原始区块头或密码学证明，不把节点自报高度升级为独立共识验证。[Cosmos官方历史REST header示例，2024-01-24活动资料](https://github.com/cosmos/testnets/blob/master/testnet-tuesdays/gameday01/README.md)

|上下文状态|起点|终点|
|---|---|---|
|区块高度|107552133|107696209|
|indexer blockTime（UTC）|2026-09-30 23:00:54.608|2026-10-02 00:35:20.252|
|节点原始equity（quote quantums）|5801036086|5799293883|
|对应USDC计价余额|5801.036086|5799.293883|
|库存inventory|0|0|
|状态|CLOSE_ONLY，仅减仓|CLOSE_ONLY，仅减仓|

**这两个时点不是10月1日零点和10月2日零点。** 它们仅为固定日两侧的上下文核对，不改固定日定义，不当该日精确收益。USDC计价单位依据源码quote atomic resolution=−6及USDC标识；按百万分之一换算，与indexer相同两高度equity精确对应。源码USDC_DENOM是占位符，未用它认定实际链上代币denom；计价金额也不当法币出金。

官方升级表将105002000起列为v9.7.0适用区间，两个107百万高度落在其声明区间。这比仅看到2024发布说明更具体，但还不是取得v9.7.0精确源码、实际块级应用运行回执及期间全部参数变更；不拿当前2026-10-15 commit倒填10月1日。两个快照CLOSE_ONLY/库存0也不证明所有中间块都没改参数或交易。

**3．资金究竟去哪里。**

已取得按终点高度截断的转账第一页100条，最早到2026-08-26，早于上下文起点；据返回的降序页与字段筛出区间内四笔，全部是BTC子金库转给主MegaVault的TRANSFER_OUT、symbol=USDC。固定日请求与上下文高度请求返回相同原响应SHA，未借新截止点更换核查日。

|UTC时间，2026-10-01|调出USDC|交易哈希|
|---|---:|---|
|09:35:51.050|0.031917|`9C95CDB2BFDF738478BF4817BB22DC76AF9A1ECD4C79AFEAA1A08DD06EA997AC`|
|11:13:24.753|0.064635|`2C5E9BC3C7F83F97E2B3BD6EC91912E6AE13542F1B19239A809C6FDCB6F400BC`|
|20:22:36.275|0.031913|`5BF3CCE7D2D5DCC7799204F670216AEA982C48D7D35226FA89FF58E48F41C6BE`|
|23:12:40.451|1.613738|`E5AC87055CD4CFBB10A05C4A6F3F40910CDC2CF9518C798A6BA0F3C827686C4A`|
|合计|**1.742203**|四个不同哈希、四个不同记录ID|

实际余额核对：

`5799.293883 − 5801.036086 = −1.742203 USDC`

`余额变化 − 已记录净转入 = −1.742203 − (−1.742203) = 0.000000`

这是观测区间的现金/净值对应核查，计算的是已有原账，不是策略回测。原件见root/context-transfers.original.json、archive-context-before-start/after-end.original.json；context-cash-accounting-observation.json保存筛选条件、原记录及算式。转账接口来源：[公开BTC子账户转账](https://indexer.dydx.trade/v4/transfers?address=dydx1c0m5x87llaunl5sgv3q5vd7j5uha26d2q2r2q0&subaccountNumber=0&limit=100&page=1&createdBeforeOrAtHeight=107696209%29

推断限度：四笔是池内调拨，合并看整个MegaVault时应避免把内部转出和收款重复算为新收入。余额变化恰好由它们解释，未显示另一个未解释增量；这不独立证明完整净利润、期间全部费用为零或完整现金流从无缺失。四笔链交易的全部消息、Gas付款主体及完整费用尚未逐笔取得；不把内部调拨本身标作手续费或收益分配。

**4．成交、资金费：有原件，也有实质缺口。**

|原件|本次实际结果|结论边界|
|---|---|---|
|成交分页第一页|HTTP200，100条，offset0/pageSize100/totalResults36974；时间2024-09-13至09-22。|API页码模式按eventId升序，第一页不是最近交易，不能直接称目标日没有成交。|
|事先计划的最新成交对照请求，不带page|HTTP200，最近100条止于2025-12-17，早于固定日。|该返回未显示目标日成交；不当完整36974条或供应商绝无遗漏。|
|成交第370页|一次HTTP403，4906B、Cloudflare拒绝页；停止该资源。|没有重试、换身份/工具/host/代理，也没把另一份已并发发出的响应当拒绝页替代。全历史分页未完成。|
|按Swagger命名的资金费请求|HTTP200，却返回2025年数据，早于请求的2026-10-01下界。|请求成功不证明筛选参数生效，不能据此算窗口资金费。|
|按实际路由参数重新查询|HTTP200，totalResults=0，fundingPayments=[]。|供应商对该查询没有返回资金费记录；不是独立证明一切资金费/其他费用从未发生。|
|历史PnL窗口和两侧一小时上下文|HTTP200，26条；两端equity与节点状态换算吻合，26条totalPnl相同。|聚合累计损益起点/费用定义未完全核，不把累计值当固定日损失或完整净利润。|

关键源码证据：Swagger的方法签名使用`afterOrAt`和`zeroPayments`，但实际HTTP路由从matchedData读取`createdOnOrAfter`和`showZeroPayments`，再传入控制器。修正普通公开GET后才得到上述零条；未改变认证或绕过拒绝。保留错误口径与修正原响应，不能只留下“看起来正确”的一个。[资金费路由L164–199](https://github.com/dydxprotocol/v4-chain/blob/50d70ca0f151a6a40f9fa62438d6d85b583d710c/indexer/services/comlink/src/controllers/api/v4/funding-payments-controller.ts#L164)[成交控制器](https://github.com/dydxprotocol/v4-chain/blob/50d70ca0f151a6a40f9fa62438d6d85b583d710c/indexer/services/comlink/src/controllers/api/v4/fills-controller.ts)

已读transformer直接输出fee、可选builderFee/orderRouterFee、affiliateRevShare等，没有在这里证明它们能无条件相加，或某项已包含另一项。缺完整原始事件与费用定义时，不自行填零或重复扣费。[响应转换源码](https://github.com/dydxprotocol/v4-chain/blob/50d70ca0f151a6a40f9fa62438d6d85b583d710c/indexer/services/comlink/src/request-helpers/request-transformer.ts)

读取说明：不带page的对照与第370页请求在收到403之前已按书面计划同时发出，绝非403后的换参数访问；之后没有进一步成交请求。原先身份读取脚本在响应、回执已落盘后发生输出格式NameError；本地重新解析已有文件，没有重复联网。两次后续本地摘录/统计分别遇到超行号及null参数类型，均只修解析，不冒供应商失败、模型失败或新的交易尝试。

**5．这个样本现在能教你什么。**

已读实际地址、历史库存/状态、原始现金流，并纠正了一处会影响结论的接口口径。你可以低成本复制的研究环节，是**先识别账户和资产，再把余额变化拆成外部资金、内部调拨、交易盈亏与费用**。看到收款不立即叫利润；看到余额下降也不立即叫亏损。AI适合帮助整理原账和交叉核查，结果仍须与原响应独立对上。

“库存约束报价”仍可作为源码学习环节，但本BTC固定样本不能证明该模块在本时段赚取价差；当前CLOSE_ONLY也不能当持续双边做市。取得整体195条状态的三个快照中，均186个CLOSE_ONLY、9个QUOTING、库存均0；九个QUOTING的单vault quoting_params均null，需链上默认参数，未取得就不能把源码默认值代入判断是否有效挂单，更不能泛化为整个MegaVault期间从未交易或没有盈利。

**本候选结论收敛：保留研究样本；该固定BTC窗口不准入为扣费盈利案例。** 没有因此证明MegaVault全业务不赚钱，也没有换币或换日寻找正数。个人盈利优势、实际资金容量及达到Gerko财富1%的路径，仍没有被这份证据证明。

下一步最重要的未知转为：**在同一固定日，主金库损益中的协议费用分成能否取得独立原件，并与子金库内部调拨和交易损益分开。** 这直接决定“钱来自服务收入/补贴，还是报价交易”，也决定个人能否获得同样收入。先读官方分成生效规则、收款与计费原账的可得性，不延续受拒成交页、不按赢家换市场、不运行模型/回测/交易。若不能拆开，MegaVault不作为可复制交易盈利的证明。

证据目录：evidence/megavault-btc-ledger-identity-20261017/。root负责资料读取与报告/总台账；protocol子Agent负责独立身份/原件复核。原FAILED、9400/50/188、AAPL、Issue85及监控保持不变；本轮有新实质原件和现金流对应证据，分类progress，财富目标未达、goal active。
