# انسخ هذا إلى ملف .env واملأ القيمة ثم لا ترفع الملف لمستودعك
TELEGRAM_TOKEN=
DB_PATH=roulette.db
STARTING_BALANCE=1000# -
أكيد! هذا كود بوت تيليجرام كامل وجاهز — نسخة مبسطة، آمنة، وتشتغل مباشرة على جهازك أو Render أو GitHub.
(بدون أي توكن داخل الكود — أنت تضيفه من الخارج فقط).


---

🟩 ملف bot.py (الكود الكامل)

انسخ هذا الملف كما هو:

import os
import random
from telegram.ext import Updater, CommandHandler

# ---------------------------------------------------
#  احضار التوكن من متغير البيئة (ليس داخل الكود)
# ---------------------------------------------------
TOKEN = os.getenv("BOT_TOKEN")

# تخزين أرصدة المستخدمين (في الذاكرة فقط)
balances = {}

# ---------------------------------------------------
# أوامر البوت
# ---------------------------------------------------

def start(update, context):
    update.message.reply_text(
        "مرحباً! 👋\n"
        "أنا بوت روليت تجريبي.\n\n"
        "الأوامر:\n"
        "/balance — عرض رصيدك\n"
        "/deposit 100 — إضافة رصيد\n"
        "/bet red 10 — رهان على اللون\n\n"
        "استمتع!"
    )

def balance(update, context):
    user = update.message.from_user.id
    bal = balances.get(user, 0)
    update.message.reply_text(f"💰 رصيدك الحالي: {bal}")

def deposit(update, context):
    user = update.message.from_user.id
    try:
        amount = int(context.args[0])
        if amount <= 0:
            update.message.reply_text("يجب إدخال مبلغ أكبر من 0")
            return

        balances[user] = balances.get(user, 0) + amount
        update.message.reply_text(f"✔️ تم إضافة {amount}. رصيدك الآن {balances[user]}")

    except:
        update.message.reply_text("استخدم: /deposit 100")

def bet(update, context):
    user = update.message.from_user.id

    try:
        bet_color = context.args[0].lower()
        amount = int(context.args[1])
    except:
        update.message.reply_text("استخدم: /bet red 10")
        return

    bal = balances.get(user, 0)

    if amount > bal:
        update.message.reply_text("❌ ليس لديك رصيد كافٍ.")
        return

    # خصم المبلغ
    balances[user] -= amount

    # توليد رقم الروليت
    number = random.randint(0, 36)
    color = "green" if number == 0 else ("red" if number % 2 else "black")

    if bet_color == color:
        win = amount * 2
        balances[user] += win
        update.message.reply_text(
            f"🎉 فزت!\n"
            f"النتيجة: {number} ({color})\n"
            f"ربحك: {win - amount}\n"
            f"رصيدك الآن: {balances[user]}"
        )
    else:
        update.message.reply_text(
            f"❌ خسرت!\n"
            f"النتيجة: {number} ({color})\n"
            f"رصيدك الآن: {balances[user]}"
        )

# ---------------------------------------------------
# تشغيل البوت
# ---------------------------------------------------
def main():
    if TOKEN is None:
        print("❌ BOT_TOKEN غير موجود. ضعه في متغير البيئة.")
        return

    updater = Updater(TOKEN, use_context=True)
    dp = updater.dispatcher

    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(CommandHandler("balance", balance))
    dp.add_handler(CommandHandler("deposit", deposit))
    dp.add_handler(CommandHandler("bet", bet))

    print("✅ البوت يعمل الآن...")
    updater.start_polling()
    updater.idle()

if __name__ == "__main__":
    main()


---

🟩 ملف requirements.txt

الملف اللازم للتشغيل على GitHub أو Render:

python-telegram-bot==13.15


---

🟩 ملف render.yaml (إذا كنت ستنشر على Render)

services:
  - type: worker
    name: telegram-bot
    env: python
    plan: free
    buildCommand: "pip install -r requirements.txt"
    startCommand: "python bot.py"


---

🟦 هل تريد؟

✔️ أن أربط لك الكود جاهز ب‍ GitHub؟
✔️ أن أجهّز ملف ZIP يحتوي المشروع كاملاً؟
✔️ تعليمات نشر Render خطوة بخطوة؟

بس قلّي، وأنا أجهّز لك كل شيء 👍
