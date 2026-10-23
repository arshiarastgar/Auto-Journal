# Auto-Journal
this script can write you trading journal for you from your CEX trading history

# 📓 Bitunix Trading Journal Bot

ژورنال معاملاتی کاملاً خودکار برای صرافی **Bitunix**:

- **گزارش هفتگی تلگرام** — هر دوشنبه ساعت ۰۹:۰۰ تهران: آمار هفته (وین‌ریت، سود/ضرر، R)، مقایسه با هفته قبل، و بخش «نکات، اشتباه‌ها و پیشنهادها» با تحلیل الگوهای معاملاتی شما.
- **ژورنال زنده در Notion** — هر ۳۰ دقیقه: پوزیشن باز به‌محض باز شدن با وضعیت `Open` در ژورنال ثبت می‌شود (ورود، ساعت، لوریج، حجم، SL، RR، ریسک) و وقتی بسته شد، نتیجه‌اش (Profit/Loss) و بقیه اطلاعاتش خودکار کامل می‌شود.
- ستون‌هایی که ربات پر می‌کند فقط خانه‌های **خالی** هستند؛ چیزی که خودتان دستی نوشته باشید هرگز بازنویسی نمی‌شود.
- درصد ریسک هر معامله = ریسک دلاری برنامه‌ریزی‌شده ÷ کل سرمایه × ۱۰۰.

همه‌چیز روی **GitHub Actions** اجرا می‌شود؛ کامپیوتر شما لازم نیست روشن باشد.

---

## راه‌اندازی (قدم‌به‌قدم)

### ۱) ریپو بسازید
یک ریپوی جدید در گیت‌هاب بسازید (Private یا Public فرقی ندارد) و همه فایل‌های این پکیج را در آن آپلود کنید — با همین ساختار:

```
journal.py
.env.example
.gitignore
.github/workflows/weekly.yml
.github/workflows/sync.yml
```

### ۲) کلید Bitunix (فقط خواندنی!)
در Bitunix از بخش API Management یک کلید بسازید با دسترسی **Read only** —
⛔ دسترسی Trade و Withdraw را فعال **نکنید**.

### ۳) بات تلگرام
- در تلگرام به **@BotFather** پیام بدهید → `/newbot` → توکن بات را نگه دارید.
- به بات خودتان یک پیام بدهید (مثلاً سلام).
- چت‌آیدی: مرورگر را باز کنید و بروید به
  `https://api.telegram.org/bot<TOKEN>/getUpdates`
  عدد `chat.id` در جواب، چت‌آیدی شماست.

### ۴) دیتابیس Notion
یک دیتابیس با این ستون‌ها بسازید (اسم و نوع باید دقیقاً همین باشد):

| ستون | نوع |
|---|---|
| Nu. | Title |
| Date | Date |
| Time | Text |
| Symbol | Select |
| Direction | Multi-select (گزینه‌ها: LONG, SHORT) |
| Entry Price | Text |
| Leverage | Select (مثل 5X, 10X, 20X) |
| TF | Select |
| Method | Multi-select |
| Oscillator | Multi-select |
| Mood | Select |
| Risk | Text |
| SL | Text |
| RR | Text |
| Volume | Text |
| P/L | Select (گزینه‌ها: Profit, Loss, Order, Open, Canceled, Risk Free) |
| Notes | Text |
| Note | Text |

بعد:
- در [notion.so/my-integrations](https://notion.so/my-integrations) یک **Connection** بسازید و **Internal Integration Secret** آن را بردارید (با `ntn_` شروع می‌شود).
- دیتابیس را با آن Connection شیر کنید: دیتابیس را باز کنید → `•••` → **Connections** → کانکشن شما → Confirm.
- آیدی دیتابیس را از لینک دیتابیس بردارید (قسمت ۳۲ کاراکتری قبل از `?v=`).

### ۵) سکرت‌ها را در گیت‌هاب بگذارید
ریپو → **Settings → Secrets and variables → Actions → New repository secret** — این ۶ تا:

| اسم سکرت | مقدار |
|---|---|
| `BITUNIX_API_KEY` | کلید API بیت‌یونیکس |
| `BITUNIX_API_SECRET` | سکرت همان کلید |
| `TELEGRAM_BOT_TOKEN` | توکن بات تلگرام |
| `TELEGRAM_CHAT_ID` | چت‌آیدی شما |
| `NOTION_TOKEN` | سکرت کانکشن نوشن (`ntn_...`) |
| `NOTION_DATABASE_ID` | آیدی دیتابیس نوشن |

### ۶) شخصی‌سازی
در هر دو فایل `.github/workflows/*.yml` دو مقدار را با شرایط خودتان تنظیم کنید:
- `ACCOUNT_SIZE_USD` — کل سرمایه معاملاتی شما به USDT (مبنای محاسبه Risk %).
- `NOTION_SYNC_FROM` — تاریخ شروع ژورنال؛ معاملات قبل از این تاریخ در نوشن نوشته نمی‌شوند.

### ۷) تست
در تب **Actions**:
- ورک‌فلوی **Notion live sync** را با دکمه **Run workflow** اجرا کنید (همیشه اجرای تازه بزنید، نه Re-run). اگر پوزیشن بازی دارید باید ظرف چند دقیقه در نوشن ظاهر شود.
- ورک‌فلوی **Bitunix Weekly Journal** را با حالت `last_30_days` اجرا کنید؛ گزارش باید به تلگرامتان بیاید.

از آن به بعد همه‌چیز خودکار است: گزارش دوشنبه‌ها + سینک هر ۳۰ دقیقه.

---

## 🔐 نکته‌های امنیتی (مهم)

- کلیدها **فقط** در GitHub Secrets قرار می‌گیرند — هرگز آن‌ها را داخل هیچ فایلی در ریپو ننویسید.
- فایل `.env` (اگر برای اجرای محلی ساختید) را **هرگز** آپلود نکنید؛ در `.gitignore` هم هست.
- کلید Bitunix حتماً **Read only** باشد؛ با این کلید نه معامله ممکن است نه برداشت.

## ستون‌هایی که دستی می‌مانند
ربات این‌ها را از API نمی‌تواند بداند و خالی می‌گذارد تا خودتان پر کنید:
`TF` ،`Method` ،`Oscillator` ،`Mood` ،`Notes` ،`Note`
