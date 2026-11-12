# mac-storage-maintenance

一个用于 macOS 磁盘维护的 Codex 技能。它帮助查找内置盘上的大文件、识别更新后重新出现的缓存、迁移适合外置 SSD 的应用和文件，并验证下载、构建和渲染输出实际写到哪里。

## 安装与使用

在 Codex 中输入：

> 用 `$skill-installer` 安装 https://github.com/jiaxiaofan813-byte/mac-storage-maintenance/tree/main/skills/mac-storage-maintenance

也可以下载此仓库，把 `skills/mac-storage-maintenance` 整个目录复制到 `~/.codex/skills/`，再开启新对话。

> `$mac-storage-maintenance` 检查我的 Mac 内置盘，清理可以安全重建的缓存，并核对应用更新后是否重新写回内置盘。

如果只想查看占用，可以说「只做检查，给我一份按大小排序的清单」。

## 内容

- [技能入口](skills/mac-storage-maintenance/SKILL.md)
- [常见缓存位置和清理方法](skills/mac-storage-maintenance/references/storage-map.md)
- [迁移、验证和回退](skills/mac-storage-maintenance/references/migration.md)
- [实际案例](skills/mac-storage-maintenance/references/field-notes.md)
