# 平台适配：按能力解析，不按品牌猜测

核对日期：2026-10-27。适用于能够阅读指令的Agent；下列Codex、Claude、WB（WorkBuddy）、豆包工作和Kimi Code是已整理的示例，不是支持范围白名单。Claude需区分Claude Code与Claude网页/桌面/Cowork；WB不等于CodeBuddy IDE；Kimi Code不等于普通Kimi对话。能力以当前宿主提供的工具、配置和实际授权为准。

## 通用使用方式

先利用当前对话已提供的能力信息；只有实际任务缺少必要能力时才检查，不要求用户先填写平台配置。

| 当前能力 | 执行方式 |
|---|---|
| 支持技能加载与项目文件读写 | 加载SKILL.md，按任务读取一个模块，在授权目录执行并回读核验 |
| 不支持技能加载，但能读取项目文件 | 将SKILL.md作为对话指令提供，按其中相对路径或宿主资源入口读取所需模块，流程相同 |
| 仅支持聊天或附件 | 提供SKILL.md和本次需要的模块正文，以及相关项目规则、当前HANDOFF或文件清单；输出待保存文稿、归位方案或删除清单，由用户应用。缺材料时只请求本步骤必需内容 |

没有终端不影响采用宿主文件工具；没有文件写入或删除能力时，不能宣称已经保存、移动或清理。没有项目内容就不能完成项目事实核查。跨Agent可以保持相同的文件职责与交接结构，实际执行程度由工具与权限决定。

相对链接无法打开时，按文件名请求对应材料，不一次索取所有references或HISTORY。不要将全部技能正文写入项目AGENTS；它仍只保存项目规则。普通对话结束后不假定技能会自动保留，下次需重新选择技能或提供主入口。

接续工作时，新Agent读取适用项目规则与当前HANDOFF，再按下一步读取必要材料；遇到决定来源、状态矛盾或恢复问题才检索HISTORY。无需导入上一位Agent的完整聊天记录，不承诺自动跨平台同步文件。

neat-freak也可以通过可读技能资源或用户提供的主文档及按需参考资料执行，不以安装器或斜杠命令为前提；资料不可用时说明依赖核对未完成。具体读取条件见[交接模块](handoff.md)。

## 必须解析的变量

这些是工作流概念变量，不是要求用户配置的环境变量；不生成额外项目配置文件。

| 变量 | 来源与用途 |
|---|---|
| host / mode | 当前客户端、版本、本机/云端/附件模式，不由模型名称推断 |
| workspace_root / writable_scope | 用户指定目录与挂载/授权范围；无法访问父级则说明缺项 |
| rules_file / rule_entry | 既有权威规则正文与宿主入口，不假定AGENTS自动加载 |
| skill_roots / skill_resource_base | 宿主技能列表、配置或原生资源标识；相对引用基于技能位置而非工作目录 |
| init_action | 是否存在、可调用及真实产物；不能调用则走Codex本地化初始化 |
| file_tools / shell / search | 当前可用读写和检索方式；无终端不执行Shell，无rg不强装 |
| confirm_method | 允许用于许可请求的表单或明确对话回复；无回复不执行删除 |
| handoff_path / history_path | 项目既有约定优先；新项目默认根文件，不自动搬迁 |
| neat_freak_ref | 已安装技能实际路径或宿主可读取资源，不能套用别人的本机路径 |

只有缺少用户决策才询问；工具、路径、命令等可自行核对的事实先检查。未知自动加载机制按“本技能显式读取”处理，不捏造平台支持。

## 平台映射

