# MegaVault历史发布与报价版本身份补审

结论：新增官方2024年发布记录与固定旧版源码身份，**同类vault报价机械规则不再仅有今天源码**；但实际链升级高度、生效版本、历史参数和独立首公开时刻仍未证明。未读取收益、账户账或候选表现，5次公开读取后停止网络。

1. 当前固定commit `50d70ca0f151a6a40f9fa62438d6d85b583d710c` 的Git author/committer date均为2026-10-15T21:01:59Z，消息为无关vault的gzip修复。这是提交元数据，不是独立首公开封存，更不能用此当前commit直接代表2024/2025执行版本。
2. 官方[protocol/v7.0.0发布记录](https://github.com/dydxprotocol/v4-chain/releases/tag/protocol/v7.0.0)的API记录published_at为2024-11-04T17:28:34Z、created_at为2024-10-26T21:22:50Z。正文明确列出MegaVault，接受USDC为多市场提供流动性、向存款者分配收益或损失；要求从v6.x破坏性升级，upgrade name为`v7.0`，并列迁移vault份额/初始化MegaVault账户等变更。这里的发布/API记录不是主网实际升级回执。正文仍写“Release candidate”，API虽draft=false/prerelease=false，也不能据此认定某链何时实际采用。
3. 官方tag ref `refs/tags/protocol/v7.0.0` 的object明确type=commit，SHA为 `fdb1fd329fa1b7000b07946213e26ef8872280a8`，与release target_commitish相同。这不是仅猜tag或用branch名替代commit。release API目前immutable=false，独立历史存档及tag从未改写证明未取得，不冒精确首次公开。
4. 按该确定旧commit取得 `protocol/x/vault/keeper/orders.go`，19614B，SHA256 `036b505aa380f1e725d46043f97726e9ec28c880f24625bcc6ba5e7555ead0e7`，Git blob SHA1核对通过。L20–154有刷新/换撤单，L156–417有oracle/equity/库存偏斜报价和close-only，L458–540有内部下撤单调用。可将报价模块源码与官方v7发布版本关联，不等于已证明链上执行使用此字节。
5. 与本轮原current orders.go逐文本比较，仅一处差异：旧版L48含 `vault.PerpetualPositions == nil || len(vault.PerpetualPositions) == 0`，当前为 `len(vault.PerpetualPositions) == 0`。保留两份原字节及不同SHA，不规范化成相同文件；未编译/运行，也不把这一个文件近同当整个应用依赖、构建或runtime等同。

可接受的来源层：官方平台记录支持2024发布过含同类MegaVault报价模块的版本；当前commit日期与该旧版本严格分开。不能从发布说明自动确定主网上线时刻、现有链全部升级、特定账期参数/分配政策或盈利。上一份准入报告的当时UNKNOWN保持历史状态，本补充只更新已有新证据。

下一最小缺口是**选择相关链/账期的实际v7.0或后续升级生效记录与版本对应，再核该块vault/operator/参数及变更记录**。官方明确部署/升级声明也可按来源等级采用，不要求形式化远程证明或私人bot。是否能取这些历史状态由根Agent另行核，不在本次扩查；参数选择政策与许可前置条件也未因此补齐。

5次官方gh只读GET均200（当前commit、发布列表前100条、v7发布、tag ref、固定旧版orders）。第一份发布列表未涵盖旧v7，故按已观察官方升级目录/标签命名直接定位，未称列表穷尽。原URL/UTC/hash/bytes及Git文件身份见historical-read-receipts.json；实际输入绝对路径/指纹见同名JSON。actual model/effort UNKNOWN；规则版本不变，继承设置不覆盖。0新模型/回测/源码/SDK/SQL/EVM/策略执行、0账户/安装/付款/交易/外发，0收益或链账数据。
