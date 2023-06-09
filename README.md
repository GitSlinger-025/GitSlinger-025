import telebot
import youtube_dl

# Set up Telegram bot
bot = telebot.TeleBot('6290196267:AAGBgDH-LV0Fj4GWP89wHN-kZjRPAkmgfqw')

@bot.message_handler(commands=['start'])
def start_message(message):
    bot.reply_to(message, 'Welcome! Please send me a YouTube video URL.')

@bot.message_handler(func=lambda message: True)
def download_video(message):
    try:
        url = message.text
        ydl_opts = {'outtmpl': '%(title)s.%(ext)s'}
        with youtube_dl.YoutubeDL(ydl_opts) as ydl:
            info = ydl.extract_info(url, download=False)
            video_title = info['title']
            video_url = info['url']
            bot.send_message(message.chat.id, f"Downloading: {video_title}")
            ydl.download([url])
            bot.send_message(message.chat.id, f"Download complete: {video_title}")
            bot.send_video(message.chat.id, video=open(f"{video_title}.mp4", 'rb'))
    except Exception as e:
        bot.reply_to(message, f"Error: {str(e)}")

# Start the bot
bot.polling()
