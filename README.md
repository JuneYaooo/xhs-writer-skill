<div align="center">

# xhs-writer-skill

**一句话喂主题，产出一套小红书竖版卡片 + caption + hashtags。**

Claude Code / OpenClaw Skill。装进 agent 后，从长文撰写 → 去 AI 化 → 拆分 3-9 张 3:4 卡片 → 生成 caption 和 hashtags 一条龙，按规范保存到 `output/小红书/`。

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](./LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-orange.svg)](https://www.anthropic.com/claude-code)
[![Platform](https://img.shields.io/badge/platform-小红书-ff2442.svg)](https://www.xiaohongshu.com/)
[![Zh](https://img.shields.io/badge/lang-zh--CN-red.svg)](./README.md)

</div>

---

## ✨ 能做什么

- 🧠 **长文 → 卡片的标准拆分** —— 先写 2000-4000 字原稿给你 review，再拆成 `cover` + `content × N` + `ending` 的 3-9 张卡片
- 🧹 **内置去 AI 化 playbook** —— 5 层 50+ 条规则（禁用词、句式、节奏、活人感），重写后给质量评分（满分 50）
- 🔎 **多轮多角度搜参考** —— 中英双语 + 至少 3 轮 + 交叉验证关键数据，原文抓取到 `reference/articles/`
- 🖼 **零水印配图纪律** —— 下载后多模态目视检查，Getty / 视觉中国 / IC 这类水印必须处理再用
- 🗂 **严格目录 & 命名规范** —— 时间戳 `YYYYMMDDHHmm`、特殊字符白名单、H2 起始不写 H1，产物长啥样一眼可预期
- 🧰 **纯 skill，无后端依赖** —— 只用 Claude 内置的 WebSearch / WebFetch / Bash / Read，装完就能跑

## 📦 产物长这样

```
output/小红书/2026-04-24/春季护肤攻略_202604241500/
├── 春季护肤全攻略_7个你没注意的细节.md   # 长文原稿（2000-4000 字）
├── meta.json                              # title / caption / hashtags / 7 张卡片的 title+content
├── images/                                # （可选）卡片配图
└── reference/
    ├── thinking.md
    ├── search_results.json
    ├── summary.md
    └── articles/
        └── ref_001_xxx.md
```

完整 schema 见 [`docs/meta-schema.md`](./docs/meta-schema.md)。

---

## 🚀 安装

### 方式一：让 AI 自己装（推荐）

把下面这段 prompt 丢给你的 AI 助手（Claude Code / OpenClaw / Codex / Cursor / Trae 都行），它会自动完成安装：

```
帮我安装 xhs-writer-skill：
https://raw.githubusercontent.com/JuneYaooo/xhs-writer-skill/main/docs/install.md
```

agent 会自己 clone 仓库、跑安装脚本、提示你重启。

### 方式二：手动安装

```bash
git clone git@github.com:JuneYaooo/xhs-writer-skill.git
cd xhs-writer-skill
bash install_as_skill.sh
```

脚本会把 skill 装到 `~/.claude/skills/xhs-writer-skill/`，Claude Code 重启后自动识别。

本 skill **不需要任何 API key** 就能跑（只用 Claude 内置工具）。如果你想接入 text-to-image 服务出卡片配图，在 `~/.claude/skills/xhs-writer-skill/.env` 里自己配（可选，见 `.env.example`）。

---

## 🛠 在 Claude Code 里怎么用

装完直接跟 Claude 说人话就行：

> 帮我用 **xhs-writer** 写一篇关于**春季护肤避坑**的小红书笔记，风格走简约清新，7 张卡片。

Claude 会：

1. 先问你目标读者 / 想表达的核心观点
2. 多轮搜参考（中英文、数据 / 观点 / 案例）
3. 写 2000-4000 字长文原稿让你 review
4. 按去 AI 化规则重写并给评分
5. 拆成 7 张卡片，每张 ≤80 字
6. 生成 100-300 字 caption + 5-8 个 hashtags
7. 把产物目录路径告诉你

> 🧑‍💻 想自己写脚本而不走 agent？看 [`SKILL.md`](./SKILL.md)，写作规范、拆卡规则、meta.json 结构都在那。

---

## 🎨 内置的卡片风格关键词

供写 prompt 时挑一套，保持全文风格统一：

| 风格 | 一句话定位 | 适用主题 |
|------|-----------|---------|
| 简约清新 | 大留白 + 莫兰迪色 | 护肤、生活方式、读书、情绪 |
| 科技感 | 深色 + 霓虹 + 几何线条 | AI、效率工具、程序员向 |
| ins 风 | 胶片感 + 高饱和 | 穿搭、美食、旅行 |
| 商务专业 | 蓝白灰 + 数据图 | 职场、投资、行业分析 |
| 文艺复古 | 米色 + 衬线字 + 植物插画 | 文化、诗词、手作 |
| 可爱卡通 | 糖果色 + 手绘 Q 版 | 亲子、萌宠、学生向 |

卡片比例**固定 3:4 竖版**，不建议改。

---

## 📁 文件结构

```
xhs-writer-skill/
├── SKILL.md                    # Claude Code skill 入口（权威文档）
├── AGENTS.md                   # codex / aider / cursor 的薄索引
├── README.md                   # 本文件
├── install_as_skill.sh         # 一键安装脚本
├── .env.example                # 可选：text-to-image 服务配置
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

---

## 🔗 相关仓库

- [wechat-article-skill](https://github.com/JuneYaooo/wechat-article-skill) —— 微信公众号长文 skill（姊妹仓库）

## 🙏 致谢

- 去 AI 化规则综合自维基百科「AI 写作特征」、学术降 AI 率实践、中文公众号写作研究（卡兹克写作法）
- 目录规范脱胎于内部写作系统 `记小兰` 的约定

## License

Apache License 2.0，详见 [LICENSE](./LICENSE)。
