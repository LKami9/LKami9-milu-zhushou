# LKami9秘录助手 · 在线模块仓库

LKami9秘录助手的**在线模块**仓库（用于添加到 OmniToolbox 的在线模块列表），作者 LKami9。

> 仓库名 `LKami9-milu-zhushou` 是 `LKami9秘录助手` 的 ASCII 转写。
> GitHub 的仓库名只接受 `A-Za-z0-9._-`，中文只能放在 Description 和文档里，所以本仓库地址是拼音形式。

## 在 Omni 中添加

插件设置 → 在线模块 → 填本仓库地址：

```text
https://github.com/LKami9/LKami9-milu-zhushou
```

首次安装后模块默认关闭，到「树树妙妙屋 → 在线」里手动启用。

## 目录结构

```text
LKami9-milu-zhushou/
  Modules/
    LKami9FolkloreBooks.cs      # 传承录 / 秘籍检测与兑换需求
  TreeHouseModules.json         # 清单（含 SHA256）
  .gitattributes                # 锁定换行符，保证 SHA 稳定
  README.md
```

## 模块清单

| InternalName | 名称 | 版本 | Omni 最低版本 |
|---|---|---|---|
| `LKami9FolkloreBooks` | LKami9秘录助手 | 1.0.0 | 1.1.3.0 |

功能：列出已使用 / 未使用的传承录与秘籍，显示兑换材料、持有量与缺口，材料名可点击复制。

命令：`/omni LKami9FolkloreBooks scan` 重新检测。

## 发布 / 更新流程

1. 改完 `Modules/*.cs`
2. 重新计算该文件的 SHA256：

```powershell
(Get-FileHash -LiteralPath '.\Modules\LKami9FolkloreBooks.cs' -Algorithm SHA256).Hash
```

3. 把结果写进 `TreeHouseModules.json` 的 `Sha256`，并**提高 `Version`**
4. 提交推送
5. 在 Omni 里刷新在线模块，确认能下载、启用、保存配置

> 只改 `Sha256` 不提 `Version` 不会触发已安装模块的更新。
> 文件中途被改过（哪怕只是注释或空格）都要重算摘要。

## 换行符必须保持锁定

系统级 `core.autocrlf=true`（Git for Windows 的默认值）会改写换行符。一旦生效，GitHub 上的文件字节就不再等于本地文件，Omni 的 SHA256 校验会失败。

根目录的 `.gitattributes` 用 `* -text` 禁止一切换行符转换，**不要删除或修改它**。

## 本地自检

```powershell
$root = '.'
$manifest = Get-Content -Raw -LiteralPath (Join-Path $root 'TreeHouseModules.json') | ConvertFrom-Json
foreach ($m in $manifest.Modules) {
    $actual = (Get-FileHash -LiteralPath (Join-Path $root $m.File) -Algorithm SHA256).Hash
    '{0}: {1}' -f $m.InternalName, ($actual -eq $m.Sha256)
}
```

输出 `True` 表示本地文件与清单一致。

## 说明

- 在线模块与 Omni 同权限、无独立沙箱，请只从可信来源安装。
- 模块不做任何自动化行为之外的上行请求；所有功能都是本地读写游戏状态。
- 模块内的自动使用功能（传承录 / 秘籍）默认关闭，需在设置中手动开启。
