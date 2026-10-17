# xbot market

xbot 组件分发仓库。包含 skills、agents、plugins 三类可安装组件。

## 使用

```bash
# 添加 registry
xbot-cli registry add official https://github.com/ai-pivot/market

# 搜索
xbot-cli search           # 浏览全部
xbot-cli search code      # 按关键词搜索

# 安装
xbot-cli install github          # 安装 skill
xbot-cli install code-reviewer   # 安装 agent
xbot-cli install xbot-ambience   # 安装 plugin

# 查看已安装
xbot-cli list-installed
```

## 目录结构

```
├── registry.json      # 索引文件
├── skills/            # 技能 (SKILL.md + scripts/references)
├── agents/            # 子代理角色定义 (.md)
└── plugins/           # 插件 (plugin.json + entry point)
```

## registry.json 格式

```json
{
  "version": 1,
  "items": [
    {
      "type": "skill",
      "name": "github",
      "description": "...",
      "author": "ai-pivot",
      "version": "1.0.0",
      "path": "skills/github",
      "tags": ["github", "git"]
    }
  ]
}
```

type 可选值: `skill` | `agent` | `plugin` | `package`

## Plugin layout (source + prebuilt binaries)

Go plugins (`runtime: stdio` / `grpc`) cannot be built from a registry clone
because their `go.mod` replaces xbot's `plugin/protocol` via a repo-relative
path. The registry therefore ships **prebuilt binaries per platform** alongside
the source:

```
plugins/<plugin-dir>/
├── plugin.json
├── main.go / ...            # source (kept for reference / auditing)
└── bin/
    ├── linux-amd64/<entry-binary>
    ├── linux-arm64/<entry-binary>
    ├── darwin-amd64/<entry-binary>
    └── darwin-arm64/<entry-binary>
```

`xbot-cli install` / marketplace install selects `bin/<GOOS>-<GOARCH>/<entry-binary>`
for the current platform and installs it as `~/.xbot/plugins/<plugin-id>/bin/<entry-binary>`.

Script-runtime plugins (`runtime: script`) install the source directory as-is.
