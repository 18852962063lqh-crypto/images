# Hermes Knowledge Base

## Agnes AI API 参数速查

### 文生图
```
POST /v1/images/generations
{
  "model": "agnes-image-2.1-flash",
  "prompt": "描述",
  "size": "768x1024"  // 竖屏 或 1024x768 横屏
}
```

### 图生图（2.0 Flash ✅ 最稳定）
```
POST /v1/images/generations
{
  "model": "agnes-image-2.0-flash",
  "prompt": "描述",
  "image": "https://在线图片URL",  // 顶层参数！不是extra_body！
  "n": 1,
  "size": "1024x1024"
}
```

### 图生图（2.1 Flash - 可能超时）
```
POST /v1/images/generations
{
  "model": "agnes-image-2.1-flash",
  "prompt": "描述",
  "size": "1024x768",
  "extra_body": {
    "image": ["https://在线图片URL"],
    "response_format": "url"
  }
}
```

### 单图生视频
```
POST /v1/videos
{
  "model": "agnes-video-v2.0",
  "prompt": "动作描述",
  "image": "https://在线图片URL",
  "num_frames": 81,
  "frame_rate": 16,
  "width": 768,
  "height": 1024
}
```

### 多图生视频
```
POST /v1/videos
{
  "model": "agnes-video-v2.0",
  "prompt": "过渡描述",
  "extra_body": {
    "image": ["https://图1URL", "https://图2URL"]
  },
  "num_frames": 81,
  "frame_rate": 16,
  "width": 1024,
  "height": 576
}
```

### 对话/剧本生成
```
POST /v1/chat/completions
{
  "model": "agnes-2.0-flash",
  "messages": [{"role": "user", "content": "..."}],
  "temperature": 0.7
}
```

## 绘本视频流水线（picturebook-blocks-workflow）

### 完整流程
Block0: GPT生成故事+角色设定
  → block0_v2.js (生成story_content.json, 含title/author/pages/moral/character)

Block1: 分镜生成
  → block1_v2.js (从story_content.json读角色，每页3段=30段storyboard_full.json)

Block2: Gemini生图
  → block2相关积木 (enable_imagen→发prompt→wait_image下载)
  → 30张图存到gemini_images/

Block3: MD5文件去重
  → block3_01_get_file_hash.py (硬拦截，确保图片唯一)

Block5: 图生视频
  → 以前用混元，现在用Agnes
  → batch_single_videos.py (GitHub raw URL + 单图生视频)

Block7: TTS配音+字幕+合成
  → block7_tts_subs_compose.py (edge-tts + ffmpeg合成)

### 积木目录
D:\novel_video\scripts\blocks\ — 所有积木
D:\novel_video\scripts\bricks\ — 积木依赖库
D:\novel_video\scripts\common\ — 公共模块(chrome_connect等)

### 验证记录
- 小刺猬星光邮局 ✅
- 小河狸的新家 ✅
- 云朵小羊驼绒绒 ✅
- 小松鼠栗果的橡果音乐会 ✅ (30段合成2分32秒)
- 小浣熊果果的彩虹浆果节 ✅

## 短剧/漫剧流水线（comic-video-pipeline）
独立于绘本的新线，已集成至shortdrama-pipeline。

流程：GPT剧本→Agnes角色图→图生图场景图→图生视频→配音合成
详情看skill: comic-video-pipeline

## GitHub图床
仓库: github.com/18852962063lqh-crypto/images
本地路径: E:\\gemini-images\\
raw URL格式: https://raw.githubusercontent.com/18852962063lqh-crypto/images/main/文件名.png
push必须在Windows PowerShell下（WSL没认证）

## 关键纪律
- 接口永远是通的！超时就是我的传参有问题
- 图生图默认用2.0 flash + 顶层image参数，不超时
- timeout至少120秒，视频轮询300秒
- 先给结论再给方案
- 绝对诚实，宁可说没试过也不要美化夸大
- 每次犯错必须是全新的错
- 用户说不行后立刻换方案，3次否定=放弃
- 不劝休息，服务不稳就写重试逻辑
