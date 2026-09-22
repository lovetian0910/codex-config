# Codex 全局配置

本仓库只同步三份跨电脑共用的 Codex 配置：

- `AGENTS.md`：全局协作与执行规则，来源和目标均为 `$env:USERPROFILE\.codex\AGENTS.md`。
- `agents/sol-worker.toml`：Sol worker 角色配置，对应 `$env:USERPROFILE\.codex\agents\sol-worker.toml`。
- `agents/luna-worker.toml`：Luna worker 角色配置，对应 `$env:USERPROFILE\.codex\agents\luna-worker.toml`。

`config.toml`、登录信息、插件及其依赖、项目专属配置由每台电脑独立维护，不纳入本仓库。

## 新电脑安装

```powershell
git clone https://github.com/lovetian0910/codex-config.git
Set-Location .\codex-config
```

复制前先备份本机已有文件：

```powershell
$codexRoot = Join-Path $env:USERPROFILE '.codex'
$backupRoot = Join-Path $env:USERPROFILE ('.codex-backup-' + (Get-Date -Format 'yyyyMMdd-HHmmss'))
$files = 'AGENTS.md', 'agents\sol-worker.toml', 'agents\luna-worker.toml'

New-Item -ItemType Directory -Path (Join-Path $backupRoot 'agents') -Force | Out-Null
New-Item -ItemType Directory -Path (Join-Path $codexRoot 'agents') -Force | Out-Null
foreach ($file in $files) {
    $current = Join-Path $codexRoot $file
    if (Test-Path -LiteralPath $current) {
        Copy-Item -LiteralPath $current -Destination (Join-Path $backupRoot $file)
    }
    Copy-Item -LiteralPath (Join-Path $PWD $file) -Destination $current
}
```

## 后续更新

在仓库目录执行 `git pull --ff-only`，然后再次运行上面的备份与复制命令。

要发布本机的新版本，先确认 `git status --short` 没有未保存的仓库改动，再执行：

```powershell
$codexRoot = Join-Path $env:USERPROFILE '.codex'
Copy-Item -LiteralPath (Join-Path $codexRoot 'AGENTS.md') -Destination .\AGENTS.md
Copy-Item -LiteralPath (Join-Path $codexRoot 'agents\sol-worker.toml') -Destination .\agents\sol-worker.toml
Copy-Item -LiteralPath (Join-Path $codexRoot 'agents\luna-worker.toml') -Destination .\agents\luna-worker.toml
git add -- AGENTS.md agents/sol-worker.toml agents/luna-worker.toml
git commit -m 'chore(config): 同步 Codex 全局配置'
git push
```
