# 已知陷阱

### [2026-03-30] 文本中未转义双引号导致游戏卡死（根本原因）
**问题**: 游戏启动时卡在 "Loading StringTable" 步骤，完全无法进入。此问题是整个项目调试过程中所有运行时故障的根本原因
**原因**: .st / .conf 文件中翻译文本包含未转义的双引号 `"`（主要是中文翻译中的引用标点），Enfusion 解析器将其视为字符串结束符，导致解析状态机崩溃
**解决**: 所有文本内嵌双引号改为 `\"`（共修复 13 处 zh_cn 条目）
**预防**: 每次修改 .st 文件后必须运行验证脚本：
```powershell
$lines = [System.IO.File]::ReadAllLines("Language\crx_eai_translation.st", [System.Text.Encoding]::UTF8)
foreach ($line in $lines) {
    $t = $line.Trim()
    if ($t -match '^Target_(\w+)\s+"(.*)"$') {
        $inner = $Matches[2] -replace '\\"', ''
        if ($inner -match '"') { Write-Host "BAD: $t" }
    }
}
```

### [2026-03-29] UTF-8 BOM 导致 String Editor 报错
**问题**: Workbench 报 `Unexpected data 'null' at offset 0(0x0)`
**原因**: PowerShell 默认使用 UTF-8 with BOM 写入文件，Enfusion 不接受 BOM
**解决**: 使用 `New-Object System.Text.UTF8Encoding $false` 显式无 BOM 写入
**预防**: 所有文件写入操作使用 `[System.IO.File]::WriteAllText($path, $content, $utf8NoBom)`

### [2026-03-30] EnfusionMCP 脚本被打包导致游戏异常
**问题**: Publish 后 data.pak 包含了 MCP 控制脚本
**原因**: Workbench Publish 会打包项目中所有已注册文件
**解决**: Publish 前删除 `Scripts/WorkbenchGame/EnfusionMCP/` 目录
**预防**: .gitignore 已配置忽略该目录；发布前检查

### [2026-03-30] Override Edit.conf 使用错误 GUID 导致覆盖失效
**问题**: 翻译不生效，属性仍显示原版英文
**原因**: 自动生成 GUID 而非使用 CRX 原版的精确 GUID
**解决**: 从 CRX 原版 Edit.conf 精确复制每个属性和子元素的 GUID
**预防**: Override 中的 GUID 必须逐一从原版对应，不可自行生成

### [2026-03-30] PowerShell 正则匹配括号文本时损坏输出
**问题**: 批量添加 [CRX] 前缀时，含 `()` 的非 ASCII 文本行被损坏
**原因**: PowerShell `-match` 对含括号的多字节字符正则行为不可靠
**解决**: 分两步——先批量修改，再检测并修复损坏行
**预防**: 批量文本操作后立即运行验证脚本确认总数和格式正确
