# BTC子金库身份来源与最小状态查询路径

结论：已核官方源码中vault↔clobPair↔地址/子账户的结构，以及可直接返回子账户的公开协议查询schema；**尚未取得BTC-USD对应的字面子vault地址或实际历史状态响应**。不执行地址派生，不把主MegaVault地址冒成BTC子vault。

范围固定BTC-USD，UTC2026-10-01含至10-02不含；已见整个MegaVault汇总，非完全盲选，不换市场/日期。本轮8次官方GitHub GET均200、每次25秒/1MiB预算内，固定commit `50d70ca0f151a6a40f9fa62438d6d85b583d710c`，保存实际响应及逐文件Git blob指纹；未读取持仓/成交/收益数据。

|原件与行号|已核事实|不能替代的证据|
|---|---|---|
|postgres-constants.ts L137–140；module_accounts_test.go L41/104|字面主MegaVault模块地址为 `dydx18tkxrnrkqc2t0lr3zxr5g6a4hdvqksylxqje4r`，内部DB子账户UUID `c7169f81-0c80-54c5-a41f-9cbb6a538fdf`，源码注明主地址+子账户0。测试文件是官方静态期望，未运行测试。|不是BTC子vault、交易者EOA或历史链实账。UUID不是公开链地址，也不能直接传作address参数。|
|vault-types.ts L3–24；vault-table.ts L21–76|Vault行有address、clobPairId、status、createdAt、updatedAt；DB层可按address/clobPairId/status过滤，默认按clobPairId升序。|不是已读取DB/公开HTTP响应，没有给BTC的实际clobPairId或地址。未运行SQL。|
|comlink-helpers.ts L756–785|读取VaultTable全部行，以vault.address及子账户0构造内部UUID；用clobPairId查询市场，未找到市场的行跳过。|代码支持映射机制，不提供字面BTC映射；不假定BTC clobPairId=0或“列表第一项”。|
|vault_id.go L57–80|模块地址由`vault-<type>-<number>`经NewModuleAddress派生；ToSubaccountId为该地址的子账户0；CLOB订单使用id.Number作为ClobPairId。|只有算法，未执行hash/Bech32/SDK或源码派生，未把计算值当链返回。|
|keys.go L25–41|默认报价、单vault参数、地址、clientIDs、operator有各自存储键。|存储键不是HTTP端点或实际历史参数。|

最小官方**查询schema**见vault-query.proto：

- L21–28：`GET /dydxprotocol/vault/vault/{type}/{number}`；或先用 `GET /dydxprotocol/vault/vault`，后者不需猜type编码。
- L77–92：响应有vault_id、subaccount_id、equity、inventory、vault_params、most_recent_client_ids；因此可从实际响应取得地址/子账户，不必自行派生。
- L95–104：AllVaults带PageRequest/PageResponse，不能把单页当全量；具体host、参数JSON编码及实际分页可用性本轮未请求。
- L18–19、46–48、65–67：默认/operator参数及特定vault参数查询schema存在；不证明当前或历史状态可匿名取得，也不提供历史高度参数语义。

下一最小实际身份桥：官方市场记录给出BTC-USD的clobPairId，再与官方Vault查询返回的vault_id、subaccount_id及相关状态记录匹配；若用列表，须核终页，不依据顺序猜市场。应另确认对应链及固定日期的版本/参数有效性；当前源码晚于固定窗口，不能倒填历史。此处仅准入来源和schema，根Agent决定实际支持的只读数据路径；本子任务不发链查询，也不触碰fills/funding/transfers控制器。

引用字段虽有equity/inventory，不因此算收益或解释API单位。constants.ts L29–34含quote atomic resolution−6及USDC符号，同时USDC_DENOM是`ibc/xxx`占位，不可用它认定实际链上代币denom。公开机械映射无须私人bot才能研究，但当前没有字面BTC身份，保留UNKNOWN，不称不存在或永久不可查询。

actualmodel/effort UNKNOWN；PERMISSIONS-2026-10-14-v1、ROUTING-2026-10-13-v4不变。0模型/回测/来源代码/SDK/hash派生/SQL/EVM/策略执行，0账户认证凭证/安装/付款/交易/外发。每次URL/UTC/状态、原件SHA/bytes、Git blob及固定路径在read-receipts.json；本报告JSON绑定实际输入。本轮按8GET收口，不扩大查询或换候选，不改root/报告/全局和他人文件。
