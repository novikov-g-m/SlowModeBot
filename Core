import asyncio
from telegram import Update
from telegram.ext import Application, MessageHandler, filters, ContextTypes
from datetime import datetime, timedelta

# === НАСТРОЙКИ ===
BOT_TOKEN = "ВАШ_ТОКЕН_ОТ_BOTFATHER"  # ← Замени на свой
GROUP_ID = -1001234567890             # ← ID группы (с минусом!)
TOPIC_ID = 123                         # ← ID нужной темы (топика)
MESSAGE_COOLDOWN = 10                  # ← Интервал в секундах

# Храним время последнего сообщения каждого пользователя
user_last_message = {}

# === ОБРАБОТКА СООБЩЕНИЙ ===
async def slow_mode_topic(update: Update, context: ContextTypes.DEFAULT_TYPE):
    message = update.message
    if not message or not message.message_thread_id:
        return  # Не в теме — выходим

    # Проверяем, в нужной ли мы теме
    if message.message_thread_id != TOPIC_ID:
        return

    user_id = message.from_user.id
    now = datetime.now()

    # Проверяем, писал ли пользователь недавно
    if user_id in user_last_message:
        last_time = user_last_message[user_id]
        if now - last_time < timedelta(seconds=MESSAGE_COOLDOWN):
            # Удаляем сообщение
            try:
                await message.delete()
            except Exception as e:
                print(f"Не удалось удалить сообщение: {e}")
            # Отправляем предупреждение
            warn_msg = await message.reply_text(
                f"⚠️ Слишком частые сообщения Подождите {MESSAGE_COOLDOWN} секунд."
            )
            # Удаляем предупреждение через 5 секунд
            await asyncio.sleep(5)
            await warn_msg.delete()
            return

    # Обновляем время последнего сообщения
    user_last_message[user_id] = now

# === ЗАПУСК БОТА ===
def main():
    app = Application.builder().token(BOT_TOKEN).build()
    app.add_handler(MessageHandler(filters.Chat(GROUP_ID), slow_mode_topic))
    print("Бот запущен и следит за темой...")
    app.run_polling()

if __name__ == "__main__":
    main()
