---
name: voice-clone-narrator
description: 使用 MiniMax API 在 Codex 中完成音色克隆和克隆音色口播生成。用户提供 60-120 秒干净人声录音、可选 5-30 秒示例音频和口播文稿，skill 自动上传录音、ASR 转录示例音频、等待用户校对、创建 voice_id，并用 voice_id 按逐句情绪和断句停顿生成 narration.mp3。适用于“克隆我的声音”“复刻我的音色”“用我的声音生成口播”“AI 配音”“生成带停顿的口播”等场景。
---

# voice-clone-narrator

> 在 Codex 里用 MiniMax 克隆自己的音色，并用克隆音色生成口播。

## 适用场景

- “请使用这个技能克隆我的音色”
- “用我的声音生成这段口播”
- “根据我的文稿生成 AI 配音”
- “帮我创建 voice_id”

## 普通用户流程

1. 安装这个 skill。
2. 注册并登录 MiniMax。
3. 点击 API 进入控制台，完成充值并生成 API 密钥。
4. 录制一段 60-120 秒的干净声音备用。
5. 打开 Codex，在对话框里选中这个 skill。
6. 上传录音和文稿，并说明：“请使用这个技能克隆我的音色。”
7. Codex 提示需要 MiniMax API Key 时，从 MiniMax 控制台复制密钥发给 Codex。
8. skill 会生成你的 `voice_id`。
9. 后续上传新的配音稿件，就可以用这个 `voice_id` 生成克隆音色口播。

## 输入

### 音色克隆

- `main.mp3`：主音频，建议 60-120 秒，环境安静、单人说话、无背景音乐。
- `sample.mp3`：示例音频，可选，建议 5-30 秒，用于自动转写和人工校对。

### 口播生成

- `narration.md`：口播文稿。
- `voice_id`：从 `voice_clone/voice_id.txt` 读取，或由用户提供。

## 输出

- `voice_clone/voice_id.txt`：克隆后的声音 ID。
- `voice_clone/test.mp3`：音色克隆测试音频。
- `out/narration.mp3`：最终克隆音色口播，默认带断句停顿。
- `out/narration_no_pauses.mp3`：无额外静音的拼接备份。
- `out/narration_segments/seg{N}.mp3`：逐句生成的单段音频。
- `out/emotion_plan.json`：逐句情绪规划。
- `out/emotion_plan.html`：口播段落和情绪可视化表。

## 命令

### 克隆音色

```bash
bash scripts/voice-clone.sh main.mp3 sample.mp3
```

流程：

1. 上传主音频到 MiniMax。
2. 自动转录示例音频。
3. 打开 `voice_clone/transcript.md`，等待用户校对错字。
4. 调用 MiniMax voice clone 创建 `voice_id`。
5. 生成 `voice_clone/test.mp3` 测试音频。
6. 写入 `voice_clone/voice_id.txt`。

### 生成口播

先让 Codex 根据文稿生成 `out/emotion_plan.json`。需要断句停顿时，在计划里写 `default_pause_after` 和单段 `pause_after`：

- 普通句间停顿：`0.18`
- 操作步骤、段落转场后停顿：`0.35` 到 `0.45`
- 收尾段默认不加尾部停顿，除非用户明确需要

再运行：

```bash
bash scripts/narrate-emotion.sh out/emotion_plan.json out
```

脚本会逐段合成、生成无停顿备份 `out/narration_no_pauses.mp3`，再插入短静音并重编码为最终 `out/narration.mp3`。

## 口播参数

默认使用：

- `model`: `speech-2.8-hd`
- `speed`: `1.2`
- `pitch`: `0`
- `emotion`: `happy` / `surprised` / `calm` / `sad` / `fluent`

不要使用：

- `voice_modify`
- 非 0 的 pitch
- 过长文本段落
- `啦`、`啊`、`诶`、`嘿`
- `(breath)`、`(chuckle)`、`(sigh)`
- `<#x#>` 这类强制停顿标签

## 关键规则

- 单段文本建议 60-70 个中文字符以内，最多不要超过 80 字。
- 长文稿要拆成多段分别生成，再用 ffmpeg 拼接。
- 需要停顿时不要写 `<#x#>` 等强制标签；用 `pause_after` 插入短静音。
- 示例音频的转录文字必须校对，错字会影响克隆质量。
- `voice_id` 长时间不用可能失效，建议定期生成一次短口播保持可用。

## 关键文件

| 文件 | 用途 |
|---|---|
| `scripts/voice-clone.sh` | 上传录音、转录示例音频、创建 voice_id、生成测试音频 |
| `scripts/minimax-asr.py` | MiniMax 中文 ASR 转录 |
| `scripts/minimax-t2a.py` | MiniMax T2A v2 口播合成 |
| `scripts/narrate-emotion.sh` | 按 emotion_plan 逐段合成并拼接口播 |
| `references/voice-clone-recipe.md` | 音色克隆配方和注意事项 |
| `references/per-sentence-emotion.md` | 逐句情绪规则 |
| `references/length-rules.md` | 文本长度和分段规则 |
| `templates/emotion_plan.schema.json` | emotion_plan JSON 结构 |
