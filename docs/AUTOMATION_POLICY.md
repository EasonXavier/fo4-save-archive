# Checkpoint Automation Policy

本文件定义 VS Code / Codex 维护本仓库时的默认自动化行为。与一般提示词冲突时，以根目录 `AGENTS.md` 的安全规则及本文件的明确授权为准。

## 1. 从存档文件名读取元数据

Fallout 4 存档文件名可包含以下片段：

```text
_<LocationToken>_<DDHHMM>_<real-world timestamp>_
```

例如：

```text
_InstituteConcourse_031516_20260718163529_
```

允许按以下规则读取：

- `LocationToken` 是保存时的地点标记；
- 六位数字 `DDHHMM` 表示累计游玩时长：两位天数、两位小时、两位分钟；
- `031516` 应记录为 `3d 15h 16m`；
- `InstituteConcourse` 可规范化记录为 `Institute`，同时保留原始文件名作为证据；
- 用户明确提供的地点或时长优先于文件名解析；
- 只有模式明确匹配时才解析，模式不匹配则使用 `unknown`，不得猜测；
- 文件名不提供游戏内日历日期，不得从该字段推断 2287/2288 年的日期；
- 真实世界时间戳仅属于存档文件名的一部分，不应替代 `createdAt` 的实际归档时间。

文件名本身属于用户提供的存档证据，可作为 `game.location` 和 `game.playTime` 的来源。

## 2. 默认直接提交模式

仓库规则已稳定。创建普通检查点时，Codex 默认不再进行“准备后等待第二次确认”的两阶段流程。

在输入信息完整且验证通过后，应在同一次任务中：

1. 检查分支、远程同步和工作区状态；
2. 验证 `.fos` / `.f4se` 配对、大小、SHA-256 和 Git LFS；
3. 创建或更新 `manifest.json` 与 `progress.md`；
4. 执行隐私检查；
5. 只暂存本检查点目录；
6. commit；
7. `git push origin HEAD`；
8. 报告最终结果。

不需要在 commit 前再次询问用户。

## 3. 必须停止的阻断条件

遇到以下情况时，不得自动 commit 或 push，必须停止并报告：

- 当前分支与剧情状态不匹配；
- `git pull --ff-only` 失败或需要合并；
- 工作区存在与本任务无关的未提交修改；
- 检查点目录已经存在并可能被覆盖；
- `.fos` 缺失、为空或无法读取；
- 已知使用 F4SE 但同名 `.f4se` 缺失；
- `.fos` 与 `.f4se` 基础文件名不一致；
- 文件大小或 SHA-256 无法计算；
- 任一存档文件未由 Git LFS 管理；
- 发现凭据、Token、Cookie、API key、私钥、私人通信或其他禁止公开的信息；
- push 被拒绝或远程状态异常；
- 需要创建阵营分支，但用户尚未明确确认不可逆分叉及其起点。

不得使用 `force push`、`reset --hard`、`git clean`、历史改写或删除用户文件来绕过阻断条件。

## 4. 默认提交报告

成功后报告：

- branch；
- checkpoint ID 和目录；
- `.fos` / `.f4se` 文件名、大小、SHA-256；
- 配对结果；
- Git LFS 状态；
- 记录的地点和游玩时长，以及是否由文件名解析；
- commit hash；
- push 结果；
- 追踪范围内仍为 `unknown` 的字段。
