# BTC身份、上下文状态与原账观察独审

限定PASS，material_errors=[]。不是精确UTC日、完整净利润或盈利案例准入。实际model/effort UNKNOWN，规则版本不变；本次0新网络与执行。

- 官方BTC-USD实际市场响应clobPairId=0；三份实际Vault列表均唯一匹配VAULT_TYPE_CLOB/0、owner `dydx1c0m5x87llaunl5sgv3q5vd7j5uha26d2q2r2q0`、subaccount0。未本地运行地址派生。链元数据分别为109718530、107552133、107696209，历史两者与请求高度相同；分页next_key=null，total字符串0不等于没有返回195条。
- 三快照均195条、186 CLOSE_ONLY/9 QUOTING，所有inventory为0；9 QUOTING各自quoting_params均null，需要实际default状态，本轮未取得，不以源码默认补齐。不由快照推出全期间没交易。
- 历史上下文高度107552133对应Indexer报告2026-09-30T23:00:54.608Z，高度107696209为2026-10-02T00:35:20.252Z。均非精确午夜，不能替换事前固定UTC10月1日窗口。
- 原始历史quote quantums为5801036086→5799293883；除1e6为5801.036086→5799.293883，与Indexer同高度equity一致。上下文原转账100条按高度递减，最早越过起点；其中4条唯一USDC转账从子vault/sub0到主MegaVault/sub0，1.613738+0.031913+0.064635+0.031917=1.742203。余额变动−1.742203减已观察净转账−1.742203，残差0.000000。不是新利润、投资者分红或原生Gas已核全；只证明所见区块上下文的现金量解释。
- 原funding参数afterOrAt/zeroPayments返回100条2025记录；实际路由源码L179起用createdOnOrAfter/showZeroPayments。修正请求HTTP200、fundingPayments=[]/totalResults0，原件保留。查询只有10月1日下限、没有上限，不将其独立升级成全链完整资金费零。
- fills带page1有100条/totalResults36974，page370为403并停止。此前已并发计划的无page请求200、最新100条止2025-12-17；它不是403空终页、全量替代或全链没有该日成交的证明。无换host/身份或重试证据。
- 官方Resources表将105002000起列v9.7.0，只作为官方版本来源层进展；不是独立构建/runtime或所有历史参数核验，不能用v7.0.0冒主网实际版本。

41份实际原响应、回执、少量必要源码和派生输入绝对路径/SHA/bytes及独立计算见同名JSON。原量、时间、参数和分页返回一致，无需修正。仍缺精确边界状态、完整fills/费用及相关资产负债变化；认可现金事实不额外要求私人bot。未改root、报告或他人文件。
