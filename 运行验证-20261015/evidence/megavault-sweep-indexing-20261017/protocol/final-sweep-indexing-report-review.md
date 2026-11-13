# 归集与索引报告终审

**限定PASS，material_errors=[]；不是盈利案例准入。** 报告7851B，SHA256 `a658e79ff48c80534e943f843c17cd35d685f2fbe75db38e7c0369100b6f4b81`。37项实际读取输入绝对路径、SHA256、bytes和审阅UTC见同名JSON。model/effort实际UNKNOWN；规则版本未变。

实质核查：固定当前indexer的BlockProcessor确将两种subtype分别给两套validator/handler；SubaccountUpdate SQL更新子账户/资产/永续仓位，全文无transfers引用；Transfer SQL明确向transfers表插入。8份源码均与保存的API内容及Gitblob字节指纹相符，全部只读文本，未执行SQL或来源代码。这支持报告的局部两路区别，不证明完整生产覆盖或所有银行路径必然不入普通转账表。

报告准确保留v9.7.0链版本与当前indexer版本差异；wrapper的事件暂存/打包意图没有被提升为具体helper实现、EndBlock tx归属或全应用顺序证明。单块0个精确Sweep不推期间无分成；block_search超时无正文不作0条；426.065316未冒全部协议收入、纯交易利润或完整净利润。个人公开函数/模型能力与指定分成收款权分开，未新增私人bot完整公开门槛。

下一候选文章原抽取明确为2024-08-15运营方文章，期间4月1日至8月5日，自报ETH-BTC-USDC +7.87%，并列另一策略−4.63%。报告没有把它当本轮独立扣费净盈利、事前完整公开规则或已绑定实盘JSON。

来源缺口已解决：最初整页State快照只保存到导航L640，本次另提供state-doc-exact.native-web.saved.json，官方正文L2896–2903实际明确positions/trades/deposits/withdrawals、JSON及模拟/实盘共用结构。因此终稿该句有原文支持，不需改正文。schema共同结构不能单凭文件存在证明真实交易，这一限定保留正确。

正文Markdown无本地文件链接，证据目录导航存在；重要外链对照已存固定源码/网页URL，不新增联网刷新。未发现实质修改项；0新网络、模型/回测/SDK/SQL/EVM/策略/交易执行，审阅收口，不扩展验证。
