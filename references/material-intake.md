# 素材驱动流程与卡片合成策略

当用户提供了已有素材(文字 / 图片 / 视频 / 音频)，笔记的写法要从"找参考 → 写原稿 → 拆卡"切换到"清点素材 → 从素材里提炼观点 → 反推卡片/分镜"。本文档给出素材处理的完整流程和四种卡片合成策略。

## 一、素材清点流程

### 1. 跑脚本生成 `materials.json`

```bash
python3 scripts/analyze_material.py <path>... \
  --out <work-dir>/reference/materials.json \
  --frames-dir <work-dir>/reference/frames \
  --frames-per-video 6
```

脚本做确定性的事：

- 按扩展名分类 `text / image / video / audio / unknown`
- 图片：读 `width / height / aspect`
- 视频：读 `duration_sec`、抽 N 帧存到 `frames-dir/`
- 文本：截取前 400 字预览
- 输出 `{ "materials": [...] }`，每条含空字段 `caption`、`usage`、`strategy`

### 2. AI 用多模态能力补全

打开 `materials.json`，对每条 image 和每个 video 的抽帧：

- 用 Read 工具**目视**查看
- 填 `caption`：10-40 字客观描述画面，不加情绪词
- 填 `usage`：这张图打算用在哪(`cover` / `content-3` / `ending` / `reference-only` / `discard`)
- 填 `strategy`：见本文第二节

视频类素材的判断基于抽帧；如果抽帧不足以判断，跑 `ffmpeg` 加密抽帧或让用户补充。

### 3. 从素材反推观点

素材清点完后，再写长文原稿。**不要先写原稿再硬塞素材**——那会退化成纯文字帖 + 配图。正确顺序：

1. 素材里最强的 1-2 张/段 → 决定 `cover` 钩子
2. 其余素材按 `usage` 串成叙事线 → 决定 content 卡顺序
3. 缺口(没有素材支撑的论点) → 由 `ai_generated` 或 `pure_text` 卡补

## 二、卡片合成策略(4 种)

每张卡片在 `meta.json.cards[i].synthesis_strategy` 里必须标明走哪条路径。

### 策略 A：`pure_text` — 纯文字卡

**适用**：没有合适素材，或这张卡就是金句/数据强表达。

**做法**：

- 交给用户侧 t2i 服务生图(给一个描述性 prompt)，或
- 用用户自备的小红书模板工具(Canva / 稿定 / 创客贴) 手工排，本 skill 不做渲染

如果走 AI 生图，prompt 写法：`minimalist poster, {配色}, large Chinese text "{卡片文字}", vertical 3:4, clean background, no watermark`。

### 策略 B：`text_on_photo` — 照片 + 文字叠加

**适用**：有一张构图干净的真实照片 + 一句强钩子(cover / ending 常用)。

**做法**：

```bash
python3 scripts/text_on_image.py <input.jpg> <output.jpg> \
  --text "一句钩子" \
  --position top \
  --fit 3x4 \
  --size 72 \
  --color "#FFFFFF" \
  --bg "#000000AA"
```

注意：

- `--position` = `top / center / bottom`；cover 推荐 top 或 center
- 文字长度 ≤18 字，更长先拆成两行(脚本已支持换行符 `\n`)
- 照片有水印先走 `crop_watermark.py`
- 字体走 `assets/fonts/` 下的 CJK 字体，缺字体脚本会警告并用 PIL 默认字体(不推荐上线)

### 策略 C：`collage` — 多图拼贴

**适用**：2-4 张素材互为补充(前后对比 / 多角度 / 步骤图)。

**做法**：

```bash
python3 scripts/collage_3x4.py <output.jpg> \
  --layout 2v --inputs a.jpg b.jpg \
  --gap 12 --bg "#FFFFFF" --size 900x1200
```

布局选择：

| layout | 素材数 | 排列 | 常用场景 |
|---|---|---|---|
| `2v` | 2 | 上下 | 对比 before/after |
| `2h` | 2 | 左右 | 窄图配对(慎用) |
| `3` | 3 | 大图在上 + 两小图 | 主图 + 细节 |
| `4` | 4 | 2×2 | 步骤 / 多角度 |

拼贴出来的卡若还要叠一句标题，再串一次 `text_on_image.py`。

### 策略 D：`ai_generated` — 概念图 / 数据图

**适用**：概念图、流程图、数据图、抽象示意——实拍拿不到的。

**做法**：本 skill 不绑定 t2i 服务；把 prompt 写进 `cards[i].image_prompt`，由用户调自己的 Nano Banana / Gemini Image / Flux / SD 生图，出图后把 URL / 本地路径填 `cards[i].image_url`。

prompt 要点：

- 明确比例：`vertical 3:4, 900x1200`
- 明确风格：`minimalist, flat design, clean`
- 避免真实人脸(肖像权)
- 数据图给具体数字，别让模型瞎编

## 三、图文 vs 视频的分流判断

Step 0 问完后按如下判断：

- 用户明确说"视频" / "短视频" / "reels" → 走 5B(视频帖)
- 用户素材里 ≥1 段可用视频(> 5 秒) → 默认建议 5B，但由用户拍板
- 其余情况 → 走 5A(图文帖)

视频帖仍然需要一张 `cover` 卡(3:4)作封面，合成策略同上。

## 四、materials.json 示例

```json
{
  "materials": [
    {
      "id": "m001",
      "path": "/Users/me/photos/spring_01.jpg",
      "kind": "image",
      "size_bytes": 2458912,
      "width": 4032, "height": 3024, "aspect": 1.333,
      "caption": "阳光下的樱花树近景，粉白色花瓣",
      "usage": "cover",
      "strategy": "text_on_photo"
    },
    {
      "id": "m002",
      "path": "/Users/me/videos/hike.mp4",
      "kind": "video",
      "duration_sec": 42.5,
      "width": 1080, "height": 1920,
      "frames": [
        "reference/frames/hike_f01.jpg",
        "reference/frames/hike_f02.jpg"
      ],
      "caption": "登山小径，从山脚视角仰拍山顶",
      "usage": "content-2",
      "strategy": "use_original"
    }
  ]
}
```

注：视频素材如果要原样用在视频帖分镜里，`strategy` 填 `use_original`；在图文帖里只作为 `reference-only` 或从某一帧派生 `text_on_photo`。
