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
  "image": "https://在线图片URL",  // 在线URL，不需base64
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

## 关键纪律
- 接口永远是通的！超时就是我的传参有问题
- 图生图默认用 2.0 flash + 顶层image参数
- timeout至少120秒，视频轮询300秒
- GitHub raw URL做图床：https://raw.githubusercontent.com/18852962063lqh-crypto/images/main/
- 飞书URL需要token鉴权，不能直接给Agnes用
- push到GitHub必须在Windows PowerShell下（WSL没认证）

## 图床仓库
https://github.com/18852962063lqh-crypto/images
路径: E:\\gemini-images\\
Gemini图push到这个仓库，raw URL格式:
https://raw.githubusercontent.com/18852962063lqh-crypto/images/main/文件名.png

## 用户纪律
- 绝对诚实，宁可说没试过也不要美化夸大
- 每次犯错必须是全新的错
- 用户说不行后立刻换方案，3次否定=放弃
- 不劝休息，服务不稳就写重试逻辑
- 调积木不写临时脚本（除非调不了）
