# Environment Variables Reference

Every environment variable the code reads, with its real default (from `info.py` unless stated) and what it actually does.

---

## ⚠️ Read this first

### 1. A `.env` file is NOT loaded automatically
No code calls `load_dotenv()`. Set these as **real environment variables**: in your hosting panel (Heroku/Koyeb/Render config vars), in `docker-compose.yml` under `environment:`, or exported in the shell before `python bot.py`. `env.example` is only a template.

### 2. On/off values
Every on/off switch is read with `is_enabled()`:

| To turn ON | To turn OFF | Not set, empty or a typo |
|---|---|---|
| `True`, `yes`, `1`, `enable`, `y` | `False`, `no`, `0`, `disable`, `n` | the default shown below |

(Fixed 24 Nov 2026: 15 switches used to be read with `bool()`, where even `False` meant ON.)

### 3. List values
Values marked "space-separated" take several items separated by spaces, e.g. `ADMINS=111111 222222`.
Channel/group IDs are negative and start with `-100` (e.g. `-1001234567890`).

---

## 1. Required (the bot will not start or work without these)

| Variable | Default | Description |
|---|---|---|
| `API_ID` | *(none: crashes if missing)* | Telegram API ID from <https://my.telegram.org>. Must be a number. |
| `API_HASH` | `""` | Telegram API hash from my.telegram.org. |
| `BOT_TOKEN` | `""` | Bot token from @BotFather. |
| `DATABASE_URI` | `""` | MongoDB connection string. Also used for the `admin_database` (search stats) and referral data. |
| `ADMINS` | `""` | Space-separated user IDs of bot admins. Required for every 👑 command. |
| `LOG_CHANNEL` | `-100` | Channel for start-up messages, new users/groups, PM messages, error logs. **Also the channel the streaming server reads files from** (see `BIN_CHANNEL`). Bot must be admin. |

---

## 2. Bot identity & database

| Variable | Default | Type | Description |
|---|---|---|---|
| `SESSION` | `royal_search` | text | Pyrogram session name (the `.session` file name). |
| `DATABASE_NAME` | `Cluster0` | text | MongoDB database name for users, groups and files. |
| `COLLECTION_NAME` | `royal_files` | text | Collection that stores indexed files. Use a different name per bot if bots share a DB. |
| `MULTIPLE_DB` | `False` | flag | Use a second MongoDB. New files go to DB2 once DB1 reaches ~407 MB. |
| `DATABASE_URI2` | `""` (falls back to `DATABASE_URI` when `MULTIPLE_DB` is off) | text | Second MongoDB URI. Required if `MULTIPLE_DB=True`. |
| `MULTI_TOKEN1`, `MULTI_TOKEN2`, … | *(none)* | text | Extra bot tokens used only to share the load of the streaming web server. Any variable whose name starts with `MULTI_TOKEN` is used (`dreamxbotz/util/config_parser.py`). |

---

## 3. Channels & groups

| Variable | Default | Description |
|---|---|---|
| `CHANNELS` | `-100` | Space-separated channels to **auto-index**: every document/video/audio posted there is saved to the DB. |
| `BIN_CHANNEL` | `-100` | Where files are copied to create stream/download links; also receives "#NoResults" logs. ⚠️ The stream page reads `BIN_CHANNEL` but the video data is read from `LOG_CHANNEL` (`custom_dl.py:52`), so set **both to the same channel** or streaming fails (bug B3). |
| `PREMIUM_LOGS` | `-100` | Logs for premium added, expired, `/plan` views, Stars purchases, redeems. |
| `DELETE_CHANNELS` | `-100` | Space-separated channels: any file posted there is **deleted** from the DB. |
| `SUPPORT_CHAT_ID` | `-100` (becomes "not set") | Group ID treated as support group: searches there show a count only, and `/request` works only there. |
| `REQST_CHANNEL_ID` | `-100` (becomes "not set") | Channel where `/request` posts go (with admin action buttons). If unset, requests go to each admin by PM. |
| `SUPPORT_CHAT` | `https://t.me/` | Link on the "Support" button shown when a disabled group tries to use the bot. |
| `AUTH_CHANNELS` | `-100` (ignored) | Space-separated **force-subscribe** channels. Users must join before getting files. Premium users skip this. |
| `AUTH_REQ_CHANNELS` | `-100` (ignored) | Space-separated **request-to-join** force-sub channels (a join request counts as subscribed). |
| `INDEX_REQ_CHANNEL` | same as `LOG_CHANNEL` | Where index requests from non-admins are sent for approval. |
| `LOG_VR_CHANNEL` | `-100` | Default verification log channel for groups (groups can override with `/set_log_channel`). |
| `LOG_API_CHANNEL` | `-100` | Receives a log whenever caption, tutorial, shortener (**including the API key**), log channel, verify times or fsub are changed for a group. Since the owner lock only `ADMINS` can make these changes. |
| `MOVIE_UPDATE_CHANNEL` | `-100` | Channel that receives "New Movie/Series Added" posts. |

