from pyrogram import Client, filters
import requests
import base64
import urllib.parse
import config

VERCEL_DOMAIN = "https://heavenverse.vercel.app"
START_PIC = "https://i.ibb.co/YTLtj7r2/d43eb18b6542.jpg"

bot = Client(
    "komi_shortener_bot",
    api_id=config.API_ID,
    api_hash=config.API_HASH,
    bot_token=config.BOT_TOKEN
)

# ---------------- FORCE SUB CHECK ---------------- #

async def check_force_sub(client, user_id):
    for channel in config.FORCE_SUB_CHANNELS:
        try:
            member = await client.get_chat_member(channel, user_id)
            if member.status in ["left", "kicked"]:
                return False
        except:
            return False
    return True


# ---------------- START COMMAND ---------------- #

@bot.on_message(filters.command("start"))
async def start(client, message):

    text = """
👋 Welcome to **Komi Link Provider Bot**

🔒 Secure Links  
💰 Ad Protected Links  
⚡ Instant Link Generator

Send any link to shorten it.
"""

    await message.reply_photo(
        photo=START_PIC,
        caption=text
    )


# ---------------- SHORTENER ---------------- #

@bot.on_message(filters.text & ~filters.command(["start"]))
async def shorten_link(client, message):

    user_id = message.from_user.id

    subscribed = await check_force_sub(client, user_id)

    if not subscribed:

        buttons = ""

        for ch in config.FORCE_SUB_CHANNELS:
            buttons += f"{ch}\n"

        await message.reply_text(
            f"⚠️ You must join all channels first:\n\n{buttons}"
        )
        return

    original_link = message.text.strip()

    try:

        encoded_link = base64.urlsafe_b64encode(original_link.encode()).decode()

        protected_link = f"{VERCEL_DOMAIN}/?url={encoded_link}"

        api_url = (
            f"https://linkshortify.com/api"
            f"?api={config.SHORTENER_API}"
            f"&url={urllib.parse.quote(protected_link)}"
        )

        response = requests.get(api_url).json()

        if response["status"] == "success":

            short_link = response["shortenedUrl"]

            await message.reply_text(
                f"✅ **Your Protected Link**\n\n{short_link}\n\n"
                f"🔒 Vercel Protected\n"
                f"💰 Ad Monetized"
            )

        else:
            await message.reply_text("❌ Failed to shorten the link.")

    except:
        await message.reply_text("⚠️ Error processing link.")


print("Bot Started Successfully")

bot.run()
