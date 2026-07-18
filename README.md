# Fallout 4 Save Archive

A public, version-controlled archive of my **Fallout 4** save files, quest progress, faction states, companion states, mod environment, and branching storylines.

本仓库用于长期保存《辐射 4》的关键存档及其上下文，使我能够在未来任意时间准确回溯到某个剧情节点，而不只是保留一个无法辨认的 `.fos` 文件。

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

## Repository layout

```text
checkpoints/
  YYYY-MM-DD-checkpoint-name/
    save/
      original-save-file.fos
    progress.md
    manifest.json
    screenshots/
    environment/
      plugins.txt
      loadorder.txt
      modlist.txt
      runtime.txt
```

每个检查点至少应包含：

- 原始 `.fos` 存档；
- `progress.md`：人工可读的任务与剧情状态；
- `manifest.json`：文件名、大小、SHA-256、分支和检查点元数据；
- 必要时附任务日志截图和 MOD 环境记录。

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
- 使用过的控制台命令；
- 恢复该存档后应当先做和不应当做的事项。

无法从存档、截图或用户说明中确认的信息必须标记为 `unknown` 或“待确认”，不能猜测。

## Suggested workflow in VS Code and Codex

1. 克隆仓库并在 VS Code 中打开。
2. 将新的 `.fos` 文件复制到一个新的检查点目录。
3. 向 Codex 提供任务日志截图、当前同伴和关键选择。
4. 让 Codex依据根目录的 `AGENTS.md` 生成或更新 `progress.md` 与 `manifest.json`。
5. 检查 `git diff` 和文件路径。
6. 提交并推送当前分支。
7. 到达不可逆阵营节点时，从 `main` 的最后安全提交创建对应 `story/*` 分支。

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

- 不修改 `.fos` 文件内容；
- 不覆盖已有检查点中的存档；
- 每个新存档使用新目录；
- 为每个 `.fos` 记录 SHA-256；
- 不执行 force push；
- 不改写已公开检查点的历史；
- 不提交账号凭据、令牌、邮箱或其他敏感认证信息；
- 允许提交绝对文件路径，以及绝对路径中包含的 Windows 用户名和目录结构；仓库所有者已明确接受该公开风险；
- 不得仅因文件包含绝对路径而自动删改、脱敏或阻止提交；
- 截图和配置文件仍须检查凭据、私人通信、无关桌面内容及其他未授权公开的信息。

## Restoring a checkpoint

1. 退出 Fallout 4；
2. 检查该检查点的 `progress.md` 和 MOD 环境；
3. 校验 `.fos` 的 SHA-256；
4. 备份当前 `Documents/My Games/Fallout4/Saves`；
5. 将目标 `.fos` 复制回存档目录；
6. 恢复匹配的 DLC、Creation 和 MOD；
7. 载入后再次核对任务日志、同伴和阵营状态。

## Current project rule

目前的核心规则是：

> **在未锁定阵营前，所有关键节点都进入 `main`；只有在剧情发生不可逆阵营分叉后，才从最后一个安全节点创建对应的故事线分支。**
