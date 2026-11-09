# dsh-novelist-preset

DeepSeek Harness（DSH）的「网文主笔」Agent 预设包：按标准网文工业流程，从立项到连载一步步引导完成整部长篇网文。

## 包含内容

| 文件 | 说明 |
| --- | --- |
| `package.json` | bundle 声明（`@local/dsh-novelist-preset`） |
| `cordis.patch.yml` | 预设声明 + 完整系统提示词（网文主笔 persona：主编、主笔双重身份、九条铁律、新人五步引导、去AI味规范） |
| `skills/webnovel-guide/SKILL.md` | 分阶段构建引导：立项、定位策划、世界观、人物、总纲、卷纲、黄金开篇 |
| `skills/webnovel-serial/SKILL.md` | 单章连载流水线：一致性准备 → 细纲 → 正文 → 审查 → 修订 → 归档台账 |
| `skills/webnovel-audit/SKILL.md` | 一致性审计：伏笔账龄、时间线、人物状态、设定冲突、大纲偏差七维体检 |

## 安装方法

方式一（推荐，直接从 GitHub 装）：

```bash
dsh plugin --profile desktop add github:<你的用户名>/dsh-novelist-preset
```

方式二（本地链接）：

```bash
# 1. 克隆到任意位置（git clone https://github.com/<你的用户名>/dsh-novelist-preset.git）

# 2. 在 ~/.dsh/profiles/desktop/package.json 的 dsh.profile.bundles 中加入：
#    "@local/dsh-novelist-preset"
# 并在 dependencies 中加入（路径换成实际克隆位置）：
#    "@local/dsh-novelist-preset": "link:<克隆路径>/dsh-novelist-preset"

# 3. 在 profile 目录执行 pnpm install，然后重启 DSH
```

装好后，在 `~/.dsh/settings.yaml` 中设默认预设（可选）：

```yaml
agent-presets:
  default: novelist
```

或在 DSH 界面新建会话时选择「网文主笔」预设。

## 使用说明

预设启动后会按阶段推进：立项规格确认（体量、更新/完本周期等必须落到 `.story-system/project_spec.json`）→ 定位策划 → 世界观与人物 → 总纲卷纲 → 黄金三章 → 逐章连载流水线，每约 5 章自动做一致性审计。所有设定、大纲、正文、台账均落盘为项目文件，请为每本书使用独立的工作目录。

## 许可

自用分享，遵循仓库声明的 License。
