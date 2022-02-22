# Telegram_TODO-bot
import telebot as t
import random as r
import time

token = "5196554118:AAFmp2s9bujMiB33RV0edCLBU98MPij1R_o"

bot = t.TeleBot(token)

RANDOM_TASKS = ["Заняться спортом", "Посмотреть сериал", "Пойти погулять", "Почитать книгу"]

HELP = """
Список доступных комманд:
/help - справка
/new, /add - добавить задачу в список дел
Пример: /add 21.02.12 полить цветы
/list, /tasks - выывести список задач на заданную дату
Пример: /tasks 21.02.12
/random - добавить случайную задачу на сегодня
/exit - выход"""

todo = {}

@bot.message_handler(commands=['start'])
def start_message(message):
    bot.send_message(message.from_user.id, "Привет! Для просмотра справки используй /help")

@bot.message_handler(commands=["help"])
def help(message):
    bot.send_message(message.chat.id, HELP)

def add_todo(date, task):
    if date in todo:
        todo[date].append(task)
    else:
        todo[date] = [task]

@bot.message_handler(commands=["new", "add"])
def new(message):
    command = message.text.split(maxsplit=2)
    date = command[1].lower()
    task = command[2]
    text = "Задача " + task + " добавлена на " + date
    add_todo(date, task)
    bot.send_message(message.chat.id, text)

@bot.message_handler(commands=["random"])
def random_new(message):
    date = "сегодня"
    task = r.choice(RANDOM_TASKS)
    text = "Задача " + task + " добавлена на " + date
    add_todo(date, task)
    bot.send_message(message.chat.id, text)

@bot.message_handler(commands=["list", "tasks"])
def list(message):
    command = message.text.split(maxsplit=1)
    date = command[1].lower()
    text = ""
    if date in todo:
        text = date.upper() + "\n"
        for task in todo[date]:
            text = text + "-" + task + "\n"
    else:
        text = "Задач на эту дату нет."
    bot.send_message(message.chat.id, text)

@bot.message_handler(content_types=['text'])
def check(message):
    text = "/help"
    if message.text != text:
        bot.send_message(message.from_user.id, "Я не понимаю :( Для просмотра справки используй /help")

bot.polling(none_stop=True)
