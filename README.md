#euro_bot.py

import discord
from discord.ext import commands
from googletrans import Translator, LANGUAGES


from bot_token import token  # Tokeninizi buraya yazın

intents = discord.Intents.default()  # Default intents; tüm temel intents'leri etkinleştirir
intents.messages = True  # Mesajları dinlemek için gereklidir
intents.guilds = True    # Sunucu bağlantısını dinlemek için gereklidir

bot = commands.Bot(command_prefix='!', intents=intents)
translator = Translator()

@bot.event
async def on_ready():
    print(f'Logged in as {bot.user.name}')

@bot.command(name="eurovision")
async def eurovision(ctx, lang='en'):
    basic_info = "The Eurovision Song Contest is an international song competition held primarily among the member countries of the European Broadcasting Union."
    if lang in LANGUAGES:
        try:
            translated_info = translator.translate(basic_info, dest=lang).text
            await ctx.send(translated_info)
        except Exception as e:
            await ctx.send(f"Error translating message: {str(e)}")
    else:
        await ctx.send("Sorry, I don't support this language yet.")

@bot.command()
async def results(ctx, year: int = None):
    if year is None:
        await ctx.send("You need to specify the year. Usage: `!results <year>`")
        return

    results_info = get_eurovision_results(str(year))
    await ctx.send(results_info)

def get_eurovision_results(year):
    results = {
        '2021': 'Italy won with the song "Zitti e buoni" by Måneskin.',
        '2020': 'The contest was cancelled due to the COVID-19 pandemic.',
        '2019': 'The Netherlands won with the song "Arcade" by Duncan Laurence.',
        '2003': 'Turkey won with the song "Every Way That I Can" by Sertab Erener',
        '2005': 'Greece won with the song "My Number One" by Helena Paparizou',
        '2024': 'The Switzerland with the song "The code has been cracked" by Nemo'
    }
    return results.get(year, "Results for this year are not available.")

def get_country_eurovision_info(country_name):
    country_info = {
        'Italy': 'Italy has participated since 1956 and won three times: in 1964, 1990, and 2021.',
        'Sweden': 'Sweden has participated since 1958 and won six times, making it one of the most successful countries in Eurovision history.',
        'Turkey': 'Turkey first participated in Eurovision in 1975. Their first win was in 2003 with Sertab Erener\'s song "Every Way That I Can".',
        'Greece': 'Greece first participated in Eurovision in 1974. Their first win was in 2005 with Helena Paparizou\'s song "My Number One".'
    }
    return country_info.get(country_name, "No information available for this country.")

@bot.command()
async def country(ctx, country_name=None):
    if not country_name:
        await ctx.send("You need to specify the country name. Usage: `!country <country_name>`")
        return
    country_info = get_country_eurovision_info(country_name)
    await ctx.send(country_info)

def get_performance_video(year, country):
    video_links = {
        ('2021', 'Italy'): 'https://www.youtube.com/watch?v=video_link_here',
        ('2019', 'Netherlands'): 'https://www.youtube.com/watch?v=video_link_here',
        ('2003', 'Turkey'): 'https://www.youtube.com/watch?v=5EZquD8JXro'
    }
    return video_links.get((year, country), "Video link not available.")

@bot.command()
async def video(ctx, year=None, country=None):
    if not year or not country:
        await ctx.send("You need to specify both the year and the country. Usage: `!video <year> <country>`")
        return
    video_link = get_performance_video(year, country)
    await ctx.send(video_link)

bot.run(token)  # Tokeninizi buraya yazın
