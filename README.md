# goal-skill

把模糊任务逐步整理成可验收的计划，再按你的授权交给 Codex Goal 执行。

`goal-skill` 是一个以中文交互为主的 Codex skill。你可以让它陪你慢慢想清楚，也可以让它查阅项目，补全有依据的实施计划。它复用 Codex 的原生 Goal 能力，普通使用不需要额外脚本或 MCP 服务。

## 解决了什么

“优化一下这个项目”“做一个更好用的后台”表达了方向，却往往没有说明做到什么程度、如何验收、哪些行为不能改变。直接开始执行容易出现范围扩大、反复返工或一直无法判断完成的问题。

这个 skill 帮你处理几个具体环节：

- **需求还很模糊**：围绕一个具体场景逐轮澄清，避免一次抛出整张需求问卷。
- **不知道如何做取舍**：提供具体例子、可比较的方案和推荐理由，由你决定关键方向。
- **项目事实不清楚**：查阅相关代码、规范和测试，减少让你回答项目里已经有答案的问题。
- **计划难以执行**：补齐结果、验收证据、修改范围、执行步骤和阻塞条件。
- **计划与 Goal 不一致**：复用已有 Goal 前核对验收和范围，避免按旧标准继续。
- **担心无效消耗**：复用有效证据，限制无关探索，把详细交接规则和评测材料按需加载。

它没有固定的 token 节省比例，也不保证所有任务都能在给定预算内完成。

## 适用场景

- 想做一个功能，但需求、范围或验收方式还不清楚。
- 希望先结合现有项目讨论可行方案，再实施。
- 想把一个需要多轮调查或迭代的任务交给 Goal 持续执行。
- 已有计划或 Goal，需要核对新的约束和完成标准。

一行修改、简单解释、范围已经明确的普通任务通常直接交给 Codex 即可。仅询问“Goal 是什么”不会进入本 skill 的需求引导流程。

## 安装

需要支持本地 skills 的 Codex。实际启动 Goal 还需要当前客户端和会话提供相应能力；没有 Goal 工具时，仍可完善计划并获得可复制的 `/goal` 文本。

仓库根目录就是 skill 目录，名称为 `goal-skill`。以下安装方式任选一种，避免在多个目录重复安装同名 skill。

### 方法一：让 Codex 安装

在 Codex 对话中输入：

```text
$skill-installer 请从 https://github.com/run-bigpig/goal-skill 安装 skill。
skill 位于仓库根目录，路径为 .，安装名称为 goal-skill。
```

安装器会使用当前环境配置的 skills 目录。安装后在下一轮输入 `$goal-skill`；如果技能列表没有刷新，重启 Codex。

### 方法二：Git 克隆到个人 skills 目录

当前官方文档推荐个人 skills 使用 `~/.agents/skills`。macOS、Linux 或 WSL：

```bash
mkdir -p "$HOME/.agents/skills"
git clone https://github.com/run-bigpig/goal-skill.git "$HOME/.agents/skills/goal-skill"
```

Windows PowerShell：

```powershell
$goalSkillsRoot = Join-Path $HOME '.agents/skills'
New-Item -ItemType Directory -Force -Path $goalSkillsRoot | Out-Null
git clone https://github.com/run-bigpig/goal-skill.git (Join-Path $goalSkillsRoot 'goal-skill')
if ($LASTEXITCODE -ne 0) { throw 'goal-skill clone failed' }
```

部分安装器或既有环境使用 `$CODEX_HOME/skills`，默认可能是 `~/.codex/skills`。已有安装能被识别时继续使用该位置即可，不必再克隆一份。目录已存在时，先判断它是 Git 克隆还是安装器下载的副本，再按下文更新。

### 方法三：仅在某个项目使用

将本仓库的完整内容放入项目的 `.agents/skills/goal-skill/`，确保结构是：

```text
your-project/
└── .agents/
    └── skills/
        └── goal-skill/
            ├── SKILL.md
            ├── agents/
            ├── references/
            └── evals/
```

可以从 GitHub 下载 ZIP 后复制完整 skill 内容。不要只复制 `SKILL.md`，它会按需引用其他文件；团队共享时，将该目录作为项目文件纳入版本管理。

## 快速开始

### 陪我慢慢想清楚

```text
$goal-skill 我想改善管理后台，但还不确定先改什么。
请每次只问一个关键问题，帮我逐步完善计划，
我确认后再启动 Goal。
```

### 结合项目帮我完善

```text
$goal-skill 结合当前项目，帮我规划如何改善请求失败时的排查体验。
先查看相关实现，提出有依据的方案，
给出验收条件和执行步骤，我确认后再启动 Goal。
```

### 完善计划后直接执行

```text
$goal-skill 结合当前项目完善这个任务：
修复 CSV 导出时含逗号的字段出现错列的问题，保持普通字段输出兼容。
关键取舍问我，普通实现细节按项目规范决定。
计划完善后直接启动 Goal，并执行到验收通过或明确受阻。
```

“完善后直接执行”会保留启动授权，避免计划形成后重复确认。仍会影响范围、兼容性或产品方向的关键选择，需要先澄清。

## 过程中怎么沟通

你可以随时补充、修改或收束范围：

```text
这个问题我不确定，给我几个具体例子。
结合项目判断，给我推荐方案。
先只做这一部分，其他留到以后。
只保留计划，暂不执行。
按这个计划启动 Goal。
```

每次回答后，它会更新相关共识，并解释这个决定怎样影响计划，不要求你从头回答一遍。

如需预算，可以明确说：

```text
启动 Goal 时，将 token 预算设为 20000。
```

`20000` 只是示例，不是默认值或完成保证。只说“尽量省 token”不会自动设置一个预算数字；实际生效情况以客户端或 Goal 工具返回为准。

