# voice-clone-narrator

在 Codex 里用 MiniMax 克隆自己的音色，并用克隆音色生成口播。

## 视频教程口播文案

```text
上期音色克隆视频受到了大家的关注，这期视频详细教大家怎么使用。

第一步，先安装这个音色克隆 skill。

第二步，打开 MiniMax 官网，注册并登录账号。

登录之后，点击 API，进入控制台。

在控制台里先完成充值，然后生成一个 API 密钥。这个密钥后面会用到，先复制保存好。

第三步，录制一段自己的声音，建议 60 到 120 秒。

录音的时候尽量保持环境安静，不要有背景音乐，也不要多人说话。声音越干净，后面克隆出来的效果越好。

第四步，打开 Codex。

在对话框里选中这个音色克隆 skill，然后把刚才录好的声音文件和你的文稿上传进去。

接着告诉 Codex：

请使用这个技能克隆我的音色，并根据我的文稿生成口播。

这时候 Codex 会提示你上传 MiniMax 的 API 密钥。

你回到 MiniMax 控制台，把刚才生成的 API Key 复制过来，发送给 Codex。

然后它就会自动开始处理，帮你克隆音色，并生成你的 voice_id。

这个 voice_id 就相当于你的 AI 声音身份。

后续你只需要上传新的配音稿件，就可以用这个 voice_id 生成和你声音很像的 AI 口播。

简单来说，整个流程就是：

安装 skill，注册 MiniMax，充值并生成 API Key，录一段自己的声音，然后在 Codex 里上传录音和文稿，让它自动克隆并生成口播。

这样，你就拥有了一个可以反复使用的个人 AI 音色。
```

## 安装

把仓库放到你的 Codex skills 目录，例如：

```bash
git clone <your-repo-url> ~/.codex/skills/voice-clone-narrator
```

## 准备

1. 注册并登录 MiniMax。
2. 进入 API 控制台，充值并生成 API Key。
3. 录制一段 60-120 秒的干净人声。
4. 可选：再准备一段 5-30 秒示例音频，用于转录校对。

## 在 Codex 中使用

选择 `voice-clone-narrator` skill，然后上传：

- 主录音：`main.mp3`
- 示例录音：`sample.mp3`
- 口播文稿：`narration.md`

对 Codex 说：

```text
请使用这个技能克隆我的音色，并根据我的文稿生成口播。
```

Codex 会在需要时提示你提供 MiniMax API Key。

## 本地命令

克隆音色：

```bash
bash scripts/voice-clone.sh main.mp3 sample.mp3
```

生成口播：

```bash
bash scripts/narrate-emotion.sh out/emotion_plan.json out
```

## 输出

- `voice_clone/voice_id.txt`
- `voice_clone/test.mp3`
- `out/narration.mp3`
- `out/emotion_plan.json`
- `out/emotion_plan.html`

## 注意

- 录音尽量安静、清晰、单人说话。
- 示例音频转录后要人工校对。
- 每段口播文本建议 60-70 个中文字符以内。
- 不要在文稿里加入奇怪的口癖、动作标签或强制停顿标签。
- `voice_id` 长时间不用可能失效，建议定期生成一次短口播。

## License

MIT

