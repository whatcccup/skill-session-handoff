# 会话交接

让下一次会话能接着做：核对本轮项目事实与文档，维护规则入口和交接，列明过程稿及恢复依据，由用户决定是否清理。

中文展示名为“会话交接”，调用标识为 `session-handoff`。适用于能读取、编辑项目文件并加载 `SKILL.md` 的 Agent；没有文件访问能力时只能提供待保存文本，不能声称已经安装或写入。

## 安装

需要同时安装两个技能：

- [会话交接](https://github.com/whatcccup/skill-session-handoff)：本仓库。
- [neat-freak（洁癖收尾）](https://github.com/KKKKhazix/khazix-skills/tree/main/neat-freak)：交接的前置依赖，负责核对文档、规则、事实和工作区残留。依赖由原作者独立维护，本仓库不复制其正文。

推荐把下面这段发给你的 Agent：

```text
请帮我安装会话交接技能：
https://github.com/whatcccup/skill-session-handoff
并安装它依赖的 neat-freak：
https://github.com/KKKKhazix/khazix-skills/tree/main/neat-freak

先读取会话交接仓库的 references/install-interview.md，按安装访谈确认工具、安装位置、已有版本和全局规则。展示具体改动供我确认后安装；不要覆盖已有定制，不要顺便执行交接或清理。完成后检查两个技能是否可读取，以及全局读取规则是否已配置。
```

手动安装时，将本仓库完整内容放入当前工具能发现的 `session-handoff/` 目录，将依赖仓库中的 `neat-freak/` 完整目录安装到相应技能位置。不要只复制 SKILL.md 而遗漏 references、scripts 等依赖文件。安装路径、重新加载方式及全局规则入口以所用工具为准，不假定所有平台相同。

## 全局规则需要增加什么

在工具支持的全局规则文件中合并下面的简短规则；已有相同内容则不重复添加：

```markdown
## 进入或恢复任务时

- 每次进入或恢复对话、项目或空间，先读取适用的项目规则与交接文件：AGENTS.md，以及 .handoff/HANDOFF.md（项目另有约定时以其约定为准）。
- 文件缺失或不可访问时如实说明；交接内容须与当前文件和实际状态核对。
```

只把读取入口放在全局规则里。执行顺序、清理确认和交接格式由技能维护，项目专属约定放在项目规则中。无法绑定本地目录的空间，按工具能力读取相关交接入口，不猜测路径。配置了规则仍需检查工具是否实际加载，安装访谈会协助验证。

## 使用

安装后，在需要结束或转交工作时说：

```text
使用会话交接技能，为本轮工作更新交接。
```

支持 `$skill` 调用的工具也可使用 `$session-handoff`。

执行顺序：先执行 neat-freak → 检查本轮涉及目录的 AGENTS.md，缺少则补齐 → 确认过程稿与恢复依据 → 更新交接。默认输出为 `.handoff/HANDOFF.md`，沿用项目已有路径；涉及目录按实际任务确定，不要求固定目录层数。

有过程稿候选时，优先弹窗或表单确认；工具不支持时在对话中询问。清单包含路径、建议、Git 保存状态和恢复方式。未回复不删除，“先保存”不等于允许擅自建库或推送。没有 Git 的项目可以使用，但必须如实说明没有 Git 恢复依据。

## 文件说明

- [SKILL.md](SKILL.md)：执行规则。
- [安装访谈](references/install-interview.md)：首次安装和配置引导。
- [agents/openai.yaml](agents/openai.yaml)：支持该配置的工具所用的中文展示信息。

## 许可

本仓库当前未声明开源许可证，公开可见不等于授予再分发许可。neat-freak 的许可见[原作者仓库](https://github.com/KKKKhazix/khazix-skills)。