## 工作流程

```text
模糊任务
  → 澄清场景与结果
  → 按需查阅项目证据
  → 形成可验收计划
  → 核对授权、执行模式和现有 Goal
  → 启动或继续 Goal
  → 根据证据验收，或报告阻塞与下一步
```

计划通常包含：

| 内容 | 回答的问题 |
| --- | --- |
| 目标 | 完成后有什么可观察变化？ |
| 验收 | 哪些测试、指标或产物能证明完成？ |
| 范围与约束 | 本次做到哪里，哪些行为需要兼容？ |
| 依据与假设 | 哪些事实已确认，哪些条件仍待验证？ |
| 执行步骤 | 下一步做什么，用什么检查？ |
| 迭代与阻塞 | 失败后如何选择下一步，何时需要外部输入？ |
| 交付 | 最终应得到哪些文件和验证说明？ |

细节数量随任务调整。已有项目规范会被引用，不会为填写模板重复整份 `AGENTS.md`。

## 执行边界

- 规划阶段以阅读和安全诊断为主，先完成关键取舍和计划。
- 仅要求计划时不会启动 Goal；已明确授权“完善后直接执行”时保留该授权。
- 处于 Plan 模式时先交付计划，按客户端要求切换到允许执行的模式；不会声称已经开始自动执行。
- 已有 Goal 时核对结果、验收和范围，语义一致就复用；需要调整但接口不支持时说明具体限制。
- 没有 Goal 能力时交付 `/goal` 文本，不创建自制后台循环，也不会把它当 shell 命令运行。
- 完成依赖实际验收证据。预算耗尽、环境受限或缺少输入会如实报告，不等同于完成。

具体规则见 [SKILL.md](SKILL.md) 和 [Goal 交接说明](references/goal-handoff.md)。

## Token 效率的设计

它通过减少无效工作来控制消耗：场景未明确时先澄清；找到足以支持计划的项目证据后停止本轮探索；复用仍有效的测试和已排除假设；详细的 Goal 交接规则只在需要操作 Goal 时读取。

评测案例只供维护使用，普通引导不会加载。真实效率应比较同等验收质量下的总消耗，包括失败尝试和后续返工。

## 更新

通过 Git 克隆安装的副本，可以在确认没有需要保留的未提交修改后更新：

```bash
git -C "$HOME/.agents/skills/goal-skill" status --short
git -C "$HOME/.agents/skills/goal-skill" pull --ff-only
```

如果实际安装位置不同，请替换路径。`--ff-only` 不会自动合并分叉历史；有本地修改或无法快进时，先处理差异。

通过 skill-installer 安装的副本可能没有 `.git`，不能直接 `git pull`。可让 Codex 先比较当前安装与 GitHub 版本、保留本地定制后更新；内置安装器通常会拒绝覆盖已有目录。

## 文件结构

```text
goal-skill/
├── SKILL.md                    # 工作流入口与引导规则
├── README.md                   # 安装、使用与维护说明
├── LICENSE                     # MIT 许可证
├── agents/openai.yaml          # 技能显示名称与默认提示
├── references/goal-handoff.md   # 按需加载的 Goal 交接规则
└── evals/cases.yaml             # 11 个行为评测案例规格
```

## 验证状态与贡献

当前已完成 skill 结构、UI 元数据、引用链接、YAML 案例格式和文件空白检查。`evals/cases.yaml` 是评测规格，不是自动运行器，也不代表 11 个案例已经通过模型实测。尚未据此给出行为通过率或 token 节省数据。

案例覆盖自动触发、逐轮澄清、仅规划、预先授权、需求修改、已有 Goal 一致性、Plan 模式及工具缺失。维护时应使用隔离会话和记录型 Goal 工具替身，按对话与工具轨迹判断结果，不能只看模型的完成声明。

欢迎通过 [Issues](https://github.com/run-bigpig/goal-skill/issues) 或 Pull Request 提供改进。反馈时建议包含 Codex 版本、模型、协作模式、最小复现对话，以及预期和实际行为；请去除密钥和私人项目数据。修改交互规则时，补充对应的行为案例，避免为单个例子堆叠通用限制。

## 常见问题

**安装后找不到 `$goal-skill`？**

确认实际目录下存在 `goal-skill/SKILL.md`，没有多套一层同名文件夹；确认当前环境会扫描该目录。如果下一轮仍不可见，重启 Codex。也检查是否在多个位置安装了同名 skill。

**它和直接使用 `/goal` 有什么区别？**

`/goal` 用来设定持续目标；这个 skill 主要帮助你在启动前把目标想清楚，并把验收和范围衔接到真实 Goal。已有清晰目标时可以直接使用 `/goal`。

**必须使用 Goal 才能规划吗？**

不必。明确说“只给计划，暂不执行”即可。没有原生 Goal 工具的环境也可以完成规划。

**它是插件或独立后台服务吗？**

不是。本仓库发布的是独立 skill 文件，可安装到支持本地 skills 的 Codex 环境；不包含插件清单、执行守护进程或额外模型服务。

## 参考资料

- [OpenAI：Build skills](https://developers.openai.com/codex/skills)
- [OpenAI：Using Goals in Codex](https://developers.openai.com/cookbook/examples/codex/using_goals_in_codex)
- [OpenAI：Manage a thread goal](https://learn.chatgpt.com/docs/app-server#manage-a-thread-goal)

本项目由社区维护，与 OpenAI 无隶属关系。

## 许可证

[MIT](LICENSE) © 2026 run-bigpig。允许使用、修改和分发，须保留版权及许可声明；完整条款以 `LICENSE` 为准。
