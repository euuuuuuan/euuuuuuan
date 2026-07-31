## Agent 编排 · 确定性门禁 · AI 增强开发

[English](README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · **简体中文** · [繁體中文](README.zh-TW.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · [Русский](README.ru.md) · [Italiano](README.it.md) · [Bahasa Indonesia](README.id.md) · [Türkçe](README.tr.md)

基于代码的自动化与 agent 编排系统：带 fail-closed 权限层的 agent 平台、
带评测门禁的本地检索流水线，以及可逐比特一致回放的确定性模拟。

本页上的每一个数字都是实测值，而非估算。产出该数字的命令就在它所描述的仓库里，
并且仓库在发布前会重新校验它。

---

## 旗舰项目

| 项目 | 是什么 | 实测事实 | 在线 |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | 面向有界 agent 工作的本地优先编排平台：一个位于 fail-closed 权限门禁之后的 Swift macOS 控制守护进程，由 TypeScript 编排器通过 MCP 驱动。*（平台）* | `docs/TOOL_SURFACE.json` 中收录 **63 个有文档的 MCP 工具**、**234 个测试/规格文件**、**15 份 ADR**，以及一个远程审批代理，其授权与指纹绑定且仅限单次使用（ADR-003）。 | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | 为网络隔离（망분리）环境构建的韩语 RAG：纯标准库实现的混合检索、PII 脱敏，以及针对超范围问题的拒答门禁。*（工具）* | **0 个第三方依赖**，构建由一套 **50 题黄金测试集**把关：hit@3 **100 %**（39/39），拒答精确率 **100 %** / 召回率 **81.8 %** —— 而引用准确率 **36.75 %**，这项最弱的指标被如实公布而非隐藏。 | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | 由 agent 通过 MCP 完成生命周期/CRM 营销人员的 SQL 工作，其上是一个治理内核，决定 agent 可以走多远、哪些内容必须记录在案。*（工具）* | **108 个测试通过**（在安装 MCP SDK 的前提下），其中包括针对内核的 **33 次 PII 红队**与 **12 次治理红队**攻击。数据 **100 % 合成**且可逐字节复现：seed 42 → 完全相同的 SQLite 摘要，5,000 名客户 / 99,922 条事件。 | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | 运行在浏览器中的战术破门规划器与实时小队模拟；打完的一场突袭可以作为 URL 分享，在别人的浏览器里重新模拟出来。*（可玩构建，非商业发行）* | **30 Hz 固定 tick 模拟**，配有纯净性门禁，在模拟与内容包中封禁 **11 个非确定性 API** —— 该门禁通过，正是 seed + 输入日志能够完全一致回放的原因。**55 个测试/规格文件**，`tools/` 下的 40 个脚本中有 35 个是无头测试装置（其余 5 个是资产抓取与代码生成）。 | **[试玩](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | 一台确定性推理装置：8 个 MCP 工具，强制执行范围锁定、最低成本反证步骤计划、盲点扫查，以及一个裁决门禁——凡拿不出执行证明的主张一律驳回。*（工具）* | **0 个运行时依赖**，**33/33 测试通过** —— 其中一个测试本身就是保证：整个工具面不做任何文件系统写入、不派生进程、不发起网络调用。 | — |

---

## 这些项目是如何构建的

有意思的部分不在于“用 AI 构建”，而在于位于模型与仓库*之间*的那一层。

- **两个编码 agent，一个确定性裁判。** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)
  是一个外部驱动器，让两个 agent CLI 相互对跑，直到门禁通过。“完成”由门禁裁定 ——
  而绝不是 agent 对自己工作的自评。12 个单元测试，在 `PATH` 被削减到仅剩系统目录的情况下依然通过，
  因此无需安装任何一个 agent 即可证明驱动器的拒绝行为。
- **确定性门禁在模型被调用之前就已运行。** 是纯净性检查器，不是凭感觉：
  fatal-funnel 的模拟门禁封禁 11 个非确定性 API；reasonforge 的只读测试强制 6 种
  禁止模式。检索质量同样是构建门禁 —— 黄金测试集加阈值，一旦回归就让本次
  运行失败（[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)）。
- **UI 结论来自探针与截图，而非记忆。** [hollowmere](https://github.com/euuuuuuan/hollowmere-public)
  自带 `shot`、`relic-qa` 与 `mobile-qa` 测试装置；fatal-funnel 在 `tools/` 的 40 个脚本中有 35 个无头测试装置，
  外加 4 台固定机位的截图相机。这一课的学费很贵：一个“能用”的悬浮提示，可能因为一个
  输入过滤标志而彻底失效；合成输入探针能抓住它，读代码抓不住。
- **资产在生成之前就要过许可证门禁。** [assetforge](https://github.com/euuuuuuan/assetforge-public)
  拒绝用任何未登记在其注册表中的模型进行生成。在下游，**全部 27 个已发布快照
  都附带一份含 4 级再分发表的 `CREDITS.md`** —— 任何 fork 的人都能确切知道
  自己必须移除什么。
- **诚实的标注，由 schema 强制执行。** 在 [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  中，每一个公开发布的指标都被类型化为 `kind: z.enum(['measured', 'estimated'])`。不声明属于哪一类，
  数字就无法发布。正是这条规则，让本页上没有任何取整的营销数字。
- **发布是一条流水线，而非复制粘贴。** 这里的每个公开仓库都由一条可重复的
  脱敏发布流程产出，其声明文件会重新执行 README 中的每一个数量，一旦发生漂移就拒绝
  创建提交。27 个快照合计 **629 个测试/规格文件**。

---

## 精选作品

并非全部 27 个仓库 —— 只列出能展示某项独特能力的那些。

**浏览器游戏（打开链接即可游玩）**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** —— 确定性战术模拟，40 种武器，12 个手工设计的任务 · [在线](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** —— 紧凑的动作 RPG：招架、遗物、两个区域。*垂直切片，当前 v0.5.1* · [在线](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** —— 关于排班配比的小而完整的放置游戏，18 个测试文件 · [在线](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** —— 等距 ARPG 切片。**与 hollowmere 共享同一个确定性模拟核心**；战斗规则、遭遇与内容均为其自有。 · [在线](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** —— 三波次等距竞技场：读懂前摇预警、串联 mooncut 连段、用冲刺躲开已然出手的攻击。**43 个测试文件**，以及全组最严格的资产台账 —— 100 条逐文件 SHA-256 记录，全部 CC0。 · [在线](https://moonshard-warden.vercel.app)

**引擎游戏（Godot / Unity —— 垂直切片与原型，均未商业发行）**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** —— 太空中的主力舰炮战：炮弹飞行时间、惯性提前量、装甲入射角穿深，运行在确定性 30 Hz 模拟之上（27 个测试文件）
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** —— 单人 Boss 团本 ARPG，十个三阶段 Boss；“团队绝不替你通关”这一说法是战后实测统计，而非强制不变式 —— 若最后一击由队友打出，战报会如实写明（33 个测试文件）
- **[todak](https://github.com/euuuuuuan/todak-public)** —— 常驻在屏幕底边的桌面像素宠物（36 个测试文件）
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)**（30） · **[ragtail](https://github.com/euuuuuuan/ragtail-public)**（29） · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + 它的 **[Unity 移植版](https://github.com/euuuuuuan/driftfolk-unity-public)** —— 移植版的意义就在于与原版的数值一致性 · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** —— 定向自治的大逃杀，Unity。之所以单列，是因为这份快照是**验证骨架，而非可玩切片**：已交付的部分是确定性模拟及其黄金文件测试装置，15 条带编号的浸泡测试断言中仍有 11 条待完成。

**AI 与 agent 工具**
- **[baton](https://github.com/euuuuuuan/baton-public)** —— agent 平台：63 个工具的 MCP 工具面、审批代理、16 个编排器包、90 个 Swift 源文件
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** —— 只读推理装置，8 个 MCP 工具，0 依赖
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** —— 两个编码 agent 之间以门禁为终点的循环
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** —— 本地零成本资产生成，置于 fail-closed 许可证门禁之后
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** —— 生成 → 评判 → 组稿 → 发布，“拿不准”会让这次运行原地搁置，而不是照常上线

**数据、后端与领域工具**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** —— 多租户资产管理，租户边界落在 PostgreSQL 里：11 个迁移文件中共 **82 条行级安全（RLS）策略**，另有 6 个 pgTAP 测试文件专门对准它们 —— 而不是应用层过滤
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** —— 以评测门禁为核心卖点的检索
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** —— agent + 治理内核，运行在全合成的 CDP 之上
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** —— 面向韩国金融/保险广告文案的初审 linter。已交付的全部 5 套规则集在数据本身中就标记为 `"verified": false`，报告不带免责声明便无法打印：它只是标记文案供人工复审，并不认证合规
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** —— 既可以演奏乐器，也可以编写音序；同一首歌，从两侧都能无损编辑（27 个测试文件）
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** —— 双语静态站点，每个数字都声明自己是实测还是估算 · [在线](https://euuuuuuan.pages.dev)

---

## 关于这些仓库

全部 27 个仓库都是脱敏快照：代码采用 Apache-2.0 许可，资产由各仓库自己的 4 级
`CREDITS.md` 管辖。游戏均为个人构建与垂直切片 —— 可玩、有门禁把关、标注诚实，
并非商业发行。

**联系方式：** 在此处任一仓库开一个 issue 或 discussion 即可。
