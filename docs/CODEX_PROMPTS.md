# Codex Prompts for Fallout 4 Save Archive

这些提示词用于 VS Code 中的 Codex。开始工作前，先完整阅读：

- `README.md`
- `AGENTS.md`
- `docs/AUTOMATION_POLICY.md`
- `incoming-saves/README.md`
- `.gitattributes`
- `.gitignore`

## 当前追踪范围

默认记录：

- 地点、同伴、游戏内日期、游玩时长；
- 主线和阵营任务名称及概括性进度；
- 四大阵营敌对状态、路线可用状态和不可逆分支状态；
- 相比上个检查点的重要变化；
- 用户明确提供的同伴、NPC、DLC、聚落和关键选择；
- `.fos` / `.f4se` 存档对完整性；
- 用户明确说明的控制台命令和恢复风险。

默认不记录，也不要列入 `unknown`：

- 精确任务目标原文；
- quest stage、Form ID 或内部阶段编号；
- Fallout 4 runtime 版本；
- F4SE 精确版本；
- MOD、插件及加载顺序列表；
- 恢复测试结果。

使用 F4SE 时仍须归档同名 `.f4se`，但只记录“已使用 F4SE”，不追踪版本。

## 文件名元数据解析

当文件名明确包含：

```text
_<LocationToken>_<DDHHMM>_<real-world timestamp>_
```

允许读取：

- `LocationToken`：地点标记；
- `DDHHMM`：累计游玩时长，两位天数、两位小时、两位分钟。

例如：

```text
_InstituteConcourse_031516_20260718163529_
```

可记录为：

```text
location: Institute
playTime: 3d 15h 16m
```

只在模式明确匹配时解析。用户明确提供的信息优先。不得从文件名推断游戏内日历日期。

## 默认执行模式

普通检查点默认使用**一次性直接提交模式**：验证通过后直接创建文档、commit 并 push，不再等待第二次确认。

只有出现 `docs/AUTOMATION_POLICY.md` 所列阻断条件时才停止，不得使用危险 Git 操作绕过。

## 1. 添加公共时间线检查点并直接提交

```text
请完整阅读 README.md、AGENTS.md 和 docs/AUTOMATION_POLICY.md，并严格执行。

我要把新的 Fallout 4 检查点添加到 main。当前未锁定最终阵营路线。

输入：
- 存档公共基础名：<不含扩展名的完整名称>
- 检查点 ID：<YYYY-MM-DD-short-description>
- 标题：<标题>
- 当前同伴：<同伴或 unknown>
- 游戏内日期：<日期或 unknown>
- 已确认剧情与阵营状态：<列表>
- 已确认 DLC/Creation 状态：<列表或不更新>
- 其他本节点变化：<列表>

文件：
- incoming-saves/<基础名>.fos
- incoming-saves/<基础名>.f4se

执行：
1. git status --short --branch
2. git branch --show-current
3. git pull --ff-only
4. git lfs install
5. git lfs env
6. 确认当前分支为 main，且没有无关未提交修改。
7. 检查两个文件存在、非空、基础名完全一致；已知使用 F4SE 但缺少 co-save 时停止。
8. 按 docs/AUTOMATION_POLICY.md 从文件名解析可确认的地点和游玩时长；不得解析游戏内日历日期。
9. 新建 checkpoints/<ID>/save/，原样复制两个文件，不移动或删除中转原文件。
10. 分别计算文件大小和小写 SHA-256。
11. 通过 git check-attr 和 git lfs status 验证两个文件由 Git LFS 管理。
12. 创建 schemaVersion 2 的 manifest.json，不包含 restoreTested。
13. 创建 progress.md，只记录仓库追踪范围内的信息。
14. 不生成精确任务目标文字、任务阶段编号、runtime/F4SE 版本、MOD/插件/load order 或恢复测试字段。
15. 对用户表示“不补充”的其他任务、聚落或 NPC，只写一条概括说明，不要逐项列为 unknown。
16. 执行公开仓库隐私检查；绝对路径和路径中的 Windows 用户名属于已接受风险。
17. 只暂存 checkpoints/<ID>/。
18. 检查 git diff --cached --stat、manifest、progress 和 git lfs status。
19. commit message：checkpoint: <简短描述>
20. git push origin HEAD
21. 不 force push，不创建 story 分支，不创建 tag。
22. 成功后报告 branch、checkpoint、两个文件的大小与 SHA-256、配对状态、Git LFS 状态、地点与游玩时长来源、commit hash 和 push 结果。

除非出现明确阻断条件，否则不要在 commit 前再次询问确认。
```

## 2. 创建阵营故事线分支

