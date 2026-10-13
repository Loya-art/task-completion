# D:\hermes-environment\ 备份结构

## 目录布局

```
D:\hermes-environment\
├── tools/             ← CLI 二进制文件
│   ├── dreamina/
│   │   ├── dreamina.exe
│   │   └── version.json
│   └── kling/         ← npm 全局包，此目录为空（记录用）
│
├── skills/            ← Skill 文件副本
│   ├── kling/         ← 可灵 CLI skill（4 files）
│   ├── dreamina.md    ← 即梦 CLI skill
│   ├── gstack-skills/ ← Gstack 开发工作流套件
│   ├── gstack-upgrade/
│   ├── brain-ops/
│   └── gbrain-multi-agent-search/
│
├── credentials/
│   └── kling.credentials  ← 可灵 OAuth 凭据
│
├── hermes-core/
│   ├── config.yaml    ← Hermes 配置
│   └── .env           ← API 密钥
│
├── references/                   ← 克隆的 GitHub 仓库（只读参考）
│   ├── awesome-hermes-agent/             英文资源大全（4.7k⭐）
│   ├── awesome-hermes-agent-zh/          中文版
│   ├── wondelai-skills/                  跨平台技能库（380+⭐）
│   ├── hermes-plugins/                   运维插件集合
│   ├── hermes-workspace/                 Web GUI 工作区（500+⭐）
│   ├── portable-hermes-agent/            Windows 桌面一体包
│   ├── hermes-web-search-plus/           多引擎搜索插件
│   ├── hermes-dojo/                      自我改进系统
│   ├── hermes-incident-commander/        SRE 事故自愈
│   ├── hermes-life-os/                   个人 OS agent
│   ├── super-hermes/                     元推理增强
│   ├── hermes-skill-factory/             自动生成技能
│   └── hermes-skill-marketplace/         技能市场发布
│
└── README.md
```

## 同步时机

每次安装了新的 skill/CLI 工具后，立即同步：

```python
import shutil, os

d_root = "D:/hermes-environment"
home = os.path.expanduser("~")
skills_dir = os.path.join(home, "AppData", "Local", "hermes", "skills")

# 同步 skills
for skill_name in os.listdir(skills_dir):
    src = os.path.join(skills_dir, skill_name)
    dst = os.path.join(d_root, "skills", skill_name)
    if os.path.isdir(src) and not os.path.exists(dst):
        shutil.copytree(src, dst)
        print(f"Synced {skill_name}")

# 对于已有目录的更新（新文件追加到已同步过的目录）：
def sync_dir(src, dst):
    os.makedirs(dst, exist_ok=True)
    for root, dirs, files in os.walk(src):
        rel = os.path.relpath(root, src)
        td = os.path.join(dst, rel)
        os.makedirs(td, exist_ok=True)
        for f in files:
            shutil.copy2(os.path.join(root, f), os.path.join(td, f))
```

## 规则

- **不移动原件** — 只复制，不删除原位置文件
- **覆盖旧副本** — 同名文件直接覆盖
- **不备份运行时数据库** — state.db 太大且频繁变化，不复制
