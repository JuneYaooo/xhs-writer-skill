# AGENTS.md -- 给 codex / aider / cursor 等 agent 的入口说明

本仓库是一个小红书笔记生成 skill，**权威文档在 [`SKILL.md`](./SKILL.md)**。任何涉及"写小红书 / 做小红书笔记 / 拆卡片 / 生成 caption"的请求，都先完整读 `SKILL.md` 再动手，不要凭本文件的摘要就开跑 —— 下面只是索引。

## 一分钟索引

- **主入口**：读 [`SKILL.md`](./SKILL.md) 的「调用规范」和「写作流程」章节
- **产物目录规范**：[`docs/output-spec.md`](./docs/output-spec.md)
- **meta.json 字段**：[`docs/meta-schema.md`](./docs/meta-schema.md)
- **去 AI 化规则**：[`resources/humanizer-zh.md`](./resources/humanizer-zh.md)（5 层原则 + 质量评分）
- **参考搜集 playbook**：[`resources/reference-search.md`](./resources/reference-search.md)
- **配图 + 版权**：[`resources/image-sourcing.md`](./resources/image-sourcing.md)

## 调用规范（对 agent 的硬约束）

1. **先问三件事**：主题 / 目标读者、风格偏好（简约 / 科技 / ins / 商务 / 文艺 / 可爱）、卡片数量（3-9 张）
2. **永远先写长文原稿**（2000-4000 字）并让用户 review，不要跳过去直接给卡片
3. **永远做去 AI 化并输出评分**（按 humanizer-zh 的 5 层 + 总分 50）
4. **拆卡硬约束**：`cover` + `content × N` + `ending`，每张 ≤80 字中文
5. **caption 和 hashtags 只放 `meta.json`，不混进卡片内容**
6. **文件命名严格按 `docs/output-spec.md`**（时间戳 / 特殊字符替换 / 目录名 ≤15 字）

## 凭据

本 skill 默认**无需任何 API key**。可选接入 text-to-image 服务时从 `~/.claude/skills/xhs-writer-skill/.env` 读取，不向上递归读项目 `.env`。

## 不要做的事

- 不要跳过长文原稿直接出卡片
- 不要在 `.md` 里写 H1 标题（标题放 `meta.json.title`）
- 不要在正文末尾堆"参考来源 / References"（只保存到 `reference/`）
- 不要在 `.md` 里留 `【插入图片：...】` 占位符
- 不要让 caption 超过 300 字或让单张卡片超过 80 字

## 同步提醒

本文件是薄索引。如果 `SKILL.md` 有新增章节或流程变更，只在 `SKILL.md` 里维护，本文件的锚点列表按需补；**正文描述不要复制到这里**，避免两边漂移。
