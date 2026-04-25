# 架构决策记录 (ADR)

### ADR-001: 使用 Override 而非脚本注入进行翻译
**日期**: 2026-03-29
**状态**: accepted
**上下文**: CRX 使用硬编码英文字符串，需要选择翻译注入方式
**决策**: 通过 Enfusion 的 Override in addon 机制覆盖 Edit.conf，将硬编码字符串替换为 #ID 引用
**后果**: 干净的模组结构（只有 1 个 override conf + 语言文件），不需要修改 CRX 的脚本代码

### ADR-002: 仅翻译 Edit.conf
**日期**: 2026-03-29
**状态**: accepted
**上下文**: CRX 有多个 conf 文件和脚本中的硬编码文本
**决策**: 仅覆盖 `Configs/Editor/AttributeLists/Edit.conf`，这是 Game Master 属性面板的核心文件
**后果**: 翻译模组保持精简，其他 CRX UI 文本暂不翻译

### ADR-003: .conf 生成方式 — String Editor Build 为主，PowerShell 备用
**日期**: 2026-03-29
**状态**: superseded (2026-04-26 修正)
**上下文**: 初期 String Editor 卡死被误判为大文件导致，实际根因是未转义双引号
**决策**: 修复双引号后 String Editor 可正常打开 230+ 条目的 .st 文件并 Build Runtime Table。PowerShell 脚本保留为备用方案
**后果**: 正常使用 Workbench 工作流（打开 .st → Build Runtime Table → Publish）

### ADR-004: Name 字段添加 [CRX] 前缀
**日期**: 2026-03-30
**状态**: accepted
**上下文**: Game Master 中 CRX 属性与原版和其他模组属性混在一起，难以区分
**决策**: 所有 Name 字段（78 个 x 13 语言）添加 `[CRX]` 前缀
**后果**: 用户可快速识别哪些设置来自 CRX 模组

### ADR-005: addon.gproj 保留 StringTableSource 引用
**日期**: 2026-03-30
**状态**: accepted
**上下文**: StringTableSource 引用在游戏运行时曾导致 "Wrong GUID/name" 错误
**决策**: 保留 StringTableSource 以维持 Workbench 兼容性（String Editor 需要它定位 .st 文件）
**后果**: Workbench Play 模式正常工作；实际游戏运行时该错误在修复双引号转义后已消失
