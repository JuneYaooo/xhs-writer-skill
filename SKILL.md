---
name: xhs-writer
description: Generate Xiaohongshu (RedNote) notes -- long-form draft → humanized rewrite → split into 3-9 vertical 3:4 cards (≤80 chars each) → caption (100-300 chars) + 5-8 hashtags, saved to output/小红书/. Use when the user asks to 写小红书 / 做小红书笔记 / 小红书图文 / 小红书种草 / 出一套 xhs 卡片 / make a RedNote post / write a Xiaohongshu note.
---

# xhs-writer -- 小红书笔记生成

把一个主题 / 一份素材，变成一套**小红书竖版卡片 + caption + hashtags**，按规范产物保存到 `output/小红书/`。

## 核心理念：小红书是卡片组，不是长文

读者最终看到的是 **3-9 张 3:4 竖版卡片**（每张 ≤80 字中文）。长文原稿只是写作和拆分的中间产物，caption 只是发布时的补充说明。

## 调用规范（对 agent 的硬约束）

当用户说"写小红书笔记 / 做一套小红书卡片"时：

1. **先问三件事**（不要直接动手）：
   - 主题 / 目标读者 / 想表达的核心观点？
   - 卡片风格偏好？（简约清新 / 科技感 / ins 风 / 商务专业 / 文艺复古 / 可爱卡通）
   - 卡片数量预期？（3-9 张，默认 5-7 张）
2. **先搜参考**：按 `resources/reference-search.md` 采集资料到 `reference/`
3. **先写长文原稿（2000-4000 字）并让用户确认核心观点**，不直接跳到卡片
4. **全文去 AI 化**：按 `resources/humanizer-zh.md` 对原稿润色
5. **拆卡**：遵守 3-9 张、每张 ≤80 字、`cover` + `content` × N + `ending` 的结构
6. **生成 caption + hashtags**（caption 100-300 字含 emoji；hashtags 5-8 个）
7. **保存产物**：按 `docs/output-spec.md` 的目录规范

## 写作流程

### 1. 理解需求 & 搜集参考

读 [`resources/reference-search.md`](resources/reference-search.md)。

- 从用户输入提炼关键词
- 多角度多轮搜（中英文、事实 / 观点 / 数据），至少 3 轮
- 交叉验证关键数据
- 结果保存到该文章目录的 `reference/` 子目录

### 2. 撰写长文原稿（2000-4000 字）

写作规范：

- **结构**：H2 起始（**不写 H1**，标题放 `meta.json`），3-5 个章节
- **语气**：第一人称（我觉得 / 我看到 / 我发现）、口语化、具体数据、逻辑转折
- **段落长度**：2-10 句不等，不要整齐
- **避免**：首先 / 其次 / 综上所述、不仅……更……、通过……来……
- **推荐**：疑问句、感叹号、具体例子、个人感受、碎片化叙事

把原稿写成 `.md` 文件，**先给用户看**，确认核心论点没跑偏再进下一步。

### 3. 去 AI 化润色

读 [`resources/humanizer-zh.md`](resources/humanizer-zh.md) 的五层原则，对原稿做一遍完整扫描重写。输出时给出**质量评分**（总分 50）。

### 4. 拆分成卡片

**卡片结构**：

| 位置 | type | 内容 |
|------|------|------|
| 第 1 张 | `cover` | 标题 + 核心卖点钩子 |
| 第 2 至 N-1 张 | `content` | 小标题 + 正文要点 |
| 最后 1 张 | `ending` | 感谢阅读 / 行动号召 |

**拆分原则**：

- 每张卡片一个独立论点，不把一个论点拆到两张卡
- 数字、对比、金句优先上卡
- 80 字是硬上限（`title` + `content` 合计）
- 保持全文风格统一（卡片间用同一套配色 / 版式 / emoji 风格）

### 5. 生成 caption + hashtags

**Caption（100-300 字）**：

- 开头用 hook（数字、提问、惊叹）
- 中间包含关键信息点
- 结尾行动号召（点赞、收藏、关注）
- 语气：闺蜜聊天 / 种草分享 / 干货总结
- 含 emoji

**Hashtags**（5-8 个）：与主题强相关、避免过度泛化（`#生活` 这种少用）。

