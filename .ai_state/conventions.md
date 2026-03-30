# Project Conventions

## 项目信息
- 名称: CRX Enfusion A.I. Translation
- 类型: Arma Reforger 翻译模组（Enfusion 引擎）
- CRX 模组 GUID: `5F268647F8A1A1F4`
- 翻译模组 GUID: `68FAFAEBDEB4C6C7`
- 支持语言: en_us, zh_cn, ja_jp, ko_kr, ru_ru, fr_fr, de_de, es_es, it_it, pl_pl, pt_br, cs_cz, uk_ua (13 种)

## 文件结构
```
├── addon.gproj                          # 项目定义，含 CRX 依赖 + StringTable 注册
├── Edit.conf                            # CRX 原版 Edit.conf 副本（用于对比更新）
├── Configs/Editor/AttributeLists/
│   ├── Edit.conf                        # Override 文件（77 属性，使用 CRX 原版精确 GUID）
│   └── Edit.conf.meta                   # GUID: F3D6C6D25642352C
├── Language/
│   ├── crx_eai_translation.st           # 源 String Table（228 条目 x 13 语言）
│   ├── crx_eai_translation.*.conf       # 运行时文件（每语言一个，共 13 个）
│   └── *.meta                           # Workbench 自动生成的资源元数据
```

## 翻译 ID 命名规则
```
CRX_EAI_T-{分类前缀}{属性简称}_{字段类型}

分类前缀: Grp(小组) | Chr(角色) | Glb(全局) | Exp(实验) | Veh(载具) | Wp(路径点)
字段类型: _Name(名称) | _Desc(描述) | Val_(选项值) | Fmt_(格式字符串)
条目 GUID 格式: {68FB0001A0000XXX}（递增，文件内唯一）
```

## 编码规范
- 所有 .st / .conf 文件: **UTF-8 无 BOM**, **CRLF 换行**
- **文本中双引号必须使用 `\"` 转义**（最重要的规则，未转义会导致游戏卡死）
- Name 字段所有语言加 `[CRX]` 前缀；Description / 选项值 / 格式字符串不加
- Override GUID 必须与 CRX 原版精确一致

## CRX 更新维护流程
1. 用户提供新版 `Edit.conf`，放到项目根目录替换
2. 对比新旧版差异（新增/删除/修改属性）
3. 更新 `Language/crx_eai_translation.st`（新增/修改/删除条目）
4. 更新 `Configs/Editor/AttributeLists/Edit.conf`（使用新版 GUID）
5. 运行生成脚本重新生成 13 个 .conf
6. 运行验证脚本确认无未转义双引号
7. Publish 前删除 EnfusionMCP 脚本

## .conf 生成脚本（PowerShell，项目根目录执行）
```powershell
$stPath = "Language\crx_eai_translation.st"
$langDir = "Language"
$lines = [System.IO.File]::ReadAllLines($stPath, [System.Text.Encoding]::UTF8)
$languages = @("en_us","zh_cn","ja_jp","ko_kr","ru_ru","fr_fr","de_de","es_es","it_it","pl_pl","pt_br","cs_cz","uk_ua")
$data = @{}
foreach ($lang in $languages) { $data[$lang] = @{ ids = [System.Collections.ArrayList]::new(); texts = [System.Collections.ArrayList]::new() } }
$currentId = $null
foreach ($line in $lines) {
    $trimmed = $line.Trim()
    if ($trimmed -match '^Id\s+"(.+)"$') { $currentId = $Matches[1] }
    elseif ($currentId -and $trimmed -match '^Target_(\w+)\s+"(.*)"$') {
        $lang = $Matches[1]; $text = $Matches[2]
        if ($data.ContainsKey($lang)) { [void]$data[$lang].ids.Add($currentId); [void]$data[$lang].texts.Add($text) }
    }
}
foreach ($lang in $languages) { Write-Host "$lang : $($data[$lang].ids.Count) entries" }
$utf8NoBom = New-Object System.Text.UTF8Encoding $false
foreach ($lang in $languages) {
    $sb = [System.Text.StringBuilder]::new()
    [void]$sb.AppendLine("StringTableRuntime {"); [void]$sb.AppendLine(" Ids {")
    foreach ($id in $data[$lang].ids) { [void]$sb.AppendLine("  `"$id`"") }
    [void]$sb.AppendLine(" }"); [void]$sb.AppendLine(" Texts {")
    foreach ($text in $data[$lang].texts) { [void]$sb.AppendLine("  `"$text`"") }
    [void]$sb.AppendLine(" }"); [void]$sb.Append("}")
    [System.IO.File]::WriteAllText((Join-Path $langDir "crx_eai_translation.$lang.conf"), $sb.ToString(), $utf8NoBom)
}
```

## 当前统计
- 属性数量: 77 个 CRX Editor Attribute
- 翻译条目: 228 个（.st）
- Override 引用: 273 个 #ID
- Name 条目: 78 个（全部有 [CRX] 前缀）
