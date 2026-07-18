# Codex Prompts for Fallout 4 Save Archive

这些提示词用于 VS Code 中的 Codex。开始工作前，先读取根目录 `README.md` 和 `AGENTS.md`。

## 当前追踪范围

默认记录：

- 地点、同伴、游戏内日期、游玩时长；
- 主线和阵营任务名称及概括性进度；
- 四大阵营敌对状态、路线可用状态和不可逆分支状态；
- 相比上个检查点的重要变化；
- 用户明确提供的同伴、NPC、DLC、聚落和关键选择；
- `.fos`/`.f4se` 存档对完整性；
- 用户明确说明的控制台命令和恢复风险。

默认不记录，也不要列入 `unknown`：

- 精确任务目标原文；
- quest stage、Form ID 或内部阶段编号；
- Fallout 4 runtime 版本；
- F4SE 精确版本；
- MOD、插件及加载顺序列表；
- 恢复测试结果。

使用 F4SE 时仍须归档同名 `.f4se`，但只记录“已使用 F4SE”，不追踪版本。

## 1. 添加公共时间线检查点

```text
请完整阅读 README.md 和 AGENTS.md，并严格执行。

我要把新的 Fallout 4 检查点添加到 main。当前未锁定最终阵营路线。

输入：
- 主存档：incoming-saves/<准确文件名>.fos
- F4SE co-save：incoming-saves/<同基础文件名>.f4se
- 检查点 ID：<YYYY-MM-DD-short-description>
- 标题：<标题>
- 当前地点：<地点或 unknown>
- 当前同伴：<同伴或 unknown>
- 游戏内日期：<日期或 unknown>
- 总游玩时长：<时长或 unknown>
- 已确认剧情与阵营状态：<列表>
- 已确认 DLC/Creation 安装状态：<列表或不记录>
- 其他本节点变化：<列表>

执行：
1. git status --short --branch
2. git branch --show-current
3. git pull --ff-only
4. git lfs install
5. git lfs env
6. 确认当前分支为 main。
7. 检查 .fos 与 .f4se 基础文件名完全一致；已知使用 F4SE 但缺少 co-save 时停止。
8. 新建 checkpoints/<ID>/save/，原样复制两个文件，不修改或删除中转原文件。
9. 分别计算大小和小写 SHA-256。
10. 通过 git check-attr 和 git lfs status 验证两个文件由 Git LFS 管理。
11. 创建 schemaVersion 2 的 manifest.json，不包含 restoreTested。
12. 创建 progress.md，只记录仓库追踪范围内的信息。
13. 不要求或生成精确任务目标文字、任务阶段编号、runtime/F4SE 版本、MOD/插件/load order 或恢复测试字段。
14. 对用户表示“不补充”的其他任务、聚落或 NPC，只写一条概括说明，不要逐项列为 unknown。
15. 展示文件摘要、哈希、Git LFS 状态、git diff 和真正需要确认的字段。
16. 不 commit，不 push，等待确认。
```

确认后：

```text
继续提交刚才准备的检查点。

- 只暂存本检查点目录。
- 不提交 incoming-saves 中的原文件。
- commit message：checkpoint: <简短描述>
- git push origin HEAD
- 不 force push，不创建 story 分支。
- 报告 branch、checkpoint、两个文件的大小与 SHA-256、配对状态、Git LFS 状态、commit hash 和 push 结果。
```

## 2. 创建阵营故事线分支

```text
请读取 README.md 和 AGENTS.md。

我确认已经发生不可逆阵营分叉：
- 锁定阵营：<brotherhood / railroad / institute / minutemen>
- 触发节点：<概括性任务和选择>
- main 的最后安全起点：<commit、tag 或 checkpoint>

验证工作区干净且起点位于 main，然后创建并推送 story/<faction>。
不要修改 main，不要创建其他分支，不要合并分支。
```

## 3. 更新现有故事线检查点

```text
请读取 README.md 和 AGENTS.md。

目标分支：story/<faction>
主存档：incoming-saves/<文件名>.fos
F4SE co-save：incoming-saves/<同名文件>.f4se
检查点 ID：<ID>
概括性剧情变化：<列表>
阵营敌对/锁线变化：<列表>
同伴、NPC、DLC 或聚落变化：<列表>

确认分支一致，验证存档对，创建独立 checkpoint，分别计算 SHA-256，生成 schemaVersion 2 manifest 和 progress.md。
不要修改 main，不记录默认排除字段。先展示变更，等待确认。
```

## 4. 修正文档但不触碰存档

```text
请读取 AGENTS.md。

检查点：<目录>
需要修正的信息：<列表>

只修改 progress.md；仅在 manifest 的现有字段确实错误时修改 manifest.json。
不得修改、移动、删除或重新提交任何 .fos/.f4se。
显示差异并等待确认。
```

## 5. 仓库完整性审计

```text
请读取 AGENTS.md，对仓库执行只读审计：

1. checkpoint 是否包含 save、progress.md、manifest.json；
2. .fos 是否存在；使用 F4SE 时同名 .f4se 是否存在；
3. 两个文件的名称、大小和 SHA-256 是否与 manifest 一致；
4. f4seCoSave.status 是否有效；
5. 所有存档文件是否由 Git LFS 管理；
6. 是否存在重复 checkpointId、错配、覆盖、重命名或缺失；
7. main 和 story 分支模型是否正确；
8. incoming-saves 中的实际存档是否被误跟踪；
9. 是否存在凭据、Token、Cookie、私钥或私人通信。

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
- docs/CODEX_PROMPTS.md
- incoming-saves/README.md
- .gitattributes
- .gitignore

不要修改文件。

请确认：
1. 精确任务目标原文和任务阶段编号不再归档，也不要列为 unknown。
2. Fallout 4 runtime、F4SE 精确版本、MOD、插件和加载顺序不再默认归档，也不要列为 unknown。
3. 不再使用 restoreTested，也不要求恢复测试。
4. 仍需记录地点、同伴、游戏内日期、游玩时长、概括性剧情进度、阵营状态、DLC/Creation 状态和存档对完整性。
5. 用户明确不补充的其他任务、聚落或 NPC 状态，只保留一条概括说明，不逐项展开 unknown。
6. 使用 F4SE 时，同名 .fos/.f4se 必须一起归档并通过 Git LFS 管理。

报告当前 branch、工作区状态和是否已读取最新规则。不要 commit，不要 push。
```

## Recommended initial prompt

```text
请先阅读 README.md 和 AGENTS.md，不要修改文件。
总结 branch model、checkpoint 结构、F4SE 存档对规则、实际追踪范围和禁止操作。
```
