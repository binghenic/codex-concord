# Concord 安装与更新

Concord 是一个 Codex Skill。安装目标是仓库中的 **`skills/concord` 文件夹**，而不是整个 Git 仓库。

## 1. 获取仓库

通过命令行获取仓库需要 Git。本仓库公开可读，通常无需登录 GitHub 即可克隆；也可以在 GitHub 页面下载 ZIP 后解压。

```shell
git clone https://github.com/binghenic/codex-concord.git
cd codex-concord
```

以下命令均在克隆后的仓库根目录执行。也可以从 GitHub 下载并解压仓库，再进入包含 `skills` 文件夹的目录。

## 2. 确定安装位置

| 环境 | 通常使用的用户技能目录 |
| --- | --- |
| Windows | `%USERPROFILE%\.codex\skills` |
| macOS / Linux | `~/.codex/skills` |
| 已设置 `CODEX_HOME` | `$CODEX_HOME/skills` |

最终结构应为 `<技能目录>/concord/SKILL.md`。若宿主或管理员配置了其他技能目录，以实际配置为准。

## 3. Windows 首次安装

在 PowerShell 中执行：

```powershell
$concordSource = Join-Path (Get-Location) 'skills\concord'
$concordHome = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
$concordSkills = Join-Path $concordHome 'skills'
$concordDestination = Join-Path $concordSkills 'concord'

if (-not (Test-Path -LiteralPath (Join-Path $concordSource 'SKILL.md'))) {
    throw '未找到源文件。请先进入克隆后的仓库根目录。'
}
if (Test-Path -LiteralPath $concordDestination) {
    throw 'Concord 已安装，请使用下方更新步骤。'
}
New-Item -ItemType Directory -Path $concordSkills -Force | Out-Null
Copy-Item -LiteralPath $concordSource -Destination $concordDestination -Recurse
```

这段命令只用于首次安装，遇到已有目录会停止，避免将不同版本混合复制。

## 4. macOS / Linux 首次安装

在终端中执行：

```sh
concord_skills="${CODEX_HOME:-$HOME/.codex}/skills"

if [ ! -f ./skills/concord/SKILL.md ]; then
  printf '%s\n' '未找到源文件，请进入仓库根目录。' >&2
elif [ -e "$concord_skills/concord" ]; then
  printf '%s\n' 'Concord 已安装，请使用下方更新步骤。' >&2
else
  mkdir -p "$concord_skills" && cp -R ./skills/concord "$concord_skills/concord"
fi
```

若目录受宿主管理或没有写入权限，使用该环境支持的安装方式，不随意修改系统权限。

## 5. 检查是否安装成功

安装目录至少包含以下结构：

```text
concord/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── setup.md
    ├── orchestration.md
    ├── recovery.md
    ├── templates.md
    └── design-notes.md
```

确认没有复制成 `concord/concord/SKILL.md`。按宿主支持的方式刷新技能列表；若当前会话没有重新识别，尝试新开会话或重启应用。

在项目会话中发送：

```text
使用 $concord，先说明你读取到的技能名称、可用能力和当前项目状态。
本次只核对安装，不创建会话、不启动任务、不修改项目。
```

应能加载 Concord 的入口并说明实际能力。能加载 Skill 不代表宿主具备跨会话通信或创建会话工具，这些能力需分别核实。

## 6. 更新已有安装

1. 在本地仓库执行 `git pull --ff-only`。若有本地修改或分支分歧，先保存并核对，不强制覆盖。
2. 确认没有其他会话正在写入或更新 Concord；项目中正在使用该 Skill 的任务先保存交接。
3. 将原安装目录移到技能扫描目录之外的备份位置，保留其完整内容和版本。不要仅在 `skills` 内给旧目录改名，否则宿主可能同时发现两个同名 Skill。
4. 按首次安装步骤复制新版完整目录，再核对文件结构并刷新技能列表。

更新 Skill 不会自动更新项目配置，不会改变模型，也不会恢复暂停的项目。不要把用户的项目配置、交接、源码或登录文件复制进本仓库。

## 7. 回退与卸载

需要回退时，将当前安装移出技能扫描目录，再把完整旧版恢复到原来的 `concord` 位置；检查后刷新技能列表。保留当前版本的备份，避免丢失尚需对照的内容。

卸载时，仅移除确认属于 Concord 的安装目录，再刷新技能列表。项目工程、任务记录与 GitHub 仓库是独立内容，不需要随 Skill 一起删除。

## 常见问题

| 现象 | 优先检查 |
| --- | --- |
| 克隆失败或显示仓库不存在 | 仓库地址、网络连接，以及 Git 的代理或凭据配置 |
| `$concord` 未被识别 | 安装位置、目录层级、文件名及宿主刷新状态 |
| 同时出现两个 Concord | 旧版备份是否仍在技能扫描目录内 |
| 不能联系其他会话 | 宿主是否提供通信工具，实际接收方是否获用户授权 |
| 指定模型不可用 | 当前宿主支持的实际模型名称；不会自动替换 |

使用示例与协作边界见 [README](README.md)。
