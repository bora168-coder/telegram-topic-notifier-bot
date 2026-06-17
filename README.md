# Telegram Topic Notifier Bot

A simple Python bot that helps developers find the `chat_id` and `message_thread_id` of a Telegram group topic.

The bot can also send messages directly to a selected topic inside a Telegram forum group.

## Purpose

Telegram groups can use **Topics** to separate conversations.

When a bot sends a notification, it must know:

* The Telegram group `chat_id`
* The topic `message_thread_id`
* The Telegram bot token

This project helps you get these values and use them to send notifications to the correct Telegram topic.

## Features

* Read updates received by a Telegram bot
* Find the numeric Telegram group `chat_id`
* Find the topic `message_thread_id`
* Check whether a message was sent inside a topic
* Send notifications to a specific Telegram topic
* Support a single image URL or multiple image URLs
* Handle Telegram API errors and connection timeouts
* Load sensitive settings from environment variables

## How Telegram Topics Work

A Telegram forum group contains different topics.

Each topic has its own `message_thread_id`.

Example:

```text
Telegram Group
├── General
├── System Alerts
├── Application Logs
└── Server Monitoring
```

To send a message to the **System Alerts** topic, the bot needs both:

```text
chat_id = -1001234567890
message_thread_id = 25
```

The `chat_id` identifies the group.

The `message_thread_id` identifies the topic inside that group.

## Requirements

* Python 3.9 or later
* A Telegram bot
* A Telegram supergroup with Topics enabled
* The bot added to the group
* Permission for the bot to send messages

## Create a Telegram Bot

1. Open Telegram.
2. Search for `@BotFather`.
3. Send:

```text
/newbot
```

4. Follow the instructions.
5. Copy the bot token.

Keep the token private. Do not upload it to GitHub.

## Enable Topics in a Telegram Group

1. Open your Telegram group.
2. Open the group settings.
3. Enable **Topics**.
4. Add your Telegram bot to the group.
5. Give the bot permission to send messages.
6. Create or open the topic where the bot should send notifications.

## Installation

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/telegram-topic-notifier-bot.git
```

Open the project folder:

```bash
cd telegram-topic-notifier-bot
```

Create a virtual environment:

```bash
python -m venv venv
```

Activate it on Windows PowerShell:

```powershell
.\venv\Scripts\Activate.ps1
```

Activate it on Linux or macOS:

```bash
source venv/bin/activate
```

Install the required packages:

```bash
pip install -r requirements.txt
```

## Environment Variables

Create a `.env` file in the project root:

```env
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=-1001234567890
TELEGRAM_MESSAGE_THREAD_ID=25
```

Do not commit the `.env` file to GitHub.

Add this line to `.gitignore`:

```text
.env
```

## Find the Chat ID and Topic ID

Send a message inside the Telegram topic, for example:

```text
/get_topic_id
```

Then run:

```bash
python get_topic_id.py
```

The script should display information similar to this:

```text
Group name: Notification Group
Chat ID: -1001234567890
Message ID: 50
Message thread ID: 25
Message text: /get_topic_id
```

Use:

```text
Chat ID: -1001234567890
Message thread ID: 25
```

Do not use `message_id` as the topic ID.

## Telegram API Response Example

Telegram may return an update like this:

```json
{
  "update_id": 123456789,
  "message": {
    "message_id": 50,
    "message_thread_id": 25,
    "chat": {
      "id": -1001234567890,
      "title": "Notification Group",
      "type": "supergroup",
      "is_forum": true
    },
    "text": "/get_topic_id"
  }
}
```

Important fields:

```text
message.chat.id
message.message_thread_id
```

## Send a Message to a Topic

Example:

```python
from telegram_client import send_telegram_message

result = send_telegram_message(
    chat_id=-1001234567890,
    bot_token="YOUR_BOT_TOKEN",
    text="The application is running normally.",
    message_thread_id=25
)

print(result)
```

The notification will be sent directly to the selected topic.

## Project Structure

```text
telegram-topic-notifier-bot/
├── src/
│   ├── __init__.py
│   ├── telegram_client.py
│   └── topic_finder.py
├── tests/
│   └── test_telegram_client.py
├── .env.example
├── .gitignore
├── get_topic_id.py
├── requirements.txt
└── README.md
```

## Important Difference

Use `message_thread_id` to send a message to a topic:

```python
params["message_thread_id"] = topic_id
```

Do not use only:

```python
params["reply_to_message_id"] = message_id
```

`message_thread_id` selects a Telegram topic.

`reply_to_message_id` replies to one existing message.

## Common Errors

### No updates found

Send a new message inside the topic and run the script again.

### Bot cannot send messages

Check that the bot:

* Is a member of the group
* Has permission to send messages
* Has not been blocked or removed

### Invalid message thread ID

Make sure you copied `message_thread_id`, not `message_id`.

### Chat not found

Make sure:

* The group ID is numeric
* The group ID starts with `-100`
* The bot is inside the group
* The bot has the required permission

### Conflict: terminated by another getUpdates request

This normally means another bot process is already using `getUpdates`.

Stop the other process, then run the script again.

## Security

Never store these values directly in public source code:

* Telegram bot token
* Private group information
* Production environment settings

Use environment variables or a `.env` file.

If a bot token is exposed publicly, open BotFather and create a new token.

## Possible Future Improvements

* Add a `/topic_id` bot command
* List all detected group topics
* Save topic IDs in a configuration file
* Support Telegram webhooks
* Add Docker support
* Add logging
* Add automated tests
* Send photos using the Telegram `sendPhoto` method

## License

This project can use the MIT License.
