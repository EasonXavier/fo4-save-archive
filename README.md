# Fallout 4 Save Archive

A public, version-controlled archive of my **Fallout 4** save files, quest progress, faction states, companion states, mod environment, and branching storylines.

本仓库用于长期保存《辐射 4》的关键存档及其上下文，使我能够在未来任意时间准确回溯到某个剧情节点，而不只是保留一个无法辨认的存档文件。

## Branch model

### `main`

`main` 只保存**尚未锁定最终阵营路线**的公共时间线。

只要 Minutemen、Railroad、Brotherhood of Steel 和 Institute 的主要路线仍可并行推进，检查点就继续提交到 `main`。

完成某个阵营的前期任务，不等于需要建立分支。只有发生以下情况之一时，才从最后一个安全检查点创建故事线分支：

- 某阵营永久敌对；
- 其他阵营主线被关闭；
- 游戏出现明确的不可逆警告；
- 已确定以某个阵营完成主线结局。

### Story branches

阵营锁定后使用：

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

使用 F4SE 时，一个逻辑存档通常由两个同名文件组成：

```text
<same-basename>.fos
<same-basename>.f4se
```

- `.fos` 是 Fallout 4 主存档，始终必须存在；
- `.f4se` 是 F4SE co-save，用于保存 F4SE 插件的附加持久化数据；
- 两个文件除扩展名外，基础文件名必须完全一致；
- 对应 `.f4se` 存在时，必须与 `.fos` 一起归档、校验、提交和恢复；
- 不得把其他存档的 `.f4se` 与当前 `.fos` 配对；
- 不得创建空 `.f4se` 或伪造缺失文件；
- 已知使用 F4SE 但对应 `.f4se` 缺失时，提交前必须明确报告并等待决定；只有获得明确许可后，才能以 `missing` 状态归档。

`.fos` 和 `.f4se` 均通过 Git LFS 管理。

## Repository layout

```text
checkpoints/
  YYYY-MM-DD-checkpoint-name/
    save/
      original-save-file.fos
      original-save-file.f4se   # 使用 F4SE 且文件存在时必须包含
    progress.md
    manifest.json
    screenshots/
    environment/
      plugins.txt
      loadorder.txt
      modlist.txt
      runtime.txt
incoming-saves/
  README.md
```

每个检查点至少应包含：

- 原始 `.fos` 主存档；
- 同名 `.f4se` co-save：对应文件存在时必须包含；
- `progress.md`：人工可读的任务与剧情状态；
- `manifest.json`：两个存档文件各自的文件名、大小、SHA-256、状态，以及分支和检查点元数据；
- 必要时附任务日志截图和 MOD 环境记录。

`incoming-saves` 是本地中转目录。实际 `.fos` 和 `.f4se` 文件被 `.gitignore` 排除，Codex 应复制文件到检查点目录，不得直接提交、移动或删除中转原文件。

## Manifest save structure

推荐结构：

```json
{
  "schemaVersion": 2,
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
  }
}
```

`f4seCoSave.status` 使用以下值：

- `present`：同名 `.f4se` 已归档；
- `missing`：已知使用 F4SE，但对应文件缺失，并已获得明确许可继续归档；
- `not-applicable`：明确未使用 F4SE 或该存档不产生 co-save；
- `unknown`：现有证据无法确认。

## Checkpoint naming

目录名称统一使用：

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

## What to record

`progress.md` 应尽量记录：

- 游戏内日期和总游戏时长；
- 当前地点和当前同伴；
- 当前主线任务及目标；
- 四大阵营的任务进度；
- 阵营敌对、锁线和不可逆状态；
- 已完成的重要支线、DLC 和同伴任务；
- 聚落、关键 NPC 和关键物品状态；
- Fallout 4 runtime、F4SE、插件顺序及关键 MOD；
- `.fos` 与 `.f4se` 的配对和完整性状态；
- 使用过的控制台命令；
- 恢复该存档后应当先做和不应当做的事项。

无法从存档、截图或用户说明中确认的信息必须标记为 `unknown` 或“待确认”，不能猜测。

## Suggested workflow in VS Code and Codex

1. 克隆仓库并在 VS Code 中打开；
2. 确认本机已安装 Git LFS，并执行 `git lfs install`；
3. 将新存档的同名 `.fos` 和 `.f4se` 放入 `incoming-saves`；
4. 向 Codex 提供准确文件名、任务日志截图、当前同伴和关键选择；
5. 让 Codex 依据根目录的 `AGENTS.md` 检查文件配对，复制到新的检查点目录，并生成 `progress.md` 与 `manifest.json`；
6. 检查文件名、大小、SHA-256、`git diff` 和 Git LFS 属性；
7. 提交并推送当前分支；
8. 到达不可逆阵营节点时，从 `main` 的最后安全提交创建对应 `story/*` 分支。

## Commit messages

推荐格式：

```text
checkpoint: Liberty Reprimed completed
checkpoint: before Battle of Bunker Hill
branch: begin Railroad storyline
meta: update mod environment
fix: correct quest state for checkpoint 2026-07-17
```

## Integrity and safety

- 不修改 `.fos` 或 `.f4se` 文件内容；
- 不覆盖已有检查点中的存档；
- 每个新存档使用新目录；
- 保留两个文件的原始文件名；
- 分别为 `.fos` 和 `.f4se` 记录文件大小和 SHA-256；
- 校验两个文件的基础文件名完全一致；
- 不执行 force push；
- 不改写已公开检查点的历史；
- 不提交账号凭据、令牌、邮箱或其他敏感认证信息；
- 允许提交绝对文件路径，以及绝对路径中包含的 Windows 用户名和目录结构；仓库所有者已明确接受该公开风险；
- 不得仅因文件包含绝对路径而自动删改、脱敏或阻止提交；
- 截图和配置文件仍须检查凭据、私人通信、无关桌面内容及其他未授权公开的信息。

## Restoring a checkpoint

1. 退出 Fallout 4；
2. 检查该检查点的 `progress.md`、`manifest.json` 和 MOD 环境；
3. 分别校验 `.fos` 和 `.f4se` 的 SHA-256；
4. 确认两者基础文件名完全一致；
5. 备份当前 `Documents/My Games/Fallout4/Saves`；
6. 将目标 `.fos` 和对应 `.f4se` 一起复制回存档目录；
7. 恢复匹配的 Fallout 4 runtime、F4SE、DLC、Creation 和 MOD；
8. 载入后再次核对任务日志、同伴、阵营状态及依赖 F4SE 插件的状态。

## Current project rule

目前的核心规则是：

> **在未锁定阵营前，所有关键节点都进入 `main`；只有在剧情发生不可逆阵营分叉后，才从最后一个安全节点创建对应的故事线分支。使用 F4SE 时，同名 `.fos` 与 `.f4se` 作为一个逻辑存档整体归档。**
