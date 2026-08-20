# Video Transcribe Telegram Bot

An n8n workflow for transcribing videos from YouTube, Instagram, and other sites, then generating a summarized Telegram post using AI.

## 🎯 Features

1. User sends a video link to the Telegram bot
2. **Google Gemini 2.5 Flash** analyzes and transcribes the video
3. **OpenAI GPT-5-mini** creates an engaging, structured Persian post
4. Result is sent as a **Reply** to the user's original message

## 🏗️ Workflow Architecture

```
Telegram Trigger → Gemini Video Transcribe → Generate Telegram Post (Agent) → Send Telegram Message
                         ↑
                   OpenAI Chat Model (sub-node)
```

### Nodes

| Node | Type | Description |
|------|------|-------------|
| Telegram Trigger | Trigger | Receives Telegram messages (text/caption) |
| Gemini Video Transcribe | Google Gemini | Transcribes and analyzes video from URL |
| OpenAI Chat Model | Language Model | GPT-5-mini model for the Agent |
| Generate Telegram Post | AI Agent | Creates Telegram post from transcription |
| Send Telegram Message | Action | Sends response back to user |

## 🚀 Setup

### Prerequisites

- n8n Cloud or Self-hosted instance
- Telegram Bot (from @BotFather)
- API Keys:
  - [Google Gemini API](https://aistudio.google.com/app/apikey)
  - [OpenAI API](https://platform.openai.com/api-keys)

### 1. Import Workflow

**Option 1: From JSON file**
- In n8n: Workflows → Import → Select `video-transcribe-telegram-bot.json`

**Option 2: Clone and import**
```bash
git clone https://github.com/SirAzadi/video-transcribe-telegram-bot.git
```

### 2. Configure Credentials

Go to **Credentials** in n8n and add these three:

| Credential Type | Name in n8n | Required Values |
|-----------------|-------------|-----------------|
| Telegram API | `Telegram` | Bot Token (from @BotFather) |
| Google Gemini API | `Google Gemini` | API Key |
| OpenAI API | `OpenAI` | API Key |

### 3. Connect Credentials to Nodes

Open each node and select the appropriate credential:
- **Telegram Trigger** → Telegram
- **Gemini Video Transcribe** → Google Gemini
- **OpenAI Chat Model** → OpenAI
- **Send Telegram Message** → Telegram

### 4. Activate

1. Open the workflow
2. Click **Activate**
3. Telegram webhook is registered automatically

## 📝 Usage

1. Message your bot on Telegram
2. Send a video link (YouTube, Instagram, Aparat, etc.):
   ```
   https://www.youtube.com/watch?v=...
   https://www.instagram.com/reel/...
   ```
3. Wait for processing to complete
4. Receive the summarized post as a Reply

## ⚙️ Customization

### Change Gemini Model
In **Gemini Video Transcribe** node:
```json
"modelId": "models/gemini-2.5-flash"  // or models/gemini-1.5-pro
```

### Change OpenAI Model
In **OpenAI Chat Model** node:
```json
"model": "gpt-5-mini"  // or gpt-5, gpt-5-nano
```

### Modify Post Generation Prompt
Edit the `text` parameter in **Generate Telegram Post** node.

### Adjust Max Output Tokens
In **Gemini Video Transcribe** node:
```json
"options": { "maxOutputTokens": 8192 }
```

## 📁 Repository Structure

```
video-transcribe-telegram-bot/
├── video-transcribe-telegram-bot.json   # n8n workflow file
└── README.md                            # This file
```

## 🔧 Troubleshooting

| Error | Solution |
|-------|----------|
| "Invalid video URL" | Ensure link is a direct video URL, not channel/playlist |
| "Quota exceeded" | Check Gemini/OpenAI API quotas |
| "Telegram: Bad Request: chat not found" | Verify Telegram credential, ensure Bot Token is correct |
| Long videos fail to process | Increase `maxOutputTokens` or use Pro model |

## 📄 License

MIT License - Free to use, modify, and distribute.

## 🤝 Contributing

PRs and Issues welcome!

---

Built with ❤️ using [n8n](https://n8n.io) and AI