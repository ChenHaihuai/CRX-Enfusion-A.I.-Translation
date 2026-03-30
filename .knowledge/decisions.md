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

### ADR-003: 直接生成 .conf 而非使用 String Editor Build
**日期**: 2026-03-29
**状态**: accepted
**上下文**: Workbench String Editor 无法打开包含 228 条目 x 13 语言的大型 .st 文件（UI 卡死）
**决策**: 使用 PowerShell 脚本直接从 .st 文件解析生成 13 个语言 .conf 运行时文件
**后果**: 绕过了 Workbench UI 限制，但需要手动维护生成脚本；格式与 String Editor Build 输出完全一致

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