| 宿主 | 规则与初始化 | 技能加载与调用 |
|---|---|---|
| Codex | 既有AGENTS优先；原生/init是否可调用须现场确认，否则用模块一内置的Codex本地化流程 | 用当前宿主技能列表与配置；本机唯一实体目录沿用用户约定，不推广为所有用户默认 |
| Claude Code | 原生CLAUDE.md，/init生成它；已有AGENTS作为正文时不复制规则，必要时在CLAUDE中建立短引用入口，并检查实际读取 | 官方项目技能 `.claude/skills/<name>/SKILL.md`；用户路径/其他来源按当前配置确认。可用 `/session-management`，以实际命令列表为准 |
| Claude网页/桌面/Cowork | 不套用Claude Code的/init或磁盘自动读取；使用当前允许的项目资料/工作目录和技能资源 | 沿宿主技能导入/选择入口；无本地读写则提供待应用文档，不声称修改源项目 |
| WorkBuddy（WB） | 已有项目规则显式读取；当前环境没有可调用/init时，使用模块一内置流程；自动加载文件名及固定安装路径仍未核实，不借用CodeBuddy IDE规则 | 官方支持技能页安装/上传本地技能包；以已安装列表和返回资源位置为准，不推定复制来源就是运行位置 |
| 豆包工作 | 已有项目规则显式读取；当前环境没有可调用/init时，使用模块一内置流程；固定规则文件自动加载及磁盘技能目录仍未核实 | 官方介绍支持自建、导入本地技能及手动选择；以当前宿主可访问资源为准，本地电脑与云端工作区分开 |
| Kimi Code CLI | 官方/init生成AGENTS；是否可调用现场确认 | 用户技能 `$KIMI_CODE_HOME/skills`（未设时 `~/.kimi-code/skills`）及 `~/.agents/skills`；项目层和启动参数优先级按配置核对。显式调用 `/skill:session-management`；`--skills-dir` 可替代自动发现路径 |

原生/init存在不等于Agent能代用户执行。未知平台或未知模式使用显式读取与当前可用文件工具/对话确认；仅用户要求初始化时调用Codex本地化初始化；不因此自动安装CLI或修改全局设置。

## 规则正文只维护一份

沿用已有规则正文，不因切换平台把AGENTS改名或再复制一份CLAUDE。使用宿主原生初始化时遵循其文件名；明确使用模块一的Codex本地化流程时固定生成AGENTS.md。其他无已确认原生规则名的平台采用AGENTS作为本技能的显式读取约定，并注明不代表平台自动加载。

多客户端确需不同入口时，仅在需要的入口写简短指针（Claude Code可用其受支持的导入方式），不生成整份同步副本，不建软链接。不支持导入时由本技能显式读取正文。入口也必须保留用户已有内容；原有两份规则冲突时先呈现冲突，不擅自选择覆盖。

## 交互与分发

三个模块只依赖Markdown与宿主可用文件能力。`agents/openai.yaml` 仅Codex界面元数据，其他平台不必解析，不能据此假定技能已加载。其他平台通过原生技能选择或自然语言调用，不统一使用 `$skill-name`。

公开分发保留session-management的SKILL.md及references资源，初始化流程已内置，无额外初始化技能或旧入口依赖。不得携带local-dependencies中的个人路径。只有正文而缺配套资源不能声称完整安装。

无Git时如实记录无Git恢复点；无写权限时提供待应用内容及限制，不把生成文本记为已落盘。neat-freak里的平台示例也按当前能力解释；不可执行的检查报告未执行，不能伪造通过。

## 官方核对来源与验证边界

- [Claude Code命令与CLAUDE.md](https://support.claude.com/en/articles/14553413-claude-code-cheatsheet)
- [WorkBuddy技能安装与导入](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Skills-Market)
- [WorkBuddy技能包结构](https://open.workbuddy.cn/docs/skill)
- [豆包工作功能与技能说明（飞书官网）](https://www.feishu.cn/content/article/7677519271848610746)
- [Kimi Code技能位置](https://www.kimi.com/code/docs/en/kimi-code-cli/customization/skills.html)
- [Kimi Code命令参考](https://www.kimi.ai/academy/kimi-code-cheat-sheet)

以上是文档核对与能力适配规则，不代表已在所有客户端实机验证。未确认的路径及自动规则读取保持未知，用通用路径完成可执行部分。
