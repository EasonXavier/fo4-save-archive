# Fallout 4 Save Archive

A public, version-controlled archive of my **Fallout 4** save files, quest progress, faction states, companion states, installed DLC, and branching storylines.

本仓库用于长期保存《辐射 4》的关键存档及其剧情上下文，使我能够在未来回到某个明确节点，而不只是保留无法辨认的存档文件。

## Branch model

### `main`

`main` 只保存**尚未锁定最终阵营路线**的公共时间线。

只要 Minutemen、Railroad、Brotherhood of Steel 和 Institute 的主要路线仍可并行推进，检查点就继续提交到 `main`。

只有发生以下情况之一时，才从最后一个安全检查点创建故事线分支：

- 某阵营永久敌对；
- 其他阵营主线被关闭；
- 游戏出现明确的不可逆警告；
- 已确定以某个阵营完成主线结局。

### Story branches

```text
story/brotherhood
story/railroad
story/institute
story/minutemen
```

DLC 只有在需要保留互斥选择时才建立独立分支，例如：

```text
story/far-harbor
story/nuka-world
```

故事线分支不会合并回 `main`。`main` 始终停留在阵营分叉前的公共时间线。

## Save-pair policy

使用 F4SE 时，一个逻辑存档由两个同名文件组成：

```text
<same-basename>.fos
<same-basename>.f4se
```

- `.fos` 是 Fallout 4 主存档，始终必须存在；
- `.f4se` 是 F4SE co-save；
- 两个文件除扩展名外，基础文件名必须完全一致；
- 对应 `.f4se` 存在时，必须与 `.fos` 一起归档、校验、提交和恢复；
- 不得错配、替换、伪造或创建空 `.f4se`；
- 已知使用 F4SE 但对应 `.f4se` 缺失时，提交前必须明确报告并等待决定；只有获得明确许可后，才能以 `missing` 状态归档。

`.fos` 和 `.f4se` 均通过 Git LFS 管理。

## Repository layout

```text
checkpoints/
  YYYY-MM-DD-checkpoint-name/
    save/
      original-save-file.fos
      original-save-file.f4se
    progress.md
    manifest.json
    screenshots/                 # optional
incoming-saves/
  README.md
```

每个检查点至少应包含：

- 原始 `.fos` 主存档；
- 同名 `.f4se` co-save：对应文件存在时必须包含；
- `progress.md`；
- `manifest.json`；
- 必要时附任务日志、DLC 安装情况或其他证据截图。

`incoming-saves` 是本地中转目录。实际 `.fos` 和 `.f4se` 文件被 `.gitignore` 排除；Codex 应复制文件到检查点目录，不得直接提交、移动或删除中转原文件。

## Manifest save structure

```json
{
  "schemaVersion": 2,
  "checkpointId": "YYYY-MM-DD-short-description",
  "title": "Human-readable title",
  "branch": "main",
  "createdAt": "ISO-8601 timestamp",
  "save": {
    "fos": {
      "fileName": "original-save-file.fos",
      "sizeBytes": 0,
      "sha256": "lowercase SHA-256"
    },
    "f4seCoSave": {
      "status": "present",
      "fileName": "original-save-file.f4se",
      "sizeBytes": 0,
      "sha256": "lowercase SHA-256"
    }
  },
  "game": {
    "location": "unknown",
    "playTime": "unknown",
    "inGameDate": "unknown",
    "currentCompanion": "unknown"
  }
}
```

`f4seCoSave.status` 允许：

- `present`
- `missing`
- `not-applicable`
- `unknown`

## Checkpoint naming

```text
YYYY-MM-DD-short-description
```

例如：

```text
2026-07-17-liberty-reprimed-complete
2026-07-18-before-bunker-hill
2026-07-18-before-tactical-thinking
```

禁止使用含义不明的名称，例如 `save1`、`new-save`、`final`。

## Tracking scope

`progress.md` 默认记录：

- 游戏内日期和总游戏时长；
- 当前地点和当前同伴；
- 当前主线、阵营任务及其**概括性进度**；
- 四大阵营是否敌对、路线是否可继续、是否进入不可逆分支；
- 相比上一个检查点新增的重要完成、失败或关闭任务；
- 重要同伴、NPC、DLC 和聚落状态；
- `.fos` 与 `.f4se` 的配对和完整性；
- 关键选择和使用过的控制台命令；
- 恢复该节点时需要注意的剧情风险。

以下内容**不作为默认归档字段，不需要补充，也不应列为 unknown**：

- 任务日志中的精确目标原文；
- quest stage、Form ID 或其他内部阶段编号；
- Fallout 4 runtime 版本；
- F4SE 精确版本；
- MOD 列表、插件列表和加载顺序；
- 存档恢复测试结果。

F4SE 仍需记录为“已使用”，并保存同名 `.f4se`，但不追踪精确版本。

对用户明确暂不补充的任务、聚落或 NPC 状态，使用一条概括说明，例如“其他任务状态本检查点未补充”，不要逐项生成大量 `unknown`。

只有属于默认追踪范围、但证据不足的信息才标记为 `unknown` 或“待确认”。

## Suggested workflow in VS Code and Codex

1. 在 VS Code 中打开仓库；
2. 执行 `git lfs install`；
3. 将同名 `.fos` 和 `.f4se` 放入 `incoming-saves`；
4. 向 Codex 提供准确文件名、概括性任务进度、阵营关系、同伴、地点、游戏时间及必要截图；
5. Codex 检查存档配对并创建新的检查点；
6. 检查文件名、大小、SHA-256、Git LFS 属性和文档摘要；
7. 提交并推送当前分支；
8. 到达不可逆阵营节点时，从 `main` 的最后安全提交创建对应 `story/*` 分支。

## Commit messages

```text
checkpoint: Liberty Reprimed completed
checkpoint: before Battle of Bunker Hill
branch: begin Railroad storyline
meta: update DLC inventory
fix: correct quest state for checkpoint 2026-07-17
```

## Integrity and safety

- 不修改 `.fos` 或 `.f4se`；
- 不覆盖已有检查点；
- 每个新存档使用新目录；
- 保留两个文件的原始文件名；
- 分别记录文件大小和 SHA-256；
- 校验两个基础文件名完全一致；
- 不执行 force push；
- 不改写已公开检查点历史；
- 不提交凭据、令牌、邮箱或其他敏感认证信息；
- 允许提交绝对路径及其中的 Windows 用户名和目录结构；
- 截图仍须检查凭据、私人通信、无关桌面内容及其他未授权公开的信息。

## Restoring a checkpoint

1. 退出 Fallout 4；
2. 检查 `progress.md` 和 `manifest.json`；
3. 分别校验 `.fos` 和 `.f4se` 的 SHA-256；
4. 确认两者基础文件名完全一致；
5. 备份当前存档目录；
6. 将 `.fos` 和对应 `.f4se` 一起复制回存档目录；
7. 确认所需 DLC、Creation 和相关 MOD 已在当前游戏环境中可用；本仓库默认不记录其精确版本、插件列表或加载顺序；
8. 载入后由用户自行核对任务、同伴和阵营状态，不要求记录恢复测试结果。

## Current project rule

> **在未锁定阵营前，所有关键节点都进入 `main`；发生不可逆阵营分叉后，才从最后一个安全节点创建对应故事线分支。使用 F4SE 时，同名 `.fos` 与 `.f4se` 作为一个逻辑存档整体归档。**
