import telebot
from telebot.types import Message
from collections import defaultdict

# === BOT TOKEN ===
TOKEN = "8433947409:AAGVi22HL81cKe5yeqzNEU5yYv2CBUlz3SM"
bot = telebot.TeleBot(TOKEN)

# === MEMORY DATABASE ===
users = {}
referrals = defaultdict(set)
wallets = {}

# === /start ===
@bot.message_handler(commands=['start'])
def handle_start(message: Message):
    user_id = message.from_user.id
    username = message.from_user.username or f"user_{user_id}"
    referrer_id = None

    # Referral detection
    if " " in message.text:
        ref_param = message.text.split(" ")[1]
        if ref_param.isdigit():
            referrer_id = int(ref_param)

    # If user not joined before
    if user_id not in users:
        user_num = len(users) + 1

        # Base reward logic
        if user_num <= 100:
            base_reward = 100
        elif user_num <= 1000:
            base_reward = 50
        else:
            base_reward = 0

        users[user_id] = {
            'username': username,
            'ref': referrer_id,
            'referrals': set(),
            'base': base_reward
        }

        if referrer_id and referrer_id != user_id and referrer_id in users:
            referrals[referrer_id].add(user_id)
            users[referrer_id]['referrals'].add(user_id)

    # Referral link
    referral_link = f"https://t.me/falahxofficialbot?start={user_id}"
    referral_count = len(users[user_id]['referrals'])
    base = users[user_id]['base']
    referral_reward = referral_count * 2
    total = base + referral_reward

    reply = (
        f"👋 Welcome to FALAHX Airdrop!\n\n"
        f"✅ You are Join #{len(users)}\n"
        f"🎁 Base Reward: {base} FALAHX\n"
        f"🔁 Referrals: {referral_count} → {referral_reward} FALAHX\n"
        f"💰 Total Airdrop: {total} FALAHX\n"
        f"🔗 Your Link: {referral_link}"
    )
    bot.reply_to(message, reply)

# === /wallet ===
@bot.message_handler(commands=['wallet'])
def handle_wallet(message: Message):
    user_id = message.from_user.id
    args = message.text.split(" ")

    if len(args) != 2:
        return bot.reply_to(message, "❗ Usage: /wallet your_wallet_address")

    wallet_address = args[1]
    wallets[user_id] = wallet_address
    bot.reply_to(message, f"✅ Wallet saved: {wallet_address}")

# === /balance ===
@bot.message_handler(commands=['balance'])
def handle_balance(message: Message):
    user_id = message.from_user.id
    if user_id not in users:
        return bot.reply_to(message, "❗ Please use /start first.")

    base = users[user_id]['base']
    referral_count = len(users[user_id]['referrals'])
    total = base + referral_count * 2
    bot.reply_to(message, f"💼 Total: {total} FALAHX (🎁 {base} + 🔁 {referral_count}×2)")

# === /help ===
@bot.message_handler(commands=['help'])
def handle_help(message: Message):
    bot.reply_to(message,
        "/start - Join Airdrop\n"
        "/wallet <your_wallet> - Save your wallet\n"
        "/balance - Check airdrop balance\n"
        "/help - Show help"
    )

# === BOT START ===
print("🤖 FALAHX Bot is running...")
bot.infinity_polling()
