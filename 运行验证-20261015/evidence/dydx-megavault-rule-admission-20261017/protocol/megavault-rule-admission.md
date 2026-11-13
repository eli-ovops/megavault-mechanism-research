# MegaVault公开规则、参数身份与许可准入

结论：官方vault模块已给出可读的报价、换撤单和库存收缩机械规则；**本轮未绑定历史链运行版本/参数，也未取得资本分配的完整选择政策或盈利原账**。不能把可见参数默认值当实际运行值，不能因为有operator就断言所有规则私有或个人不可研究复制。

固定官方仓库 `dydxprotocol/v4-chain` commit `50d70ca0f151a6a40f9fa62438d6d85b583d710c`。8次公开只读GET均200：代码搜索1次（前30/87项）、完整tree1次、6份固定源码/许可；逐文件Git blob SHA1已核。准确请求URL/UTC/HTTP/来源SHA/bytes及Git blob/html/download URL在read-receipts.json。未读取commit/release日期或历史链升级记录，首次公开、首次部署、当前或历史生产采用均UNKNOWN。

|环节|静态实际证据|边界|
|---|---|---|
|报价入口|orders.go L20–75遍历状态为QUOTING/CLOSE_ONLY的vault，无持仓且低于激活阈值则跳过；无独立参数则读默认值。|这里只证明函数逻辑，ABCI实际调用/周期文件本轮未读，不称已核每块运行。|
|换撤单|L80–154按价格/量/side变化替换订单，过期/全成交后翻转client ID重挂，取消多余订单；L458–540通过clob keeper内部下撤单。|不是全部旧单无条件先撤的注释字面流程；实际订单不变时保留。未执行这些函数。|
|报价/尺寸/库存|L156–417读oracle价格、market参数、equity/leverage、vault状态；订单尺寸约为order_size_pct×equity/oracle，并按量步长截断。spread调用lib/vault.SpreadPpm；按leverage、layer和skew因子构造双边价，按tick取整/限界。增加库存方向post-only，close-only只挂减库存方向并限总量。|分支实际按当前leveragePpm的符号（L281）选skew表达式，不把注释中的层杠杆表述替代代码。未展开SpreadPpm、equity/price计算依赖或clob所有实现，不称整个系统全部审完；无未知私人报价服务依赖证据。|
|资本分配|allocate.go L18–29仅允许HasAuthority或当前operator；目标VaultId和QuoteQuantums金额来自消息。|授权入口与金额参数公开，不等于决定市场/金额/时点的配置选择政策已公开。底层AllocateToVault实现/撤回资本本轮未读，是否另有自动分配算法UNKNOWN；不擅称全部人为主观。|
|参数和operator|set-vault-params.go L18–35允许HasAuthority或operator设置经Validate的单vault参数；update-operator.go L18–28只准HasAuthority更换operator。params.go L25–81有参数约束，L84–92默认operator为GovModuleAddress。|HasAuthority具体成员/治理提案路径本轮未读；默认Governance不等于任意历史时点实际operator。权限可变不证明实际改过。|
|默认值|params.go L11–21：2层、SpreadMinPpm10000、SpreadBufferPpm1500、SkewFactorPpm2000000、OrderSizePctPpm100000、60秒过期、激活阈值1000000000 quote quantums（源码注释1000USDC）。|全部是源码默认值，不冒当前或历史链状态参数，也不是各参数选择政策的证据。|
|许可|protocol/LICENSE开头明确以适用法律及v4 Terms of Use为条件授予下列AGPLv3权利，并写违反适用法律自动终止；后续AGPL文本有复制/修改/运行及网络交互相关条件。|不是无条件MIT或纯粹只需AGPL就可部署。前置v4条款未读取，个人具体部署/司法管辖与交易资格UNKNOWN；协议源码许可不自动授权交易、数据和第三方依赖。|

这比仅公开接口/框架更接近可核机械规则：给定正确执行版本、对应块的状态及参数，报价生成部分可以按公开代码解释；本轮未运行或重放。要把它绑定真实执行，最小公开身份链应包含相应链升级/应用版本记录与固定源码版本关联，再配对应块vaultId/子账户、市场oracle/equity/持仓、默认及单vault参数/operator、分配/参数变更消息和实际订单事件。该路径是依据已读函数输入作出的研究推断，未证明所有历史查询实际可得；根Agent另核indexer来源。

历史每次可见参数及变更不等于参数选择政策赛前锁定；有公开、事前可识别的完整政策才可按用户要求准入。反之，未知不等禁止或必须索要私人bot，也不要求形式化远程证明。当前只是规则/许可和版本身份的部分准入，未看收益、没有链账或净利结论。

actual model/effort UNKNOWN，继承设置不覆盖；PERMISSIONS-2026-10-14-v1和ROUTING-2026-10-13-v4不变。0来源源码/模型/回测/SDK/SQL/EVM/策略执行，0收益榜/链数据/账户认证凭证/安装/付款/交易/外发/监控。仅8次必要来源读取，停止扩展。依赖调用点/历史参数及时间证据的缺口明确保留，不当全代码无公开或全市场不能盈利。
