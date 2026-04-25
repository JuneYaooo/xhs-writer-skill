<div align="center">

# xhs-writer-skill

**小红书爆款笔记生成器 — 从主题到发布，一条龙。**

Claude Code / OpenClaw Skill。装进 agent 后，用一句自然语言生成 9:16 竖版卡片组（图文）或短视频脚本，配 caption + hashtags，按规范落盘。支持真实素材图生图、卖点分析、5种标题公式。

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](./LICENSE)
[![Python](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-orange.svg)](https://www.anthropic.com/claude-code)

</div>

---

## 🎬 效果演示：真实案例

**案例**：推广 gpt-image2-ppt-skills 开源项目

<table>
<tr>
<th width="50%">输入：GitHub 项目原图</th>
<th width="50%">输出：小红书风格卡片（9:16 竖版）</th>
</tr>
<tr>
<td><img src="docs/assets/input-github-screenshot.jpg" width="100%" alt="GitHub 原图：模板演示"></td>
<td><img src="docs/assets/demo-comparison.jpg" width="100%" alt="小红书卡片：对比图"></td>
</tr>
<tr>
<td align="center"><sub>GitHub 项目截图（原始素材）</sub></td>
<td align="center"><sub>卡片2：对比图（图生图）<br/>保留真实对比效果 + 小红书风格文字</sub></td>
</tr>
<tr>
<td><img src="docs/assets/input-style-gallery.jpg" width="100%" alt="GitHub 原图：10种风格"></td>
<td><img src="docs/assets/demo-styles.jpg" width="100%" alt="小红书卡片：风格展示"></td>
</tr>
<tr>
<td align="center"><sub>GitHub 项目截图（10种风格展示）</sub></td>
<td align="center"><sub>卡片3：风格展示（图生图）<br/>保留真实素材 + 素人感设计</sub></td>
</tr>
</table>

**完整案例**：
- 项目：[gpt-image2-ppt-skills](https://github.com/JuneYaooo/gpt-image2-ppt-skills)
- 输入：GitHub 项目截图、README 图片、演示图
- 输出：7张卡片（封面、对比图、风格展示、谁需要、真实案例、CTA、GitHub）
- 方法：卖点分析（模板克隆 125分 > 10种风格 18分）→ 聚焦核心卖点
- 处理：图生图保留真实素材 + 叠加小红书风格文字（素人感）
- 标题：5个选项（痛点式、提问式、发现式、热点词、身份共鸣）

---

## ✨ 能做什么

- 🎯 **卖点/亮点分析** — 用公式（稀缺性×实用性×可感知）自动判断优先级，聚焦核心卖点
- 🖼️ **真实素材优先** — 项目截图、对比图用图生图（gpt-image-2），保留真实感 > 纯 AI 生图
- 🎨 **素人感设计** — 纯色背景 + 大 emoji + 口语化，不是品牌宣传风格
- 📝 **5种标题公式** — 痛点式、提问式、发现式、热点词、身份共鸣，自动生成选项
- 🔄 **快速迭代** — V1(60分) → 用户反馈 → V2(70分)，持续优化
- 📦 **完整交付** — 6-7张卡片 + caption + hashtags + 发布方案

## 📱 适用场景

- ✅ **产品推广** — 开源项目、SaaS 工具、App 应用
- ✅ **好物分享** — 数码产品、生活好物、美妆护肤
- ✅ **知识科普** — 技术教程、生活技巧、行业知识
- ✅ **经验总结** — 职场经验、学习方法、成长心得
- ✅ **测评对比** — 产品对比、使用体验、效果评测

---

## 🚀 安装

### 方式一：让 AI 自己装（推荐）

把下面这段 prompt 丢给你的 AI 助手（Claude Code / OpenClaw / Codex / Cursor 都行），它会自动完成安装：

```
帮我安装 xhs-writer-skill：
https://raw.githubusercontent.com/JuneYaooo/xhs-writer-skill/main/docs/install.md
```

agent 会自己 clone 仓库、跑安装脚本、提示你重启。

### 方式二：手动安装

```bash
git clone https://github.com/JuneYaooo/xhs-writer-skill.git
cd xhs-writer-skill
bash install_as_skill.sh
```

脚本会把 skill 装到 `~/.claude/skills/xhs-writer-skill/`，Claude Code 重启后自动识别。

### 配置（可选）

如果需要图生图功能（推荐），配置 OpenAI API key：

```bash
# 创建 .env 文件
cat > ~/.claude/skills/xhs-writer-skill/.env << 'EOF'
OPENAI_BASE_URL=https://api.openai.com
OPENAI_API_KEY=sk-your-key-here
GPT_IMAGE_MODEL_NAME=gpt-image-2
GPT_IMAGE_QUALITY=high
EOF
```

> 🔒 **不会误吃密钥**：只从 skill 自己目录的 `.env` 加载。
>
> 💡 **图生图 vs 纯文字卡片**：有真实素材时，图生图效果远超纯 AI 生图（保留真实感）。没有 API key 也能用，会生成纯文字卡片。

---

## 🛠 在 Claude Code 里怎么用

装完直接跟 Claude 说人话就行：

### 场景1：推广产品/项目（推荐配合 social-account-doctor）

> 先帮我用 social-account-doctor 找一下同类产品的爆款笔记，然后参考它们的结构，帮我推广这个项目 `/path/to/project`。

Claude 会：
1. **找对标**：搜索同类产品的爆款笔记
2. **拆爆款**：分析标题模式、卡片结构、钩子套路
3. **扫描素材**：你的项目截图、对比图、演示图
4. **卖点分析**：让你打分（稀缺性×实用性×可感知）
5. **套公式**：用爆款的结构 + 你的真实素材
6. **输出**：6-7 张卡片 + caption + hashtags

### 场景2：快速生成（不分析对标）

> 帮我推广这个项目 `/path/to/project`，做一套小红书卡片。

Claude 会：
1. 扫描项目素材（截图、对比图、演示图）
2. 卖点分析（让你打分：稀缺性×实用性×可感知）
3. 生成 5 个标题选项（痛点式、提问式等）
4. 用图生图处理真实素材
5. 输出 6-7 张卡片 + caption + hashtags

### 场景3：好物分享

> 写一条关于 XX 好物推荐的小红书笔记。

Claude 会：
1. 确认亮点（这个好物的核心优势是什么）
2. 素材盘点（产品图、使用场景图）
3. 生成卡片（封面 + 亮点展示 + 使用场景 + 真实体验 + CTA）

### 场景4：知识科普

> 写一条关于 XX 知识的小红书笔记。

Claude 会：
1. 采集外部参考（搜索资料，交叉验证）
2. 写长文原稿 + 去 AI 化
3. 拆成卡片（封面 + 核心概念 + 步骤/要点 + 注意事项 + CTA）

> 🧑‍💻 想自己写脚本调 CLI 而不走 agent？看 [`SKILL.md`](./SKILL.md)，完整工作流程都在那。

---

## 📊 方法论框架

### 5大核心原则

1. **真实素材优先** — 项目截图、对比图、演示图 > 纯 AI 生图
2. **聚焦核心卖点** — 用公式判断优先级（稀缺性×实用性×可感知）
3. **素人感设计** — 纯色背景 + 大 emoji + 口语化 > 品牌宣传风格
4. **痛点导向** — 说"能解决什么问题" > 堆砌功能列表
5. **快速迭代** — V1(60分) → 用户反馈 → V2(70分) → 持续优化

### 5种标题公式

| 公式 | 格式 | 适用场景 |
|---|---|---|
| 痛点式 | `{具体痛点}？{解决方案}` | 有明确痛点的工具/产品 |
| 提问式 | `有没有那种{功能描述}的{产品类型}？` | 新工具推荐、功能发现 |
| 发现式 | `我发现了个宝藏！{核心价值}` | 兴奋分享、好物推荐 |
| 热点词 | `{热点词}爆火后，我用它做了{场景}` | 蹭热点、技术类产品 |
| 身份共鸣 | `{身份标签}必备！{核心功能}` | 有明确目标人群的产品 |

### 工作流程建议

**推荐流程**（结合 [social-account-doctor](https://github.com/JuneYaooo/social-account-doctor)）：

```
Step 1: 找对标爆款
→ 用 social-account-doctor 搜索同类内容
→ 分析爆款的钩子、结构、封面风格

Step 2: 拆解爆款
→ 提取可复用的公式（标题模式、卡片结构、文案套路）
→ 不是照搬，是学习底层逻辑

Step 3: 套自己的内容
→ 用 xhs-writer-skill 生成初稿
→ 应用从爆款学到的公式
→ 保留自己的真实素材和独特卖点

Step 4: 快速迭代
→ V1 发布 → 看数据 → 优化 → V2
```

**卡片结构**：不固定模板，根据对标爆款和内容类型灵活调整。

常见结构参考：
- 推广类：封面 + 核心卖点 + 功能展示 + 目标人群 + 真实案例 + CTA + 链接
- 好物类：封面 + 产品亮点 + 使用场景 + 效果对比 + 购买建议 + CTA
- 知识类：封面 + 核心概念 + 步骤拆解 + 注意事项 + 总结 + CTA

详细方法论见：[`references/xiaohongshu-viral-methodology.md`](./references/xiaohongshu-viral-methodology.md)

---

## 📁 输出结构

```
output/小红书/{YYYY-MM-DD}/{短标题}_{YYYYMMDDHHmm}/
├── {完整标题}.md        # 长文原稿
├── meta.json            # 元数据(卡片/caption/hashtags/materials)
├── images/              # 最终卡图（9:16 竖版）
└── reference/           # materials.json / 搜索结果 / 思考过程
```

---

## 💬 Community

[**LINUX DO — 中文开发者社区**](https://linux.do/)

---

## License

Apache License 2.0，详见 [LICENSE](./LICENSE)。
