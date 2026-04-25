# 工具使用经验

### Enfusion Workbench
**版本**: Arma Reforger Tools (Diag)
**配置**: NET API 端口 5775, 需在 Options 中勾选 Enable NetAPI
**注意**:
- String Editor 之前误判为大文件卡死，实际根因是未转义双引号；修复后可正常处理 230+ 条目
- Override in addon 功能正常，Duplicate 会生成全新 GUID
- Publish 功能会将项目目录所有文件打包进 data.pak，包括不应包含的脚本
- Play 模式使用项目源文件目录，不需要 Publish 即可测试

### EnfusionMCP
**版本**: 通过 wb_launch / wb_connect 连接
**配置**: Handler scripts 安装到 `Scripts/WorkbenchGame/EnfusionMCP/`
**注意**:
- 发布前必须清理（wb_cleanup 或手动删除）
- 无法直接读取 .pak 文件内容
- wb_localization 的 modify 操作有时报 item not found
- mod_build 可能超时（>300s）

### PowerShell (.conf 生成)
**版本**: 系统自带
**配置**: 无特殊配置
**注意**:
- 写文件使用 `[System.Text.UTF8Encoding]::new($false)` 避免 BOM
- `-match` 正则对含括号的非 ASCII 文本不可靠，需后验证
- `[System.IO.File]::ReadAllLines` 能正确处理 UTF-8 多字节字符
- 生成脚本见 MAINTENANCE.md

### PakEntpacker
**版本**: 外部工具
**配置**: 解包 CRX pak 到 `D:\ArmaReforgerMod\addons\CRXEnfusionA.I._5F268647F8A1A1F4\data\`
**注意**: 需要用户手动解包，AI 无法直接读取 pak 文件
