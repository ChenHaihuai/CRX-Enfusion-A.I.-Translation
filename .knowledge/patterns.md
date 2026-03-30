# 成功模式

### [2026-03-29] PowerShell 逐行解析 .st 生成 .conf
**场景**: 需要从 .st 文件生成运行时 .conf（String Editor 的 Build 功能不稳定，且项目文件含双引号导致解析错误）
**方案**: 逐行读取 .st，匹配 `Id "..."` 和 `Target_xx "..."` 模式，按语言分类收集后写入对应 .conf
**效果**: 准确处理 228 x 13 = 2964 个翻译条目，包括含转义引号的复杂文本。生成脚本见 conventions.md

### [2026-03-30] Workbench Play 模式 + 临时标记验证翻译
**场景**: 英文翻译值与原版相同，无法判断翻译是否生效
**方案**: 临时将 en_us.conf 中 "Stance" 改为 "Stance [T]"，在 Workbench Play 中验证后恢复
**效果**: 快速确认整条翻译链路（override → #ID → .conf → 显示）工作正常

### [2026-03-30] 原版 vs Override 五维交叉验证
**场景**: 需确认翻译覆盖完整性
**方案**: 5 维度对比（Name/Desc/EntryName/EntryDesc/SliderFmt 数量）+ 属性类列表 + GUID 一致性 + #ID 交叉引用
**效果**: 273/273 字段完全匹配，77/77 属性完全覆盖
