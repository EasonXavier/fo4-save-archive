# Incoming Saves

本目录用于本地暂存尚未归档的 Fallout 4 存档文件。

使用 F4SE 时，请将同一存档的两个文件一起放入本目录：

```text
<same-basename>.fos
<same-basename>.f4se
```

要求：

- 两个文件除扩展名外，基础文件名必须完全一致；
- `.fos` 是主存档，必须存在；
- 对应 `.f4se` 存在时，必须与 `.fos` 一起归档；
- 不要手动重命名、编辑、压缩或覆盖这两个文件；
- 本目录中的实际存档文件已被 `.gitignore` 排除，不会被直接提交；
- Codex 应把文件复制到新的 `checkpoints/<id>/save/` 目录，而不是移动或删除中转原文件。
