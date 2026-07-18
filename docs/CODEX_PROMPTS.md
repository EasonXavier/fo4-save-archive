# Codex Prompts for Fallout 4 Save Archive

这些提示词用于 VS Code 中的 Codex。开始任务前，先确保仓库已经打开，并让 Codex 读取根目录的 `AGENTS.md`。

使用 F4SE 时，`.fos` 与同名 `.f4se` 是一个逻辑存档对。除扩展名外，两个文件的基础文件名必须完全一致。

## 1. 添加一个新的公共时间线检查点

```text
请读取根目录 README.md 和 AGENTS.md，并严格按其中规则工作。

我要把一个新的 Fallout 4 存档检查点添加到 main。当前尚未锁定任何最终阵营路线。

输入材料：
- 主存档文件：incoming-saves/<填写准确文件名>.fos
- F4SE co-save：incoming-saves/<填写相同基础文件名>.f4se
- 任务截图：<填写截图路径，可有多个>
- 检查点标题：<填写标题>
- 当前同伴：<填写或 unknown>
- 当前地点：<填写或 unknown>
- 我明确确认的任务状态：
  - <逐条填写>

请执行：
1. 确认当前分支是 main，并执行 git pull --ff-only。
2. 执行 git lfs install 和 git lfs env。
3. 检查 .fos 与 .f4se 的基础文件名是否完全一致。
4. 如果已知使用 F4SE 但找不到对应 .f4se，停止并报告，不要使用其他文件代替。
5. 按 YYYY-MM-DD-short-description 创建新的 checkpoints 目录。
6. 原样复制 .fos 和同名 .f4se，不修改文件名和内容，不删除 incoming-saves 中的原文件。
7. 分别计算两个文件的大小和小写 SHA-256。
8. 验证两个文件均由 Git LFS 管理。
9. 根据我提供的截图和说明生成 schemaVersion 2 的 manifest.json 与 progress.md。
10. 无法确认的信息标记为 unknown，不要根据游戏常识猜测。
11. 检查公开仓库隐私风险；绝对路径以及路径中暴露的 Windows 用户名属于已接受风险，不需要脱敏。
12. 展示 git diff、Git LFS 状态和待提交文件摘要。
13. 在提交前先停下，等待我确认。
```

确认内容无误后继续：

```text
继续提交刚才的检查点。

要求：
- commit message 使用：checkpoint: <简短描述>
- push 到当前 main 分支
- 完成后分别报告 .fos 和 .f4se 的文件名、大小、SHA-256
- 报告两个基础文件名是否匹配，以及 Git LFS 状态
- 报告 branch、commit hash 和 push 结果
- 不要创建新的故事线分支
- 不要删除 incoming-saves 中的原文件
```

## 2. 在阵营锁定时创建故事线分支

```text
请读取 AGENTS.md。

当前存档已经发生不可逆阵营分叉。我确认：
- 锁定阵营：<brotherhood / railroad / institute / minutemen>
- 触发节点：<任务名称和具体选择>
- 从 main 的哪个安全检查点分叉：<commit、tag 或检查点目录>

请先验证该节点确实位于 main，并检查工作区是否干净。
然后从指定安全节点创建：

story/<faction>

不要修改 main，不要提前创建其他阵营分支，不要合并分支。
完成后报告新分支的起点 commit 和远程 push 结果。
```

## 3. 向现有故事线分支添加检查点

```text
请读取 README.md 和 AGENTS.md。

我要向 <story/faction> 分支添加新的 Fallout 4 检查点。

输入材料：
- 主存档：incoming-saves/<准确文件名>.fos
- F4SE co-save：incoming-saves/<同名文件名>.f4se
- 截图：<路径>
- 检查点标题：<标题>
- 与上一个检查点相比的变化：
  - <任务完成或失败>
  - <阵营敌对变化>
  - <同伴或 NPC 变化>
  - <关键选择>

请：
1. 确认当前分支与指定故事线一致。
2. 不修改或合并 main。
3. 验证 .fos 和 .f4se 的基础文件名完全一致。
4. 新建独立检查点目录，并原样复制两个文件。
5. 分别计算文件大小和 SHA-256，验证 Git LFS 状态。
6. 生成 schemaVersion 2 的 manifest.json，并在 progress.md 记录存档对完整性。
7. 重点记录该分支已经关闭的其他路线和不可逆状态。
8. 不删除 incoming-saves 中的原文件。
9. 先展示变更，等待我确认后再 commit 和 push。
```

## 4. 只修正检查点说明，不修改存档

```text
请读取 AGENTS.md。

我要修正以下检查点的文字信息：
- 检查点：<目录>
- 正确信息：<逐条说明>

要求：
- 不修改、移动、删除或重新提交任何 .fos 或 .f4se 文件。
- 只更新 progress.md；只有 metadata 字段确实错误时才更新 manifest.json。
- 保留原始检查点目录。
- 显示修改前后的差异。
- commit message 使用：fix: correct checkpoint metadata for <ID>
- 提交前等待我确认。
```

## 5. 验证仓库完整性

