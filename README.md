# Video Transcribe Telegram Bot

یک ورکفلو n8n برای ترنسکرایب ویدیوهای یوتیوب/اینستاگرام/سایر سایت‌ها و تولید پست تلگرام خلاصه شده با هوش مصنوعی.

## 🎯 عملکرد

1. کاربر لینک ویدیو را برای بات تلگرام می‌فرستد
2. **Google Gemini 2.5 Flash** ویدیو را تحلیل و ترنسکرایب می‌کند
3. **OpenAI GPT-5-mini** یک پست جذاب، ساختاریافته و فارسی می‌سازد
4. نتیجه به صورت **Reply** به پیام اصلی کاربر ارسال می‌شود

## 🏗️ معماری ورکفلو

```
Telegram Trigger → Gemini Video Transcribe → Generate Telegram Post (Agent) → Send Telegram Message
                         ↑
                   OpenAI Chat Model (sub-node)
```

### نودها

| نود | نوع | توضیح |
|------|------|--------|
| Telegram Trigger | Trigger | دریافت پیام‌های تلگرام (متن/کپشن) |
| Gemini Video Transcribe | Google Gemini | ترنسکرایب و تحلیل ویدیو از URL |
| OpenAI Chat Model | Language Model | مدل GPT-5-mini برای Agent |
| Generate Telegram Post | AI Agent | تولید پست تلگرام از ترنسکرایب |
| Send Telegram Message | Action | ارسال پاسخ به کاربر |

## 🚀 راه‌اندازی

### پیش‌نیازها

- اکانت n8n Cloud یا Self-hosted
- بات تلگرام (از @BotFather)
- API Keyهای:
  - [Google Gemini API](https://aistudio.google.com/app/apikey)
  - [OpenAI API](https://platform.openai.com/api-keys)

### 1. ورود به n8n و Import

```bash
# روش 1: از فایل JSON
# در n8n: Workflows → Import → انتخاب فایل video-transcribe-telegram-bot.json

# روش 2: کلون مخزن و import
git clone https://github.com/SirAzadi/video-transcribe-telegram-bot.git
```

### 2. تنظیم Credentials

در n8n به **Credentials** بروید و سه مورد زیر را اضافه کنید:

| Credential Type | نام در n8n | مقادیر مورد نیاز |
|-----------------|------------|------------------|
| Telegram API | `Telegram` | Bot Token (از @BotFather) |
| Google Gemini API | `Google Gemini` | API Key |
| OpenAI API | `OpenAI` | API Key |

### 3. اتصال Credentials به نودها

هر نود را باز کنید و Credential مربوطه را انتخاب کنید:
- **Telegram Trigger** → Telegram
- **Gemini Video Transcribe** → Google Gemini
- **OpenAI Chat Model** → OpenAI
- **Send Telegram Message** → Telegram

### 4. فعال‌سازی

1. ورکفلو را باز کنید
2. دکمه **Activate** را بزنید
3. وب‌هوک تلگرام به‌صورت خودکار ثبت می‌شود

## 📝 نحوه استفاده

1. در تلگرام به بات خود پیام دهید
2. لینک ویدیو را بفرستید (یوتیوب، اینستاگرام، آپارات، و...):
   ```
   https://www.youtube.com/watch?v=...
   https://www.instagram.com/reel/...
   ```
3. صبر کنید تا پردازش کامل شود
4. پست خلاصه شده به صورت Reply دریافت می‌کنید

## ⚙️ تنظیمات قابل سفارشی‌سازی

### تغییر مدل Gemini
در نود **Gemini Video Transcribe**:
```json
"modelId": "models/gemini-2.5-flash"  // یا models/gemini-1.5-pro
```

### تغییر مدل OpenAI
در نود **OpenAI Chat Model**:
```json
"model": "gpt-5-mini"  // یا gpt-5, gpt-5-nano
```

### تغییر پرامپت تولید پست
در نود **Generate Telegram Post** → پارامتر `text` را ویرایش کنید.

### تغییر حداکثر توکن‌های خروجی
در نود **Gemini Video Transcribe**:
```json
"options": { "maxOutputTokens": 8192 }
```

## 📁 ساختار مخزن

```
video-transcribe-telegram-bot/
├── video-transcribe-telegram-bot.json   # فایل ورکفلو n8n
└── README.md                            # این فایل
```

## 🔧 عیب‌یابی

| خطا | راه‌حل |
|------|--------|
| "Invalid video URL" | مطمئن شوید لینک مستقیم ویدیو است، نه لینک کانال/پلی‌لیست |
| "Quota exceeded" | سهمیه API Gemini/OpenAI را چک کنید |
| "Telegram: Bad Request: chat not found" | Credential تلگرام را بررسی کنید، Bot Token صحیح باشد |
| ویدیو‌های طولانی پردازش نمی‌شوند | `maxOutputTokens` را افزایش دهید یا مدل Pro استفاده کنید |

## 📄 مجوز

MIT License - آزاد برای استفاده، تغییر و توزیع.

## 🤝 مشارکت

PRها و Issueها خوش‌آمده‌اند!

---

ساخته شده با ❤️ با [n8n](https://n8n.io) و هوش مصنوعی