---

## 4. Links & branding

| Variable | Default | Used in |
|---|---|---|
| `GRP_LNK` | `https://t.me/+tMAyQb3wxmsyMjg1` | "Request here" / "Search here" buttons. |
| `OWNER_LNK` | `https://t.me/Escanor_24` | "Send payment screenshot", "Bot Owner", "Contact support", "Request to admin" buttons; Developer link in About. |
| `UPDATE_CHNL_LNK` | `https://t.me/+tMAyQb3wxmsyMjg1` | "Join Updates Channel" button under every file. |
| `TUTORIAL` | `https://t.me/+tMAyQb3wxmsyMjg1` | Default "How to verify" link (verification 1). |
| `TUTORIAL_2` | same as above | Default tutorial link for verification 2. |
| `TUTORIAL_3` | same as above | Default tutorial link for verification 3. |
| `MSG_ALRT` | `Share & Support Us ♥️` | Toast text shown on many button presses. |
| `CUSTOM_FILE_CAPTION` | `Script.CAPTION` (HanakoMovix caption, links to your updates channel) | Caption on every sent file. Placeholders: `{file_name}`, `{file_size}`, `{file_caption}`. Groups can override with `/set_caption`. |
| `IMDB_TEMPLATE` | `Script.IMDB_TEMPLATE_TXT` | Default IMDb result template (groups can override with `/set_template`). |

> Many other names and links are **hardcoded** (not env variables): see section 6 of `SECURITY_AUDIT.md`.

---

## 5. Images

| Variable | Default | Used for |
|---|---|---|
| `IMG_BASE_URL` | `https://escanor244.github.io/telegram-bot-resources/hanako-movix-images` | Folder every image link below is built from. Change it per bot. |
| `PICS` | `IMG_BASE_URL/start1-4.jpg` (space-separated) | Random photo on `/start`. |
| `SUBSCRIPTION` | `IMG_BASE_URL/premium.jpg` | Premium menus, `/myplan`, `/start premium`. |
| `FSUB_PICS` | `IMG_BASE_URL/fsub1.jpg fsub2.jpg` | Force-subscribe prompt. |
| `MELCOW_PHOTO` | `IMG_BASE_URL/welcome.jpg` | Group welcome message. |
| `VERIFY_IMG` | `IMG_BASE_URL/verify.jpg` | "Verification complete" message. |
| `QR_CODE` | `Your_Qr_Code` | Link to your payment QR image (premium text and Donation page). |
| `NOR_IMG` | `IMG_BASE_URL/start1.jpg` | ❌ Not used anywhere. |
| `SPELL_IMG` | `IMG_BASE_URL/start1.jpg` | ❌ Not used anywhere. |

---

## 6. Payments & premium

| Variable | Default | Description |
|---|---|---|
| `OWNER_UPI_ID` | `ɴᴏ ᴀᴠᴀɪʟᴀʙʟᴇ ʀɪɢʜᴛ ɴᴏᴡ` | UPI ID shown in UPI/premium/donation screens. |
| `QR_CODE` | `Your_Qr_Code` | See Images. |
| `PREMIUM_USER` | `""` | ❌ Parsed but not used anywhere; use `/add_premium`. |
| *(code only)* `STAR_PREMIUM_PLANS` | `{10:"7day", 20:"15day", 40:"1month", 55:"45day", 75:"60day"}` | Stars price → duration. Not an env var; edit `info.py:65`. Prices in the text are in `Script.PREMIUM_TEXT`. |

---

## 7. Verification (URL shortener gate)

