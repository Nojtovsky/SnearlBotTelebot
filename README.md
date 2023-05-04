# SnearlBot

Telegram бот для отправки голосовых сообщений, создания стикеров-цитат и запрета репостов из заблокированных чатов.

## Зависимости:
`Python 3.10+`

`python-telegram-bot >= 20.2`

`Pillow >= 9.4.0` - опционально

## Использование
1. Установите Python 3.10+
2. Установите зависимости: `pip install -r requirements.txt`
3. В файл `data/import/token.txt` запишите свой токен бота.
4. Запустите `start_admin_tool.py` и импортируйте токен.
5. Запустить бота можно с помощью `start_bot.py` или командой `python -m snearl`.

### Команды
_(В формате для /setcommands BotFather)_
```
help - Помощь с ботом
info - Информация о чате
block - Внести канал в чёрный список
allow - Разблокировать канал
blacklist - Посмотреть чёрный список
voice_add - Добавить войс
voice_delete - Удалить войс
voicelist - Посмотреть список войсов
quote_add - Добавить цитату
quote_delete - Удалить цитату
quotelist - Посмотреть список цитат
```

### Инлайн
Бот выдает инлайн полный список сохраненных `в`ойсов и `ц`итат. Перейти к отдельной категории можно если ввести выделенную букву в начале запроса.

## Администрирование
Модуль `start_admin_tool.py` содержит некоторые команды недоступные через бота.
Модуль содержит функции импорта/экспорта/удаления данных из базы данных, а также функцию миграции и локального режима.
Инструкции для каждой функции можно прочитать при её запуске.
Для прочих своих запросов редактируйте `data/database.db` через СУБД Sqlite.

### Импорт/экспорт
Импорт производится из директории `data/import`.
Экспорт производится в директорию `data/export`.
Структуры этих двух папок одинаковы:

`token.txt` - содержит токен бота.

`blacklist.json` - список заблокированных чатов и пользователей.

`voicelist/` - иерархия аудио-файлов. Используется в импорте если у вас уже имеются сторонние файлы .ogg.

`quotelist/` - иерархия стикеров. Используется в импорте если у вас уже имеются сторонние файлы .webp.

### База данных и миграция чата
* Войсы и цитаты хранятся привязанными к каждому отдельному чату. Администратор может сменить эту привязку на другой чат вручную.
* При смене типа чата на супергруппу привязка в базе данных меняется автоматически.
* Все данные чата удаляются если бот выходит из него.

### Локальный режим
В этом режиме бот разрешает использовать команды изменяющие базу данных только администраторам и владельцам одного избранного чата.
С выключенным режимом в любом другом чате пользователи смогут создать собственный набор войсов и список блокировок.

## Как добавить новый функциональный модуль
Файлы модуля хранятся в `snearl/module/`. Там же можно посмотреть на примере как модули реализованы.
Помимо этого нужно:
1. Импортировать новый модуль в `snearl/instance.py` в `start_bot()`;
2. Если модуль использует БД, то добавить нужные вызовы функций в `module/dataupdate.py` в `chat_migrate()` и `bot_status_changed()`;
2. Если модуль использует инлайн, то добавить по аналогии в `module/inline_handler.py`;

Администраторские функции обычно выносятся в отдельный файл и:
1. Импортируются в файле `start_admin_tool.py`;
2. В массив `commands` добавляются доступные пользователю команды;
2. В `clear_table()` добавляется возможность удалить все записи из таблицы БД модуля;
