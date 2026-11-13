# 主账户原账观察：有限独立审阅

结论：余额与索引转账算术通过；426.065316 仍是未完全归因残差，不是已核手续费收入或净利润。原 block_results 的一项派生解码存在质量问题，已报根代理；本审阅直接使用原始明文字段，不依赖误解码值。全部实际输入 SHA/bytes、复算及 UTC 在同名 JSON。

两份历史 share 响应 HTTP200、未截断，metadata 高度各为 107552133、107696209；主地址均为 50000 ppm，即两个快照的 5%。另两地址比例不推现实归属，两端相同也不证明期间未变。

主子账户两端身份相同、number=0，只有资产 id0，无 perpetual position。quantums 1515567246150→1515578264891，按已核 USDC6 单位增 11.018741。Indexer 同高度 equity 各为 1515567.246150、1515578.264891，与此相符。两块时间 2026-09-30T23:00:54.608Z→2026-10-02T00:35:20.252Z，不能替代精确 UTC10月1日账期。

1000 条 transfers 原页无重复 id，height 降序，最早 107486405（9月30日10:43:19.359Z），越过下界。在该索引器排序及返回完整性条件下，覆盖本上下文所需前缀；不是全部 2654800 行已读或独立全链无漏证明。筛选 `107552133 < h <= 107696209` 得 783 行、779入/4出、7 unique tx；全部相关行 USDC 且主地址方向一致。

| 项 | 独立复算 USDC |
|---|---:|
| 索引流入 | 130.862669 |
| 索引流出 | 545.909244 |
| 净索引转账 | −415.046575 |
| 链资产增量 | 11.018741 |
| 资产增量减净索引转账 | 426.065316 |

用已保存下界 195 个 vault 的 owner 集合分类，776 笔流入合90.612669；另3笔来自两地址合40.25，其资金性质未核。分类只是已知集合，不冒充历史所有运营者或所有资本来源。

26 个 PnL ticks 的相同两端 `totalPnl` 为8074622.910008→8075048.975324，差426.065316。已读 pnl-ticks-helper.ts L333–336 使用 currentEquity 与历史净 transfers，L466–477 实现 `currentEquity.minus(totalTransfers)`。所以这里数字一致是同类记账定义的对应，不是另一份独立盈利验证，也不能据其命名排除未进普通索引的 sweep/deposit 或其他资产记账来源。

固定 block_results 高度107688409与 envelope 相符；在所返回交易及 finalize events 中未见 main 地址 `coin_received` 或名含 sweep 的事件。只支持此块此返回的观察，不证明整段无分成。bank tx_search 25秒超时、无响应正文、status UNKNOWN；不是零命中。

质量问题：原事件 `module=clob` 已为明文，旧派生 fixed-block-events-passively-decoded.json 将它启发式 base64 解成含控制字符的文本。请保留原件并纠正派生/明确不使用误解码字段。本审阅 main 收款/sweep 检查直接读原明文字段；金额残差不受这处字符串影响。

没有新增网络、模型、回测、源码执行、SDK、EVM或交易；model/effort实际UNKNOWN。PASS只限这些原件支持的状态、索引前缀与算术层，不表示盈利案例准入。下一项仍是实际银行分成与扫入原事件对应，不能把5%或426.065316当已完成全期收入归因。

补充复核：上述本地解析问题已解决。event-parser-correction.json 显式保留并作废坏派生；新 fixed-block-events-raw-attributes.json 的全部事件/attributes 与原件逐项一致，不再启发式解码。对 envelope 中明确 Base64 的 block.data.txs 只作被动字节解码及 SHA256，独立确认真实6笔交易，index2为E5AC…686C4A且result code0；withdraw_from_megavault 的 redeemed_quote_quantums=505661256，恰与已索引505.661256 USDC流出对应。旧通用summary的tx_count0来自错误字段位置，不能当空块。费用币种元数据未另核，未做美元/Gas换算。这一修正不改变残差或来源归因 UNKNOWN。
