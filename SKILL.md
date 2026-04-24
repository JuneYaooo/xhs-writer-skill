---
name: xhs-writer
description: >-
  生成小红书(RedNote)笔记：把主题或用户提供的素材(文字/图片/视频)做成一套竖版卡片(图文)
  或一段带分镜脚本的视频稿(视频)，配 caption + hashtags，落盘到 output/小红书/。
  使用时机：用户说 写小红书 / 做小红书笔记 / 小红书图文 / 小红书种草 / 出一套 xhs 卡片 /
  小红书视频 / write a Xiaohongshu note / make a RedNote post。
---

# xhs-writer — 小红书笔记生成

把一个主题 + (可选)用户素材，生成**图文卡片组**或**短视频脚本**，按规范落盘到 `output/小红书/`。

## 核心理念

小红书读者看的不是长文，是**卡片组**或**短视频**。长文原稿只是中间产物；caption 只是发布配文。

- **图文帖(image post)**：3-9 张 3:4 竖版卡片(cover + content × N + ending)，每张 ≤80 字中文。
- **视频帖(video post)**：一段 15-90 秒竖屏视频脚本 + 封面卡，分镜写在 `meta.json.shots` 里，视频合成本 skill 不做。

## 工作流

按顺序执行。**不要跳步**。

### Step 0 — Intake(必问，一次问完)

收到请求后不要动手，先向用户确认以下要点，尽量一条消息问完：

1. **主题 / 目标读者 / 核心观点**
2. **输出形态**：图文 or 视频？(默认图文)
3. **素材**：有没有已有的文字/图片/视频要用？贴路径或拖文件
4. **风格**：简约清新 / 科技感 / ins 风 / 商务 / 文艺复古 / 可爱卡通(默认"简约清新")
5. **卡片数量 / 视频时长**：图文默认 5-7 张；视频默认 30-60 秒

### Step 1 — 素材清点(有素材才做)

如果用户提供了素材路径，**先跑脚本生成清单**，再由 AI 用多模态能力填描述：

```bash
python3 scripts/analyze_material.py <path>... \
  --out <work-dir>/reference/materials.json \
  --frames-dir <work-dir>/reference/frames
```

脚本只做确定性预处理(分类、取分辨率/时长、抽帧)。AI 随后用视觉能力 **打开** `materials.json` 里每条 image/frame，填 `caption` 和 `usage`(cover / content-N / ending / reference)。详见 [`references/material-intake.md`](references/material-intake.md)。

### Step 2 — 采集外部参考(观点类 / 资讯类必做；纯素材驱动可跳过)

按 [`references/reference-search.md`](references/reference-search.md) 执行，结果写入同一 `reference/` 目录。核心数据 ≥2 个来源交叉验证。

### Step 3 — 写长文原稿(2000-4000 字)

从 H2 开始(**不写 H1**；标题入 `meta.json.title`)。写完**先给用户看**，确认主旨再继续，不要直接跳到卡片/分镜。反模式清单以 [`references/humanizer-zh.md`](references/humanizer-zh.md) 为准。

### Step 4 — 去 AI 化

读 [`references/humanizer-zh.md`](references/humanizer-zh.md) 五层原则，完整扫描重写原稿，给出质量评分(满分 50)。

### Step 5 — 分发：图文 or 视频

#### 5A. 图文帖：拆成 3-9 张卡片

- 结构：`cover`(第 1 张) + `content`(中间若干) + `ending`(最后 1 张)
- 每张 ≤80 字(`title` + `content` 合计，代码点计数)
- 一张卡只讲一个论点；数字 / 对比 / 金句优先上卡
- 全套卡片 emoji 风格与配色保持一致

每张卡选一种 **合成策略**(写入 `cards[i].synthesis_strategy`)，四选一。详见 [`references/material-intake.md`](references/material-intake.md)：

| strategy | 适用 | 工具 |
|---|---|---|
| `pure_text` | 无素材，纯文字卡 | AI 生图 或 模板 |
| `text_on_photo` | 有 1 张合适照片 + 一句钩子 | `scripts/text_on_image.py` |
| `collage` | 有 2-4 张互补照片 | `scripts/collage_3x4.py` |
| `ai_generated` | 概念图 / 数据图 | 用户自备 t2i 服务 |

素材有水印 → 先跑 `scripts/crop_watermark.py` 或按 [`references/image-sourcing.md`](references/image-sourcing.md) 处理。

#### 5B. 视频帖：写分镜脚本

- 写 6-12 个分镜，每镜 2-8 秒，累计时长对齐用户预期
- 每镜含：`narration`(口播，≤30 字)、`on_screen_text`(屏幕字，≤15 字)、`visual`(画面描述)、`material_ref`(若引用 `materials.json` 里某条素材)
- 仍要出一张 `cover` 卡(3:4)作为封面；视频本体由用户侧工具合成，本 skill 只产脚本

字段结构见 [`references/meta-schema.md`](references/meta-schema.md) 的 `shots[]` 定义。

### Step 6 — caption + hashtags

- **caption**：100-300 字，hook 开头(数字/提问/惊叹) → 关键信息 → 行动号召(点赞/收藏/关注)，带 emoji，闺蜜语气
- **hashtags**：5-8 个，与主题强相关；避免 `#生活` 等过度泛化标签
- 只写进 `meta.json`，**不**粘进卡片或正文

### Step 7 — 落盘

目录与命名规则见 [`references/output-spec.md`](references/output-spec.md)。

```
output/小红书/{YYYY-MM-DD}/{短标题}_{YYYYMMDDHHmm}/
├── {完整标题}.md        # 长文原稿
├── meta.json            # 元数据(卡片 / 分镜 / caption / hashtags / materials)
├── images/              # (图文)最终卡图 / (视频)封面
└── reference/           # materials.json / 搜索结果 / summary / 思考过程
```

目录短标题与时间戳**必须**走脚本标准化，别手写：

```bash
python3 scripts/normalize_slug.py "原始长标题" --with-ts
```

`meta.json` 完整字段定义见 [`references/meta-schema.md`](references/meta-schema.md)。

### Step 8 — 校验(强制)

写完 `meta.json` 后必须跑：

```bash
python3 scripts/validate_meta.py <work-dir>/meta.json
```

非 0 退出 → 读报错修 `meta.json` 再跑，直到 clean。不要把未校验的产物交给用户。

## 不要做的事

- 不写 H1；标题只放 `meta.json.title`
- 不在正文末尾写"参考来源 / References"；只落到 `reference/`
- 不在 `.md` 里留 `【插入图片：...】` 占位符；图片同步下载 + 引用相对路径
- 不跳过 Step 4(去 AI 化)和 Step 8(validate)
- 不自己手算目录名 / 时间戳，一律走 `normalize_slug.py`