| Variable | Default | Type | Description |
|---|---|---|---|
| `IS_VERIFY` | `False` | flag | Default verification ON/OFF for groups (`/verify on\|off` overrides per group). Premium users skip verification. |
| `SHORTENER_WEBSITE` | `https://api.gplinks.com` | text | 1st shortener domain. |
| `SHORTENER_API` | `Your_Shortener_API` (placeholder) | text | 1st shortener API key. Set your own in env. |
| `SHORTENER_WEBSITE2` | `Shortner_Website_Url` | text | 2nd shortener domain. |
| `SHORTENER_API2` | `yei5ei5eie6id6d` (placeholder) | text | 2nd shortener API key. |
| `SHORTENER_WEBSITE3` | `Shortner_Website_Url` | text | 3rd shortener domain. |
| `SHORTENER_API3` | `5353e68e866ee` (placeholder) | text | 3rd shortener API key. |
| `TWO_VERIFY_GAP` | `1200` (20 min) | seconds | Time after the 1st verify before the 2nd is required. Groups: `/set_time`. |
| `THREE_VERIFY_GAP` | `54000` (15 h) | seconds | Time after the 2nd verify before the 3rd is required. Groups: `/set_time_2`. |

Verification resets daily at midnight (Asia/Kolkata).

---

## 8. Search & result behaviour

| Variable | Default | Type | Description |
|---|---|---|---|
| `AUTO_FFILTER` | `True` | flag | Answer search text in groups. |
| `PM_SEARCH` | `True` | flag | Default for searching in bot PM (overridden by `/pm_search`). |
| `USE_CAPTION_FILTER` | `True` | flag | Also match the search against file captions (not only file names). |
| `SAVE_CAPTION` | `True` | flag | Store captions when indexing (stored as `INDEX_CAPTION`). Saves DB space when off. |
| `ULTRA_FAST_MODE` | `False` when unset | flag | Faster search that skips counting total results (pages show "1, 2…" instead of "1/10"). |
| `MAX_BTN` | `True` | flag | Default "Max buttons": 10 results per page when on, `MAX_B_TN` when off. |
| `MAX_B_TN` | `5` | number | Results per page when Max buttons is off. |
| `BUTTON_MODE` | `False` | flag | Default result style: buttons (True) or text links in the message (False). |
| `SPELL_CHECK_REPLY` | `True` | flag | Default spell-check / "did you mean" for groups. |
| `NO_RESULTS_MSG` | `True` | flag | Log failed searches to `BIN_CHANNEL`. |
| `IMDB` | `False` | flag | Default "IMDB poster" setting for groups. ⚠️ Enabling it currently crashes searches (bug B1). |
| `TMDB_ON_SEARCH` | `False` | flag | Use the TMDB-based lookup for search results / spell-check. ⚠️ Sends your key to a third-party API (R2). |
| `LONG_IMDB_DESCRIPTION` | `False` | flag | Use the long plot outline instead of the short plot. |
| `MAX_LIST_ELM` | *(none)* | number | Limit how many items (cast, languages…) are shown in IMDb lists. |
| `AUTO_DELETE` | `True` | flag | Default: auto-delete search results after `DELETE_TIME`. |
| `DELETE_TIME` | `300` (5 min) | seconds | How long sent files, results and some prompts stay before being deleted. e.g. `7200` = 2 hours. |
| `PROTECT_CONTENT` | `False` | flag | Default "File secure": stops users forwarding/saving sent files. |
| `MELCOW_NEW_USERS` | `False` | flag | Default "Welcome message" for groups. |
| `EMOJI_MODE` | `False` | flag | React with a random emoji to every message. |
| `P_TTI_SHOW_OFF` | `False` | flag | Stored as the group's `botpm` setting and printed at start-up, but no code acts on it. |
| `SKIP` | `2` | number | Default first message ID when indexing a channel (also `/setskip`). |
| `CACHE_TIME` | `300` | number | ❌ Not used anywhere. |
| `AUTH_USERS` | `""` | space-separated | ❌ Not used anywhere. |
| `BATCH_FILE_CAPTION` | same as `CUSTOM_FILE_CAPTION` | text | ❌ Not used anywhere. |

---

## 9. Movie-update channel posts

Posts "New Movie/Series Added" to `MOVIE_UPDATE_CHANNEL` when files arrive in `CHANNELS`.