**caption 和 hashtags 只放在 `meta.json`，不混入卡片内容。**

### 6.（可选）卡片配图

读 [`resources/image-sourcing.md`](resources/image-sourcing.md)。

本 skill 不绑定任何生图 API。如果用户有 text-to-image 服务（Nano Banana / Gemini Image / Flux / SD），按下面给 prompt：

- **封面卡**：大标题 + 视觉焦点元素，配色强烈
- **内容卡**：数据可视化 / 场景插画 / 概念图，与文字呼应
- **结尾卡**：emoji + 留白 + 行动号召视觉
- **比例**：3:4（竖版）

图片 URL 填入 `meta.json.cards[i].image_url`，同时下载到本地 `images/`。

## 输出目录（见 `docs/output-spec.md`）

```
output/小红书/{YYYY-MM-DD}/{短标题}_{YYYYMMDDHHmm}/
├── {文章完整标题}.md          # 长文原稿
├── meta.json                  # 核心：title / caption / hashtags / cards
├── images/                    # （可选）卡片配图
└── reference/                 # 搜索结果、参考资料、思考过程
```

**文件命名规则**（见 `docs/output-spec.md`）：

- 目录名 ≤15 字精简短标题
- 特殊字符 `：:？?！!""''/\*<>|……——` → `_`
- 空格 → `_`，连续 `_` 合并为单个
- 时间戳 `YYYYMMDDHHmm`（Asia/Shanghai）

## meta.json 必含字段

```json
{
  "title": "💼 AI 失业危机？数据告诉你真实情况",
  "platform": "小红书",
  "created_at": "2026-04-24T15:00:00+08:00",
  "caption": "媒体说危机，数据说增长。Anthropic 调研 5000 人，83% 说 AI 提高效率而非取代……",
  "hashtags": ["#AI对工作的影响", "#失业危机是假的"],
  "poster_style": "简约清新",
  "card_count": 7,
  "cards": [
    {
      "page": 1,
      "type": "cover",
      "title": "💼 AI 失业危机？数据告诉你真实情况",
      "content": "媒体说危机，数据说增长\n美国失业率：4.2%→3.8%\n看完这 7 页，重新理解 AI 时代"
    },
    {
      "page": 2,
      "type": "content",
      "title": "🚨 被夸大的危机",
      "content": "Anthropic 调研 5000 人\n83%：AI 提高效率，非取代\n失业率↓ 不是↑"
    },
    {
      "page": 7,
      "type": "ending",
      "content": "抗拒 AI = 危险\n拥抱 AI = 机遇\n薪资看涨 岗位增多\n变化中的赢家，从现在开始 👍"
    }
  ],
  "images": [],
  "references": {
    "search_queries": ["AI 就业影响 2026"],
    "source_count": 6,
    "summary_file": "reference/summary.md"
  }
}
```

完整 schema 见 [`docs/meta-schema.md`](docs/meta-schema.md)。

## 不要做的事

- ❌ 不写 H1 标题（标题统一放 `meta.json.title`）
- ❌ 不在正文末尾写"参考来源 / 参考文献 / References"（只保存到 `reference/`）
- ❌ 不在 `.md` 里留 `【插入图片：...】` 占位符
- ❌ caption 超过 300 字 / 卡片单张超过 80 字
- ❌ 跳过去 AI 化直接给用户

## 文件结构

```
xhs-writer-skill/
├── SKILL.md                    # 本文件（Claude Code skill 入口）
├── AGENTS.md                   # codex / aider / cursor 的薄索引
├── README.md                   # 项目说明
├── install_as_skill.sh         # 一键安装到 ~/.claude/skills/
├── .env.example
├── docs/
│   ├── install.md              # 给 AI agent 自动安装读的指引
│   ├── output-spec.md          # 目录与命名规范
│   └── meta-schema.md          # meta.json 字段定义
├── resources/
│   ├── humanizer-zh.md         # 去 AI 化完整指南（5 层原则 + 评分）
│   ├── reference-search.md     # 参考资料采集 playbook
│   └── image-sourcing.md       # 配图搜索 / 版权检查 / 水印处理
└── agents/
    └── openclaw.yaml
```
