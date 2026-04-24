# xhs-writer-skill 安装指南（给 AI agent 读）

> 本文档是**让 AI 助手自动完成安装**的可执行指引。Claude Code / OpenClaw / Codex / Cursor / Trae / Hermes Agent 等任一 agent 按以下步骤执行即可。
>
> 人类用户请**不要**手动照抄下面的步骤，直接把这份 URL 扔给你的 AI 助手，它自己会跑通。

## 项目一句话介绍

`xhs-writer-skill` 是一个 Claude Code / OpenClaw Skill，把一个主题变成一套小红书竖版卡片（3-9 张 3:4）+ caption + hashtags，按规范保存到 `output/小红书/`。

仓库：https://github.com/JuneYaooo/xhs-writer-skill

## 前置依赖（agent 请先检查）

- **必需**：`git`、`bash`、`rsync`
- **可选**：`python3`（仅 `image-sourcing` 里用 PIL 裁剪水印时才需要）

## 安装步骤

### 1. 克隆仓库到临时目录

```bash
git clone https://github.com/JuneYaooo/xhs-writer-skill.git /tmp/xhs-writer-skill
cd /tmp/xhs-writer-skill
```

### 2. 跑安装脚本

```bash
bash install_as_skill.sh
```

这一步会：

- 把项目文件拷贝到 `~/.claude/skills/xhs-writer-skill/`
- 从 `.env.example` 生成 `~/.claude/skills/xhs-writer-skill/.env` 骨架（可选，留空也能用）

安装脚本**交互式**，目标目录已存在时会问是否覆盖；agent 可以用 `yes | bash install_as_skill.sh` 自动选"是"，或先检查目录是否存在再决定。

### 3. 告知用户 skill 已可用

本 skill **写作流程无需 API key**，装完直接可用。

如果用户想接入 text-to-image 服务自动给卡片出配图，可 **主动问用户** 是否需要配：

> 你想接入一个 text-to-image 服务（比如 Nano Banana、Gemini Image、Flux）自动给卡片出 3:4 竖版配图吗？如果要，请提供 `base_url` + `api_key` + 模型名，我写进 skill 目录的 `.env`。不配也完全可以，卡片会只输出文字方案。

用户同意后写入 `~/.claude/skills/xhs-writer-skill/.env`：

```bash
T2I_BASE_URL=<用户提供>
T2I_API_KEY=<用户提供>
T2I_MODEL_NAME=<用户提供>
T2I_ASPECT_RATIO=3:4
```

### 4. 提示用户重启 agent

装完之后告诉用户：

> 已装到 `~/.claude/skills/xhs-writer-skill/`。请**重启 Claude Code**（或你正在用的 agent）让 skill 生效。

### 5.（可选）清理临时目录

```bash
rm -rf /tmp/xhs-writer-skill
```

## 冒烟测试（用户重启 agent 后）

告诉用户直接跟 agent 说：

> 帮我用 **xhs-writer** 写一条小红书笔记，主题「猫为什么喜欢盒子」，5 张卡片。

正常的话 agent 会：先问目标读者 / 风格偏好 → 搜参考 → 写长文 → 去 AI 化 → 拆卡 → 生成 caption + hashtags → 给出 `output/小红书/...` 的产物路径。

## 常见问题（给 agent 参考）

- **Claude Code 识别不到 skill** → 确认目录是 `~/.claude/skills/xhs-writer-skill/`（不是其它路径），完全重启过 Claude Code
- **用户说"想自动出配图"** → 回到步骤 3，问用户要 text-to-image 服务凭证
- **用户问"能不能接微博 / 抖音"** → 本 skill 只做小红书；公众号走姊妹仓库 `wechat-article-skill`

## 完成标志

以下两条都满足即视为安装成功：

1. `~/.claude/skills/xhs-writer-skill/SKILL.md` 存在
2. agent 重启后，用户用自然语言要求写小红书时能触发本 skill

装完不用逐字读 `SKILL.md`，但需要告诉用户："你可以直接用自然语言要小红书笔记，skill 会先问你风格和卡片数量，再逐步产出。"
