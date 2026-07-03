# Setup, Commands, and Premium Guide

This project is a Telegram auto-filter bot with premium features, admin tools, and timed message cleanup.

## 1) What You Need To Set Up

### Core bot env
- `SESSION` - Pyrogram session name
- `API_ID` - Telegram API ID
- `API_HASH` - Telegram API hash
- `BOT_TOKEN` - Bot token from BotFather
- `DATABASE_URI` - MongoDB connection string
- `DATABASE_NAME` - MongoDB database name
- `COLLECTION_NAME` - Main file collection name

### Admin and channel env
- `ADMINS` - one or more admin user IDs
- `CHANNELS` - source channels for indexing
- `LOG_CHANNEL` - log channel ID
- `BIN_CHANNEL` - bin channel ID
- `PREMIUM_LOGS` - premium activity log channel
- `DELETE_CHANNELS` - channels used for delete flow
- `SUPPORT_CHAT_ID` - support group ID
- `REQST_CHANNEL_ID` - request channel ID

### Optional but commonly used env
- `MULTIPLE_DB` - enable second MongoDB database
- `DATABASE_URI2` - second MongoDB URI when `MULTIPLE_DB=True`
- `PORT` - web server port
- `FQDN` - public host/domain for the web server
- `HAS_SSL` - choose `https` or `http`
- `PING_INTERVAL` - keepalive ping interval

### Search, filter, and message behavior
- `CACHE_TIME` - cache lifetime
- `USE_CAPTION_FILTER` - caption-based search toggle
- `SAVE_CAPTION` - save captions to DB
- `AUTO_FFILTER` - auto filter on/off
- `AUTO_DELETE` - auto-delete command/info messages on/off
- `DELETE_TIME` - delete delay in seconds
- `PROTECT_CONTENT` - Telegram content protection flag
- `PM_SEARCH` - allow PM search
- `BUTTON_MODE` - button/link mode
- `STREAM_MODE` - enable stream mode
- `PREMIUM_STREAM_MODE` - premium-only stream mode
- `IMDB`, `TMDB_ON_SEARCH`, `TMDB_API_KEY` - media metadata options

### Verification / shortener env
- `IS_VERIFY` - enable verification
- `SHORTENER_API`, `SHORTENER_WEBSITE`
- `SHORTENER_API2`, `SHORTENER_WEBSITE2`
- `SHORTENER_API3`, `SHORTENER_WEBSITE3`
- `TWO_VERIFY_GAP`, `THREE_VERIFY_GAP`
- `AUTH_REQ_CHANNELS`, `AUTH_CHANNELS`

### Premium / payment env
- `QR_CODE` - QR image for UPI payments
- `OWNER_UPI_ID` - UPI ID for manual payment
- `PREMIUM_USER` - pre-approved premium user IDs
- `STAR_PREMIUM_PLANS` - built-in star pricing map in code

### Branding / links
- `GRP_LNK` - group link
- `OWNER_LNK` - owner contact link
- `UPDATE_CHNL_LNK` - update channel link
- `SUBSCRIPTION`, `PICS`, `MELCOW_PHOTO`, `NOR_IMG`, `SPELL_IMG` - images used in bot messages

## 2) Install And Run

Typical setup flow:

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
python bot.py
```

If you are deploying with Docker, use the provided `Dockerfile` / `docker-compose.yml` / `heroku.yml` files instead of running locally.

## 3) Main Commands And What They Do

### User-facing
- `/start` - open the bot
- `/plan` - open premium plan options
- `/myplan` - check your premium status
- `/stats` - show bot/database stats
- `/alive` - check bot health
- `/info` - show user info
- `/id` - show Telegram ID
- `/settings` - manage group settings where allowed
- `/reload` - reconnect a group to PM settings
- `/set_template` - change group template text
- `/request` - send a request from the support flow

### File/search flows
- `/delete` - admin-only delete one indexed file from the database
- `/deleteall` - admin-only prompt to remove all indexed files
- `/deletefiles` - admin-only remove files by keyword

### Admin broadcast and moderation
- `/broadcast` - send one replied message to all saved users
- `/grp_broadcast` - send one replied message to all saved groups
- `/clear_junk` - remove blocked/deleted users from DB
- `/junk_group` or `/clear_junk_group` - clean junk groups and leave them if needed
- `/send <user_id>` - forward a replied message to a specific user
- `/logs` - send the log file to admin

### Premium management
- `/add_premium <user_id> <time>` - grant premium access
- `/remove_premium <user_id>` - remove premium access
- `/get_premium <user_id>` - inspect a user’s premium status
- `/premium_users` - list all premium users
- `/add_redeem <time> <count>` - create redeem codes
- `/redeem <code>` - redeem a premium code

## 4) Premium Access Path

There are three ways premium is handled in the code:

1. User self-service: open `/plan`, then use the buttons to buy premium with Telegram Stars or UPI.
2. Referral/free-trial flow: the bot shows referral and free-trial options from the premium menu.
3. Admin grant: an admin can directly give access with `/add_premium` or issue redeem codes with `/add_redeem`.

Premium checks are enforced in code through the database, so access is not just a UI toggle. The bot reads the stored expiry time and removes access automatically when it expires.

## 5) Auto-Delete After The Bot Generates Messages

This is already in the project.

- `DELETE_TIME` in `info.py` defaults to `300` seconds, which is 5 minutes.
- Several bot responses are intentionally deleted after `DELETE_TIME` by the code.
- If you want deletion after 2 hours, set `DELETE_TIME=7200` in your deployment environment.
- This is not a Telegram-only setting. Telegram content protection is separate and does not replace the bot’s own timed deletion.

So, if you want 2-hour cleanup, you do need to set it in the project environment or hosting config, not only inside Telegram.

## 6) Quick Notes

- `bot.py` dynamically loads every file in `plugins/*.py` at startup, so every plugin file is executable code.
- Keep admin IDs and channel IDs correct, or admin-only commands will fail.
- If `MULTIPLE_DB=True`, you must provide `DATABASE_URI2` as well.