```text
请读取 AGENTS.md，并对整个仓库执行只读审计。

检查：
1. 所有 checkpoints 目录是否都有 save、progress.md 和 manifest.json。
2. 每个检查点是否包含 .fos。
3. 使用 F4SE 的检查点是否包含基础文件名完全一致的 .f4se。
4. manifest 中两个文件各自的 fileName、sizeBytes 和 sha256 是否与实际文件一致。
5. f4seCoSave.status 是否只使用 present、missing、not-applicable 或 unknown。
6. 所有 .fos 和 .f4se 是否由 Git LFS 管理。
7. 是否存在重复 checkpointId。
8. main 是否只包含阵营锁定前的公共时间线。
9. story 分支是否从合理的 main 安全节点分叉。
10. 是否存在故事线分支被合并回 main 的情况。
11. 是否存在邮箱、令牌、凭据、私人通信或其他未授权公开的隐私信息。绝对路径及其中的 Windows 用户名不视为问题。
12. 是否有存档文件被覆盖、重命名、错误配对或缺失。
13. incoming-saves 中的实际存档文件是否被意外跟踪或提交。

不要修改任何文件。输出问题清单、严重程度和修复建议。
```

## 6. 恢复某个检查点

```text
请读取 AGENTS.md。

我要恢复检查点：<checkpoint ID>

请只进行安全准备，不启动游戏：
1. 找到对应 .fos、.f4se 和 manifest.json。
2. 分别校验两个文件的 SHA-256。
3. 验证两个基础文件名完全一致。
4. 如果 f4seCoSave.status 不是 present，明确说明恢复风险并停止自动复制。
5. 列出该检查点需要的 runtime、F4SE、DLC、Creation 和 MOD 环境。
6. 指出恢复后的当前任务、当前同伴、阵营状态和下一不可逆节点。
7. 给出备份当前 Saves 目录和同时复制两个目标文件的 PowerShell 命令。
8. 不删除或覆盖任何现有文件；如有同名文件，使用时间戳备份名称。
```

## 7. 从截图更新当前任务进度

```text
请读取 AGENTS.md。

以下图片是同一 Fallout 4 存档的任务日志截图：
<填写截图路径>

请只根据图片中可见文字和我补充的信息，整理：
- active quests
- visible objectives
- completed objectives
- faction state implications
- unknown fields

不要因为某个任务没有出现在单张截图中就判定它已完成或失败。
先输出分析，不要修改仓库。待我确认后，再更新指定检查点的 progress.md。
```

## 8. 一次性完成检查点并推送

仅在输入信息完整、无需人工复核时使用：

```text
请读取 README.md 和 AGENTS.md，并为以下 Fallout 4 存档建立检查点后直接提交和 push。

- 目标分支：<main 或 story/...>
- 主存档路径：incoming-saves/<文件名>.fos
- F4SE co-save 路径：incoming-saves/<同名文件名>.f4se
- 截图路径：<截图>
- 检查点 ID：<YYYY-MM-DD-short-description>
- 标题：<标题>
- 当前地点：<地点或 unknown>
- 当前同伴：<同伴或 unknown>
- 已确认任务状态：<详细列表>
- 已确认阵营状态：<详细列表>

要求：
- 严格遵守 AGENTS.md。
- 验证 .fos 和 .f4se 的基础文件名完全一致。
- 不猜测未知信息。
- 不覆盖任何已有检查点。
- 分别校验两个文件的大小和 SHA-256。
- 验证两个文件的 Git LFS 状态。
- 使用 schemaVersion 2 的 manifest.json。
- 不删除 incoming-saves 中的原文件。
- commit message：checkpoint: <description>
- push 到当前目标分支。
- 最终报告全部文件、两个 SHA-256、配对状态、Git LFS 状态、commit hash 和 push 结果。
```

## 9. 同步现有 Codex 会话到最新规则

仓库规则发生变化后，向现有 Codex 会话发送：

```text
仓库规则已经更新。请先执行 git status --short --branch 和 git pull --ff-only，然后重新完整阅读：
- README.md
- AGENTS.md
- docs/CODEX_PROMPTS.md
- incoming-saves/README.md
- .gitattributes
- .gitignore

不要修改任何文件。

请确认并总结以下新规则：
1. 使用 F4SE 时，同名 .fos 与 .f4se 是一个逻辑存档对。
2. .fos 始终必须存在；对应 .f4se 存在时必须一起归档、校验、提交和恢复。
3. 两个文件除扩展名外的基础文件名必须完全一致，不得错配、替换或创建空 co-save。
4. 已知使用 F4SE 但对应 .f4se 缺失时，提交前必须停止并等待我的明确许可；获得许可后 manifest 才能记录 missing。
5. manifest.json 使用 schemaVersion 2，并分别记录两个文件的状态、文件名、大小和小写 SHA-256。
6. .fos 和 .f4se 都必须由 Git LFS 管理，提交前检查 git check-attr 和 git lfs status。
7. incoming-saves 中的实际存档文件不直接提交；只能复制到新的 checkpoint 目录，并保留中转原文件。
8. 绝对路径和路径中的 Windows 用户名属于已接受的公开风险，但凭据、Token、Cookie、私钥、私人通信等仍禁止提交。

最后报告当前 branch、工作区状态、是否已拉取最新规则，以及以后创建检查点时你需要我提供的两项存档文件信息。不要 commit，不要 push。
```

## Recommended initial prompt

首次在 VS Code 中打开本仓库时，可直接使用：

```text
请先阅读 README.md 和 AGENTS.md，不要修改文件。
总结本仓库的 branch model、checkpoint 结构、F4SE 存档对规则、不可执行的危险操作，以及以后添加 Fallout 4 存档时你需要向我确认的信息。
```
