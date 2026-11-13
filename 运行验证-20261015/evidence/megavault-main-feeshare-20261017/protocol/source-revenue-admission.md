# 主 MegaVault 协议分成：固定版本源码准入

结论：公开源码足以区分「协议手续费分成先进入银行账户，再扫入主子账户」与「子池资本转回主池」。它不直接给出这一天实际分成总量，也不能把普通 transfers 单页或累计 PnL 增量全部归为协议收入。本次为静态规则审阅通过，非盈利账本准入。

版本：官方 `protocol/v9.7.0` tag 在实际 GET 返回中指向 `67a3c71670c460ef92ffb15930dda7047a901f64`。已读取 9 次 GH GET，全部停止；逐 URL、UTC、原件/源码 hash、Git blob 与实际字节见 read-receipts.json 及同名 JSON。tag 映射不独证首次公开时间、实际历史链执行代码或许可范围。模型/effort 实际值 UNKNOWN；规则 PERMISSIONS-2026-10-14-v1、ROUTING-2026-10-13-v4 沿用。

| 已读原文位置 | 能支持的规则 |
|---|---|
| revshare-query.proto L30–35、L61–67；query-unconditional.go L10–20 | `GET /dydxprotocol/revshare/unconditional_rev_share` 返回 `config`；其 `configs[]` 的 `address`、`share_ppm` 见 revshare.proto L16–28。handler 读模块状态，不返回已付账。历史高度能否正确应用还须实际响应 metadata 绑定。 |
| revshare.go L208–249、L418–439 | 每 fill 的基数起于 taker fee + maker fee；有 affiliate share 时扣 affiliate，否则扣适用的 order-router shares。剩余正基数再按各收款配置的 ppm 计算 unconditional share；并非成交额、所有毛手续费或固定 50%。调用 BigMulPpm；本次未读该 helper，不另断言其实现细节。 |
| process_single_match.go L537–565 | 撮合处理调用 GetAllRevShares，随后 DistributeFees；前者错误分支会记录错误并使用空 RevSharesForFill，因此不能只由资格规则断言每笔必已分成。 |
| subaccounts-transfer.go L225–267、L306–379 | DistributeFees 遍历收款配置；TransferFees 用 ConvertAssetToCoin 后 bankKeeper.SendCoins 从 collateral pool 银行账户付款。只支持此处 USDC 资产；负量分支会反转地址，不能无视符号。余款给 fee collector。所谓 “Emit revenue share” 下实际是 metrics 遥测，不是此处专用链上收入事件。 |
| sweep_funds.go L10–53 | SweepMainVaultBankBalance 查主 MegaVault 银行地址的 USDC 余额，正余额全部存入主子账户，成功后 CosmosEventManager 发 NewSweepToMegavaultEvent。扫的是所有该币银行余额，不只协议分成。 |
| subaccounts-transfer.go L73–90、L95–159 | DepositFundsFromAccountToSubaccount 先 bank SendCoins(main bank→collateral pool)，再 UpdateSubaccounts(..., types.Deposit) 增记主子账户。此已读函数没有直接发普通子账户 Transfer；UpdateSubaccounts 下游及 indexer handlers 未读。 |

普通 transfers 索引完整覆盖银行付款、sweep、Deposit 的能力目前 **UNKNOWN**。源码证明不同处理路径；不足以声称 API 必然漏记，更不能以单页余额差补出收入。Sweep 事件的精确类型/attributes、调用调度和每次 sweep 对应来源未在 9 次预算内读到，保留 UNKNOWN。标准 bank 事件是否完整保留及查询接口也未在本轮核实。固定日收入应以实际历史配置、实际付款/扫入原事件及子账户资产变化交叉核对，避免银行付款与随后扫入重复算两次收入，避免把子池本金转回计为新收入。

根代理另告知两上下文高度实际 state 中主地址 share_ppm=50000；这不是本文件独立复读的输入，也不由两快照推整段比例恒定或实际总收入。本文件不使用默认值或旧文 50%，不解算收益、不读根的 transfers/PnL。

本轮没有读取该 tag 的 LICENSE，不新增个人部署/商业复用许可结论；也未独立编译、执行源码、请求节点或数据、运行模型/回测/SDK/EVM/交易。下一最小缺口是收入处理链的实际事件/状态对账，而非私人 bot 或全企业多年历史。
