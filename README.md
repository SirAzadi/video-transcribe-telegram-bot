# Video Transcribe Telegram Bot

An n8n workflow that receives a video link from a Telegram user, transcribes the video content using Google Gemini, and generates a ready-to-share Persian Telegram post using OpenAI GPT-5-mini.

## 🎯 How It Works

1. **User sends a video link** (YouTube, Instagram, etc.) to the Telegram bot
2. **Google Gemini 2.5 Flash** analyzes the video URL and transcribes/extracts content
3. **OpenAI GPT-5-mini** creates an engaging, structured Persian Telegram post from the transcription
4. **Bot replies** with the formatted post to the user's original message

## 🏗️ Workflow Architecture

```
Telegram Trigger → Gemini Video Transcribe → Generate Telegram Post (Agent) → Send Telegram Message
                         ↑
                   OpenAI Chat Model (sub-node)
```

### Nodes

| Node | Type | Description |
|------|------|-------------|
| Telegram Trigger | Trigger | Receives messages containing video links |
| Gemini Video Transcribe | Google Gemini | Transcribes/analyzes video from URL (gemini-2.5-flash) |
| OpenAI Chat Model | Language Model | GPT-5-mini for the AI Agent |
| Generate Telegram Post | AI Agent | Creates formatted Persian post from transcription |
| Send Telegram Message | Action | Sends the post as a reply to user |

## ⚠️ Known Issue - Missing Extraction Step

**The current workflow is missing a critical step:** There's no node to extract the video URL from the Telegram message payload.

The Telegram Trigger outputs data in this structure:
```json
{
  "message": {
    "text": "https://youtube.com/watch?v=...",
    "chat": { "id": 123456 },
    "message_id": 789
  }
}
```

But the Gemini node expects `$json.videoUrl` which doesn't exist yet.

### Required Fix
Add a **Set node** (or Edit Fields node) between Telegram Trigger and Gemini Video Transcribe to extract:
- `videoUrl` from `$json.message.text` or `$json.message.caption`
- `chatId` from `$json.message.chat.id`
- `messageId` from `$json.message.message_id`

## 🚀 Setup

### Prerequisites

- n8n Cloud or Self-hosted instance
- Telegram Bot (from [@BotFather](https://t.me/BotFather))
- API Keys:
  - [Google Gemini API](https://aistudio.google.com/app/apikey)
  - [OpenAI API](https://platform.openai.com/api-keys)

### 1. Import Workflow

**Option 1: From JSON file**
- In n8n: Workflows → Import → Select `video-transcribe-telegram-bot.json`

**Option 2: Clone repository**
```bash
git clone https://github.com/SirAzadi/video-transcribe-telegram-bot.git
```

### 2. Configure Credentials

Go to **Credentials** in n8n and add:

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

### 4. Fix the Workflow (Required)

Before activating, you **must** add an extraction node:

1. Add a **Set** node (or **Edit Fields** node) after Telegram Trigger
2. Configure it to extract:
   ```javascript
   // videoUrl
   {{ $json.message?.text || $json.message?.caption || "" }}
   
   // chatId
   {{ $json.message?.chat?.id }}
   
   // messageId
   {{ $json.message?.message_id }}
   ```
3. Connect: Telegram Trigger → Set Node → Gemini Video Transcribe
4. Update Gemini node to use `{{ $json.videoUrl }}` (already configured)
5. Update Send Message node to use `{{ $json.chatId }}` and `{{ $json.messageId }}` (already configured)

### 5. Activate

1. Open the workflow
2. Click **Activate**
3. Telegram webhook registers automatically

## 📝 Usage

1. Message your bot on Telegram
2. Send a video link:
   ```
   https://www.youtube.com/watch?v=...
   https://www.instagram.com/reel/...
   https://www.aparat.com/v/...
   ```
3. Wait for AI processing
4. Receive a formatted Persian post as a **Reply**

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
├── video-transcribe-telegram-bot.json   # n8n workflow file (needs extraction node fix)
└── README.md                            # This file
```

## 🔧 Troubleshooting

| Error | Solution |
|-------|----------|
| "Invalid video URL" / Gemini fails | Add extraction node to get videoUrl from message.text |
| "chatId undefined" in Send Message | Ensure extraction node passes chatId and messageId |
| "Quota exceeded" | Check Gemini/OpenAI API quotas |
| "Telegram: Bad Request: chat not found" | Verify Telegram credential, Bot Token must be correct |
| Long videos fail | Increase `maxOutputTokens` or use Pro model |

## 📄 License

MIT License - Free to use, modify, and distribute.

## 🤝 Contributing

PRs and Issues welcome! Especially to fix the missing extraction node.

---

Built with ❤️ using [n8n](https://n8n.io) and AI