```text
请读取 README.md、AGENTS.md 和 docs/AUTOMATION_POLICY.md。

我确认已经发生不可逆阵营分叉：
- 锁定阵营：<brotherhood / railroad / institute / minutemen>
- 触发节点：<概括性任务和选择>
- main 的最后安全起点：<commit、tag 或 checkpoint>

验证工作区干净且起点位于 main，然后创建并推送 story/<faction>。
不要修改 main，不要创建其他分支，不要合并分支，不要 force push。
```

## 3. 更新现有故事线检查点并直接提交

```text
请读取 README.md、AGENTS.md 和 docs/AUTOMATION_POLICY.md。

目标分支：story/<faction>
存档公共基础名：<完整基础名>
检查点 ID：<ID>
概括性剧情变化：<列表>
阵营敌对/锁线变化：<列表>
同伴、NPC、DLC 或聚落变化：<列表>

文件：
- incoming-saves/<基础名>.fos
- incoming-saves/<基础名>.f4se

确认分支一致，验证存档对，从文件名读取可确认的地点和游玩时长，创建独立 checkpoint，分别计算 SHA-256，生成 schemaVersion 2 manifest 和 progress.md。
验证通过后只暂存该 checkpoint，直接 commit 并 push。
不要修改 main，不记录默认排除字段，不需要二次确认。
```

## 4. 修正文档但不触碰存档

```text
请读取 AGENTS.md。

检查点：<目录>
需要修正的信息：<列表>

只修改 progress.md；仅在 manifest 的现有字段确实错误时修改 manifest.json。
不得修改、移动、删除或重新提交任何 .fos/.f4se。
显示差异后直接以 fix: correct checkpoint metadata for <ID> 提交并 push；发现歧义或可能改变存档事实时停止询问。
```

## 5. 仓库完整性审计

```text
请读取 AGENTS.md 和 docs/AUTOMATION_POLICY.md，对仓库执行只读审计：

1. checkpoint 是否包含 save、progress.md、manifest.json；
2. .fos 是否存在；使用 F4SE 时同名 .f4se 是否存在；
3. 两个文件的名称、大小和 SHA-256 是否与 manifest 一致；
4. f4seCoSave.status 是否有效；
5. 所有存档文件是否由 Git LFS 管理；
6. 是否存在重复 checkpointId、错配、覆盖、重命名或缺失；
7. main 和 story 分支模型是否正确；
8. incoming-saves 中的实际存档是否被误跟踪；
9. 是否存在凭据、Token、Cookie、私钥或私人通信；
10. 文件名中地点和游玩时长的解析是否符合规则。

绝对路径和路径中的 Windows 用户名不是问题。
不要把未追踪的 runtime、F4SE 版本、MOD、插件、load order、精确任务目标、stage ID 或恢复测试视为缺失。
不要修改文件。
```

## 6. 恢复检查点

```text
请读取 AGENTS.md。

目标 checkpoint：<ID>

只做安全准备：
1. 找到 .fos、.f4se 和 manifest；
2. 分别校验 SHA-256；
3. 验证基础文件名一致；
4. 备份目标目录中的同名文件；
5. 给出同时复制两个文件的 PowerShell 命令；
6. 列出 progress.md 已记录的 DLC/Creation 和剧情风险。

不要要求 runtime/F4SE 版本、MOD 列表、插件列表、load order 或恢复测试记录。
```

## 7. 同步现有 Codex 会话到最新规则

```text
仓库规则已经更新。请执行：

git status --short --branch
git pull --ff-only

然后重新完整阅读：
- README.md
- AGENTS.md
- docs/AUTOMATION_POLICY.md
- docs/CODEX_PROMPTS.md
- incoming-saves/README.md
- .gitattributes
- .gitignore

不要修改文件。

请确认：
1. 文件名中明确匹配的地点标记和 DDHHMM 游玩时长可以作为元数据来源。
2. DDHHMM 按天、小时、分钟解析；不得由文件名推断游戏内日历日期。
3. 普通 checkpoint 验证通过后直接 commit 和 push，不再等待二次确认。
4. 只有 AUTOMATION_POLICY 列出的阻断条件出现时才停止。
5. 精确任务目标、stage、runtime/F4SE 版本、MOD/插件/load order 和恢复测试不归档，也不列为 unknown。
6. 使用 F4SE 时，同名 .fos/.f4se 必须一起归档并通过 Git LFS 管理。

报告当前 branch、工作区状态和是否已读取最新规则。不要 commit，不要 push。
```

## Recommended initial prompt

```text
请先阅读 README.md、AGENTS.md 和 docs/AUTOMATION_POLICY.md，不要修改文件。
总结 branch model、checkpoint 结构、F4SE 存档对规则、文件名元数据解析、直接提交模式、实际追踪范围和禁止操作。
```