| Variable | Default | Type | Description |
|---|---|---|---|
| `MOVIE_UPDATE_NOTIFICATION` | `False` | flag | Default ON/OFF (overridden by `/movie_update on\|off`, which is saved in DB). |
| `DREAMXBOTZ_IMAGE_FETCH` | `True` | flag | Download and resize the poster before posting. |
| `LINK_PREVIEW` | `False` | flag | Post as text with a link preview instead of a photo. |
| `ABOVE_PREVIEW` | `True` | flag | Show the link preview above the text. |
| `TMDB_POSTER` | `False` | flag | Use TMDB data/posters (falls back to IMDb on error). ⚠️ Third-party API (R2). |
| `LANDSCAPE_POSTER` | `True` | flag | Use the wide backdrop image instead of the tall poster (with TMDB). |
| `TMDB_API_KEY` | `""` | text | Your TMDB key (<https://www.themoviedb.org/settings/api>). Currently forwarded to the third-party API. |

---

## 10. Streaming / web server

| Variable | Default | Type | Description |
|---|---|---|---|
| `STREAM_MODE` | `False` | flag | Show "Fast Download / Watch Online" buttons under files. |
| `PREMIUM_STREAM_MODE` | `False` | flag | With `STREAM_MODE`: only premium users get stream links. |
| `PORT` | `8080` | number | Web server port (always binds `0.0.0.0`). |
| `FQDN` | `0.0.0.0` (from `WEB_SERVER_BIND_ADDRESS`) | text | Public domain used to build stream links, e.g. `mybot.example.com` (no `https://`). **Must be set** for streaming. |
| `HAS_SSL` | `True` | flag | `https://` (True) vs `http://` (False) in links. |
| `NO_PORT` | `False` | flag | No real effect (the URL is rebuilt from `HAS_SSL` afterwards). |
| `WEB_SERVER_BIND_ADDRESS` | `0.0.0.0` | text | Only used as the fallback for `FQDN`; the server always binds `0.0.0.0`. |
| `APP_NAME` | *(none)* | text | Heroku app name. On Heroku without `FQDN`, links use `<APP_NAME>.herokuapp.com`. |
| `DYNO` | *(set by Heroku)* | — | Its presence switches on Heroku mode (enables `PING_INTERVAL` pinging). Don't set manually. |
| `PING_INTERVAL` | `1200` | seconds | Self-ping interval on Heroku. (A separate keep-alive ping to `URL` also runs every 298 s on all hosts.) |
| `SLEEP_THRESHOLD` | `60` | seconds | FloodWait auto-sleep limit for the extra `MULTI_TOKEN` clients only. |
| `WORKERS` | `4` | number | ❌ Not used (the main client is hardcoded to 60 workers). |
| `SESSION_NAME` | `HanakoMovix` | text | ❌ Not used. |
| `name` | `HANAKOMOVIX` | text | ❌ Not used. |

---

## 11. Variables in deployment files that the code ignores
| Where | Variable | Note |
|---|---|---|
| `docker-compose.yml` | `HEROKU_API_KEY` | Never read. |
| `app.json` | `AUTH_CHANNEL` | Fixed: now `AUTH_CHANNELS` and `AUTH_REQ_CHANNELS` (Heroku only). |

---

## 12. Minimal working example

The full commented template for Render is `env.example`.

```env
API_ID=1234567
API_HASH=0123456789abcdef0123456789abcdef
BOT_TOKEN=1234567890:AA...
DATABASE_URI=mongodb+srv://user:pass@cluster.mongodb.net/?retryWrites=true&w=majority
ADMINS=111111111
LOG_CHANNEL=-1001111111111
BIN_CHANNEL=-1001111111111        # same as LOG_CHANNEL so streaming works
CHANNELS=-1002222222222
PREMIUM_LOGS=-1001111111111
GRP_LNK=https://t.me/your_group
UPDATE_CHNL_LNK=https://t.me/your_channel
OWNER_LNK=https://t.me/your_username
TUTORIAL=https://t.me/your_tutorial
SHORTENER_WEBSITE=your-shortener.com
SHORTENER_API=your_own_key
CUSTOM_FILE_CAPTION=<b>{file_name}</b>
FQDN=your-domain.com
```
