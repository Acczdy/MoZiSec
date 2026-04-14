# MoZiSec - 墨子Sec

> 墨子 MoZiSec · 非攻兼爱 · 防御性安全测试工具

[![Version](https://img.shields.io/badge/version-v1.0-blue)]()
[![License](https://img.shields.io/badge/license-Apache--2.0-green)]()
[![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS%20%7C%20WSL--Kali-blueviolet)]()

## 📹 演示

![MoZiSec Demo](./MoZi.gif)

> 💡 **提示**: 等待 GIF 加载

> 墨家以 **兼爱、非攻** 为核心，构建了包含十大主张的严密思想体系，宗旨是 **兴天下之利，除天下之害**。
>
> 「兼相爱，交相利。」—— 在安全领域，攻与防本就相生相成。本工具以 **防御性安全** 为立场，旨在通过了解攻击手法来更好地守护系统。

## 项目思路

Claude Code 本身已经是一套成熟的 AI 编程与运维平台，内置了完善的权限管理、Skill 体系、MCP 集成、记忆和沙箱机制。本项目的想法很简单——与其在这些能力之上再构建一个管理平台，不如把 Claude Code 当作底层引擎，用一个极简的 shell 入口按安全领域组织 Skill 和工作区。安装新的 Skill、接入 MCP 服务、补充工具链，都直接交给 Claude Code 完成。

### 优秀项目

AI 安全测试方向，社区中已有不少优秀实践，以下列举部分代表项目：

| # | 项目 | 简介 |
|---|------|------|
| 1 | [CyberStrikeAI](https://github.com/Ed1s0nZ/CyberStrikeAI) | 一体化 AI 安全测试平台 |
| 2 | [LuaN1aoAgent](https://github.com/SanMuzZzZz/LuaN1aoAgent) | AI 驱动的渗透测试框架 |
| 3 | [DeepAudit](https://github.com/lintsinghua/DeepAudit) | AI 安全审计工具 |
| 4 | [hackingtool](https://github.com/Z4nzu/hackingtool) | 自动化黑客工具箱 |
| … | … | … |

这些项目从不同角度推进了 AI 与安全测试的结合。本项目选择了一条更轻量的路径，把已有的能力组合起来，让使用者能快速搭建自己的安全测试工作台。

### 使用建议

推荐在 WSL 的 Kali Linux 环境中使用。Kali 预装了完整的渗透测试工具链，而 Claude Code 可以在缺少工具时直接帮你安装配置。用户需要做的只是：添加 Skill、接入 MCP、按自己的实际场景不断扩充。

## 快速开始

### 安装

```bash
# 创建软链接，全局可用
sudo ln -s "$(pwd)/mozi" /usr/local/bin/mozi
chmod +x mozi
```

之后在任何终端直接输入 `mozi` 即可启动。

## 功能特性

### 1. 自动工作区发现

MoZiSec v1.0 引入自动发现机制，无需手动配置即可识别工作区：

- 自动扫描 `$MOZI_WORKSPACE_ROOT` 下的所有一级目录
- 识别标准：目录含有 `CLAUDE.md` 文件
- 自动提取工作区名称、分组、别名

### 2. 工作区结构

```
MoZiSec/
├── api-security/              # API 安全
│   ├── CLAUDE.md            # 角色定义（必需）
│   ├── .claude/
│   │   └── skills/          # 技能目录
│   └── tmp/                 # 临时文件目录
├── iam/                     # 身份与访问管理
├── penetration-testing/      # 渗透测试
├── reports/                 # 安全报告
└── web-security/            # Web 安全
```

### 3. 核心功能

| 功能 | 描述 |
|------|------|
| **常用工作区** | 快速访问 5 个高频工作区 |
| **全量工作区** | 浏览所有已发现的工作区 |
| **分组导航** | 按分类浏览（核心安全、专项领域、文档管理等） |
| **创建工作区** | 一键创建新的工作区目录结构 |
| **历史记录** | 记录最近 5 次访问，支持快速返回 |
| **技能预览** | 显示每个工作区的技能数量和列表 |

### 4. 分组分类

| 分组 | 包含工作区 |
|------|-----------|
| 核心安全 | 渗透测试、API 安全、Web 安全 |
| 专项领域 | 身份认证 (IAM) |
| 文档管理 | 安全报告 |

**新增自定义分组**：在 `CLAUDE.md` 中添加 `<!-- group: 你的分组名 -->` 即可将工作区归类到任意分组。

## 运行逻辑流程图

```
┌─────────────────────────────────────────────────────────────────┐
│                        启动 ./mozi [参数]                        │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
              ┌─────────────────────────────┐
              │    检测操作系统类型          │
              │    设置 SCRIPT_DIR 等路径    │
              └─────────────┬───────────────┘
                            │
                            ▼
              ┌─────────────────────────────┐
              │    discover_workspaces()     │
              │  遍历 $SCRIPT_DIR/*/         │
              │  跳过: .claude .git memory   │
              │  识别: 含 CLAUDE.md 的目录    │
              │  提取: 名称/分组/别名/名言    │
              └─────────────┬───────────────┘
                            │
                            ▼
               判断 $1 参数 ─┬─ "init"  → 输出环境变量 + 函数定义 (eval 集成)
                             │
                             ├─ "create" / "new"
                             │   → 交互式创建工作区
                             │   → 生成目录结构 + CLAUDE.md
                             │
                             ├─ 无参数
                             │   │
                             │   ▼
                             │  ┌──────────────────────────┐
                             │  │    mozi_interactive()    │
                             │  │  检查 fzf 是否安装       │
                             │  └────────┬─────────────────┘
                             │           │
                             │      ┌────┴────┐
                             │      │  有 fzf  │  → show_main_menu_fzf()
                             │      └────┬────┘
                             │           │
                             │     ┌─────┼──────────────────────┐
                             │     ▼     ▼                      ▼
                             │  ★ 常用  全量工作区          分组导航
                             │  工作区   (所有发现的工作区)   (按分组筛选)
                             │     │     │                      │
                             │     └─────┴──────────┬───────────┘
                             │                      ▼
                             │            ┌──────────────────┐
                             │            │  enter_workspace  │
                             │            │  (dir)           │
                             │            └────────┬─────────┘
                             │                     │
                             │        ┌────────────┼────────────┐
                             │        ▼            ▼            ▼
                             │   record_visit  显示欢迎信息  显示提示
                             │   (历史记录)   (名称/技能数)  (推荐Skill)
                             │        │            │            │
                             │        └────────────┴────────────┘
                             │                     ▼
                             │            ┌──────────────────┐
                             │            │  role_interact   │
                             │            │  [1] claude      │
                             │            │  [2] claude --   │
                             │            │      resume      │
                             │            │  [3] 手动命令    │
                             │            │  [h] 技能帮助    │
                             │            │  [b] 返回菜单    │
                             │            │  [q] 退出        │
                             │            └────────┬─────────┘
                             │                     │
                             │                 [1]/[2]
                             │                     ▼
                             │              cd 到工作区目录
                             │              启动 claude
                             │
                             ├─ 其他参数 (工作区名/别名/编号/路径)
                             │   │
                             │   ▼
                             │  parse_role_input()
                             │  匹配顺序: 编号(0-4) → 目录名 → 别名 → 路径
                             │       │
                             │    匹配成功          匹配失败
                             │       │                │
                             │       ▼                ▼
                             │  enter_workspace    提示"未知工作区"
                             │
                             └─ "help" / "config"
                                 → 显示帮助/配置信息
```

### 关键组件说明

| 组件 | 函数 | 作用 |
|------|------|------|
| **自动发现** | `discover_workspaces()` | 扫描含 `CLAUDE.md` 的目录，提取元数据 |
| **输入解析** | `parse_role_input()` | 按编号 → 目录名 → 别名 → 路径顺序匹配 |
| **工作区导航** | `select_common_fzf()`<br>`select_all_workspaces_fzf()`<br>`select_category_fzf()` | 基于 fzf 的三种浏览方式 |
| **进入工作区** | `enter_workspace()` | 记录访问历史 → 显示欢迎 → 显示提示 → 交互菜单 |
| **启动 Claude** | `role_interact()` | 在当前工作区目录下启动 `claude` |
| **Shell 集成** | `mozi_init()` | 输出环境变量 + 函数定义（可选，用于 eval 集成） |
| **创建向导** | `create_workspace_interactive()` | 交互式输入，自动生成目录结构和 `CLAUDE.md` |
| **历史记录** | `record_visit()` | 保存最近 5 次访问到 `~/.mozi_history.json` |

## 使用方法

### 交互模式

```bash
# 启动交互菜单
./mozi

# 或使用别名
mozi
```

### 命令模式

```bash
# 进入指定工作区
./mozi api-security
./mozi p          # 使用别名
./mozi 0          # 使用编号

# 创建工作区
./mozi create
./mozi new

# 查看配置
./mozi config

# 查看帮助
./mozi help
```

### Shell 集成

```bash
# 创建软链接后即可使用（无需额外配置）
sudo ln -s "$(pwd)/mozi" /usr/local/bin/mozi

# 之后可直接使用
mozi api-security
mozi create
```

## 自定义配置

### 方法 1：在 CLAUDE.md 中配置（推荐）

在工作区的 `CLAUDE.md` 文件开头添加 HTML 注释配置：

```markdown
# 我的工作区名称
<!-- group: 专项领域 -->
<!-- alias: myw -->
<!-- quote: 我的自定义名言 -->

## 启动方式
...
```

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| `group` | 分组名称（核心安全/威胁防御/专项领域等） | 专项领域 |
| `alias` | 快捷别名 | 目录名前 3 个字母 |
| `quote` | 墨子名言 | 守土有责，防患未然 |

**示例：**

```bash
# 创建新工作区
mkdir -p my-audit-tools/.claude/skills
cat > my-audit-tools/CLAUDE.md << 'EOF'
# 审计工具集
<!-- group: 取证分析 -->
<!-- alias: audit -->
<!-- quote: 明察秋毫，见微知著 -->

## 启动方式
cd my-audit-tools
claude
EOF
```

**自定义分组**：如果使用了新的分组名（如 `my-group`），该分组会自动添加到「分组导航」菜单中，无需修改脚本。

### 方法 2：修改脚本中的 DEFAULT_GROUPS

编辑 `mozi` 第 54-62 行，添加默认分组映射：

```bash
declare -A DEFAULT_GROUPS=(
    ["api-security"]="核心安全"
    ["my-audit-tool"]="取证分析"  # 新增
    ["my-scanner"]="威胁防御"     # 新增
)
```

### 方法 3：自定义全局配置文件

配置文件位置：
- **Linux/macOS**: `~/.mozi_workspaces.json`

### 配置示例

```json
{
  "workspaces": [
    {
      "dir": "my-custom-tools",
      "name": "我的渗透工具",
      "alias": "mpt",
      "group": "专项领域",
      "quote": "工欲善其事，必先利其器"
    }
  ]
}
```

### 环境变量

| 变量 | 描述 | 默认值 |
|------|------|-------|
| `MOZI_WORKSPACE_ROOT` | 工作区根目录 | 脚本所在目录 |
| `MOZI_CONFIG_FILE` | 配置文件路径 | `~/.mozi_workspaces.json` |
| `MOZI_HISTORY_FILE` | 历史记录路径 | `~/.mozi_history.json` |

## 技能系统

每个工作区可包含多个 Skills，位于 `.claude/skills/` 目录：

```
api-security/.claude/skills/
├── detecting-api-enumeration-attacks/
├── performing-jwt-none-algorithm-attack/
└── ...
```

### 使用 Skill

在 Claude Code 中输入：

```
/skill-name
```

### 创建 Skill

```bash
mkdir -p api-security/.claude/skills/my-skill
touch api-security/.claude/skills/my-skill/SKILL.md
```

## 项目结构

```
MoZiSec/
├── mozi                 # 主脚本（v1.0 自动发现版）
├── README.md            # 说明文档
├── .claude/             # Claude 配置
│   ├── settings.json
│   └── settings.local.json
├── docs/                # 文档
│   └── workspace-config-example.md
├── .mcp.json            # MCP 服务器配置
├── memory/              # 记忆目录
└── <workspace>/         # 工作区目录
    ├── CLAUDE.md
    ├── .claude/skills/
    └── tmp/
```

## 系统要求

| 组件 | 最低要求 | 推荐 |
|------|---------|------|
| Bash | 4.0+ | 5.0+ |
| fzf | 0.30+ | 最新 |
| 颜色支持 | 8 色 | 256 色 |

### 依赖安装

```bash
# Debian/Ubuntu
apt install fzf

# macOS
brew install fzf
```

## 安全说明

MoZiSec 是防御性安全测试工具，设计用于：

- ✅ 授权的安全测试
- ✅ 防御性安全研究
- ✅ 教育和学习目的
- ✅ CTF 竞赛和演练

**禁止用于**：
- ❌ 未授权的攻击测试
- ❌ 生产环境破坏性操作
- ❌ 恶意用途

## 贡献

欢迎提交 Issue 和 Pull Request！

## 许可证

本项目核心代码（`mozi` 脚本、工作区结构、配置文件等）采用 **Apache 2.0 License**，版权归 **Acczdy** 所有。

### 第三方 Skills 声明

本项目各工作区下的 `.claude/skills/` 目录中的 Skills 收集自互联网开源社区，其原始版权归属于各自的作者。每个 Skill 目录下包含独立的 `LICENSE` 文件，标明其各自的授权条款。本项目仅用于学习和研究目的， Skills 的原作者保留所有权利。

如果您认为某个 Skills 的收录侵犯了您的权益，请通过 [GitHub Issues](https://github.com/Acczdy/MoZiSec/issues) 联系我们，我们将及时处理。

## 相关链接

- [Claude Code](https://claude.ai/code)
- [fzf](https://github.com/junegunn/fzf)
- [Anthropic Cybersecurity Skills](https://github.com/mukul975/Anthropic-Cybersecurity-Skills)

---

**墨子曰：「兼相爱，交相利。」**
