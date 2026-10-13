# Skills Hub 安装注意事项

## 安装命令

```bash
# 搜索
hermes skills search <query>

# 预览
hermes skills inspect <identifier>

# 安装（自动确认）
echo y | hermes skills install <identifier>
```

## 常见问题

### 1. 大小写敏感性

`hermes skills install` 在部分版本中**区分大小写**，但 `inspect` 不区分。

- ✅ `hermes skills inspect "Gstack Skills"` → 解析成功
- ❌ `hermes skills install gstack-skills` → "No exact match"
- ✅ `echo y | hermes skills install "Gstack Skills"` → 成功安装

**规则：** 如果 `install <lowercase-id>` 报 "No exact match"，用 search 返回的显示名称（含大小写）重试。

### 2. 自动确认

`hermes skills install` 有交互确认提示 "Confirm [y/N]"。用 `echo y |` 管道自动确认。

### 3. GitHub 网络问题

Skills Hub 的部分源（skills.sh, gstack）从 GitHub 拉取数据。在中国网络环境下：
- `npx skills add <repo>` 用 git clone，可能超时
- `hermes skills install` 用 GitHub API + raw 文件下载，通常可达
- 如果 install 报 "Could not fetch"，先检查 GitHub 是否可达

### 4. 安全扫描拦截

部分 skill 会被安全扫描拦截（DANGEROUS 评级）。`--force` 不能绕过危险评级。拦截后放弃安装。

### 5. 子 skill vs 完整 skill

Garry Tan 的 gstack/gbrain 仓库包含多个独立子 skill：
- `gstack-upgrade`（仅升级功能）
- `setup-gbrain`（仅安装）
- `brain-ops`（仅运维）

它们之间**不互相依赖**，需要分别安装。完整的套件用 `Gstack Skills`（含 router + office-hours + review 等 15+ 命令）。
