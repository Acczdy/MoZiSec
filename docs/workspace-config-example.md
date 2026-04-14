# 示例工作区配置

本文档演示如何自定义工作区的分组、别名和名言。

## 方法 1：在 CLAUDE.md 中配置（推荐）

创建工作区时，在 `CLAUDE.md` 文件开头添加 HTML 注释：

```markdown
# 我的工作区名称
<!-- group: 专项领域 -->
<!-- alias: myw -->
<!-- quote: 我的自定义名言 -->

## 启动方式

```bash
cd my-workspace
claude
```

## 角色定义

在此定义该工作区的角色定位和专业领域。
```

### 配置说明

| 配置项 | 格式 | 说明 | 示例 |
|--------|------|------|------|
| `group` | `<!-- group: 分组名 -->` | 指定工作区所属分组 | `<!-- group: 核心安全 -->` |
| `alias` | `<!-- alias: 别名 -->` | 快捷访问别名 | `<!-- alias: js -->` |
| `quote` | `<!-- quote: 名言 -->` | 墨子名言 | `<!-- quote: 知己知彼 -->` |

### 可用分组名称

- `核心安全`
- `威胁防御`
- `安全运营`
- `专项领域`（默认）
- `取证分析`
- `网络攻击面`
- `文档管理`
- **自定义分组名**（会自动添加到菜单）

## 方法 2：修改 mozi 脚本

编辑 `mozi` 脚本第 54-62 行，添加默认分组映射：

```bash
declare -A DEFAULT_GROUPS=(
    ["js-security"]="核心安全"
    ["my-tool"]="我的自定义分组"  # 新增
)
```

## 完整示例

### 示例 1：创建渗透测试工具工作区

```bash
# 创建目录结构
mkdir -p pentest-tools/.claude/skills
mkdir -p pentest-tools/tmp

# 创建 CLAUDE.md
cat > pentest-tools/CLAUDE.md << 'EOF'
# 渗透测试工具集
<!-- group: 核心安全 -->
<!-- alias: pentest -->
<!-- quote: 知己知彼，百战不殆 -->

## 启动方式

```bash
cd pentest-tools
claude
```

## 角色定义

本工作区提供渗透测试相关的工具和方法论。
EOF
```

### 示例 2：创建代码审计工作区

```bash
mkdir -p code-audit/.claude/skills
mkdir -p code-audit/tmp

cat > code-audit/CLAUDE.md << 'EOF'
# 代码审计
<!-- group: 专项领域 -->
<!-- alias: audit -->
<!-- quote: 明察秋毫，见微知著 -->

## 启动方式

cd code-audit
claude
EOF
```

## 验证配置

创建工作区后，运行以下命令验证：

```bash
# 启动 mozi
./mozi

# 或使用命令直接进入
./mozi pentest-tools
```

在分组导航中应该能看到新工作区已归类到指定分组。

## 故障排除

### 问题 1：分组不显示

**原因**：分组名不在 `MOZI_GROUPS` 列表中

**解决**：编辑 `mozi` 第 74 行，添加自定义分组：

```bash
declare -a MOZI_GROUPS=("核心安全" "威胁防御" "安全运营" "专项领域" "取证分析" "网络攻击面" "文档管理" "我的自定义分组")
```

### 问题 2：配置不生效

**原因**：HTML 注释格式不正确

**解决**：确保格式正确，无多余空格：

```markdown
<!-- group: 专项领域 -->
```

不是：
```markdown
< !-- group: 专项领域 -- >  <!-- 错误 -->
```

## 相关文件

- `mozi` - 主脚本（第 54-62 行：DEFAULT_GROUPS，第 74 行：MOZI_GROUPS）
- `README.md` - 说明文档
- `<workspace>/CLAUDE.md` - 工作区配置

---

**墨子曰：「兼相爱，交相利。」**
