# xhs-writer-skill 安装指南

本文档供 AI agent 自动安装使用。

---

## 安装步骤

### 1. Clone 仓库

```bash
git clone https://github.com/JuneYaooo/xhs-writer-skill.git ~/.claude/skills/xhs-writer-skill
```

### 2. 运行安装脚本

```bash
cd ~/.claude/skills/xhs-writer-skill
bash install_as_skill.sh
```

### 3. 验证安装

```bash
ls -la ~/.claude/skills/xhs-writer-skill/SKILL.md
```

如果文件存在，说明安装成功。

### 4. 重启 Claude Code

安装完成后，需要重启 Claude Code 才能识别新 skill。

---

## 可选：图生图功能

如果需要图生图功能（推荐），需要额外安装 gpt-image2-ppt-skills：

```bash
git clone https://github.com/JuneYaooo/gpt-image2-ppt-skills.git ~/.claude/skills/gpt-image2-ppt-skills
cd ~/.claude/skills/gpt-image2-ppt-skills
bash install_as_skill.sh
```

然后配置 OpenAI API key：

```bash
# 创建 .env 文件
cat > ~/.claude/skills/gpt-image2-ppt-skills/.env << 'EOF'
OPENAI_BASE_URL=https://api.openai.com
OPENAI_API_KEY=sk-your-key-here
GPT_IMAGE_MODEL_NAME=gpt-image-2
GPT_IMAGE_QUALITY=high
EOF
```

**重要**：需要向用户询问 OpenAI API key，不要自己编造。

---

## 使用方法

安装完成后，直接跟 Claude 说：

```
帮我推广这个项目 /path/to/project，做一套小红书卡片。
```

或

```
写一条关于 XX 的小红书笔记。
```

Claude 会自动调用 xhs-writer-skill 完成任务。

---

## 故障排查

### 问题1：skill 未识别

**解决**：重启 Claude Code

### 问题2：图生图失败

**原因**：未安装 gpt-image2-ppt-skills 或未配置 API key

**解决**：按上面"可选：图生图功能"步骤安装

### 问题3：权限错误

**解决**：
```bash
chmod +x ~/.claude/skills/xhs-writer-skill/install_as_skill.sh
chmod +x ~/.claude/skills/xhs-writer-skill/scripts/*.py
```

---

## 卸载

```bash
rm -rf ~/.claude/skills/xhs-writer-skill
```

然后重启 Claude Code。
