# Bot Commands Reference

Every command, button flow and automatic trigger this bot actually handles, taken directly from the code.

**Who can use what**

| Level | Meaning |
|---|---|
| 👤 **Everyone** | Any Telegram user |
| 🛡️ **Group admin** | Owner/admin of the Telegram group where the command is sent (checked live via Telegram) |
| 👑 **Bot admin** | A user ID listed in the `ADMINS` env variable |

Where it says **PM only** or **Group only**, the command is ignored or refused elsewhere.

> All commands start with `/`. `/alive` and `/ping` also accept `.` (e.g. `.alive`).

---

## 1. Everyone 👤

### Getting started & info
| Command | Where | What it does |
|---|---|---|
| `/start` | PM / group | PM: welcome photo with Help / About / Top Searching / Upgrade buttons, and registers you as a user. Group: short welcome with "Add me" and Update Channel buttons; registers the group. |
| `/id` | PM / group | PM: your name, username, Telegram ID, data-centre. Group: chat ID, your ID, replied user's ID, and the file ID of a replied/attached media. |
| `/info [user_id \| @username]` or reply | anywhere | Profile card: name, ID, DC, username, permanent link, join date (supergroups), profile photo. |
| `/imdb <name>` or `/search <name>` | anywhere | Search IMDb and pick a title to see poster, genres, year, rating. |
| `/movies` | **PM only** | List of the 20 latest indexed movie titles. |
| `/series` | **PM only** | List of the latest indexed series with their seasons. |
| `/top_search [count]` | anywhere | Most-searched queries as a reply keyboard (default 20). |
| `/trendlist [count]` | anywhere | Numbered list of top searches (default 31). |
| `/group_cmd` | anywhere | Shows the group-admin command list. |
| `/alive` (`.alive`) | anywhere | "I am alive" sticker and message (auto-deletes after 60 s). |
| `/ping` (`.ping`) | anywhere | Response time in ms (auto-deletes after 60 s). |
| `/system` | anywhere | OS, bot uptime, system uptime, RAM, disk usage (auto-deletes after 60 s). |
| `/stickerid` (reply to sticker) | anywhere | Shows the sticker's file ID and unique ID. |

### Tools
| Command | Where | What it does |
|---|---|---|
| `/font <text>` | **PM only** | Converts text into fancy Unicode font styles (42 style buttons across two pages). |

### Premium
| Command | Where | What it does |
|---|---|---|
| `/plan` | anywhere | Premium menu: Buy Premium (Stars / UPI), Refer Friends, Free Trial. Logged to `PREMIUM_LOGS`. Auto-deletes after 5 min. ⚠️ The "Free Trial" button here does nothing (bug B5); use the one under `/start` → Upgrade. |
| `/myplan` | anywhere | Your premium status: time left and expiry date. |
| `/redeem <code>` | anywhere | Activates a redeem code created by a bot admin. Refused if you already have active premium. |

### Requests
| Command | Where | What it does |
|---|---|---|
| `/request <movie>` or `#request <movie>` (or reply to a message with `/request`) | **Only in the group set as `SUPPORT_CHAT_ID`** | Sends the request to `REQST_CHANNEL_ID` (or to every bot admin if that is unset) with admin action buttons. Minimum 3 characters. |

### Automatic (no command needed)
| Trigger | What happens |
|---|---|
| Any text in a group | **Auto-filter search**. Results appear with Quality / Language / Season filters, "Send All", pagination and "Remove Ads". Links posted by non-admins are deleted. In the `SUPPORT_CHAT_ID` group, the bot only says how many results exist and points to `GRP_LNK`. |
| Any text in PM | Searches if PM search is ON (`/pm_search`); otherwise replies "search in our group" and logs the message to `LOG_CHANNEL`. Messages starting with `#` are ignored. |
| Misspelled search | Tries a fuzzy IMDb match, then shows "did you mean" buttons (if spell-check is ON in the group). |
| Forward a channel post, or send a `t.me/<channel>/<msg_id>` link, in PM | **Index request**. Admins get a "Yes, index" prompt. Others' requests go to `INDEX_REQ_CHANNEL` for approval. |
| Clicking a file button | Opens the bot PM via `/start file_…`, then runs force-subscribe check → verification (if enabled) → sends the file → auto-deletes it after `DELETE_TIME`. |
| Joining a group that has welcome ON | Welcome photo (`MELCOW_PHOTO`), deleted after 10 min if auto-delete is ON. |

### `/start` deep-link payloads (created by the bot's own buttons)
| Payload | Purpose |
|---|---|
| `file_<group_id>_<file_id>` | Deliver one file |
| `allfiles_<group_id>_<key>` | Deliver every file on the current result page |
| `notcopy_…` / `sendall_…` | Return link after completing shortener verification |
| `reff_<user_id>` | Referral join (inviter gets 10 points; at 100 points they get 1 month premium; ⚠️ bug B4 crashes at 100) |
| `premium` | Show plan prices with UPI / QR and the "send screenshot" button |
| `getfile-<movie-name>` | Run a search (used by movie-update channel posts) |
| `subscribe`, `error`, `okay`, `help` | Show the normal start menu |

---

## 2. Group admins 🛡️

All of these must be sent **inside the group** (except `/reload <id>` and settings opened in PM). Changes are logged to `LOG_API_CHANNEL`.

| Command | What it does |
|---|---|
| `/settings` | In a group: choose to open settings here or in PM. In PM: pick one of your connected groups. Toggles: Result page (button/text), File secure, IMDB poster, Welcome msg, Auto delete, Max buttons (10 or `MAX_B_TN`), Spell check, Verify, Remove connection. |
| `/reload` | In a group: link the group to your PM settings list. ⚠️ No admin check (S3). In PM: `/reload <group_id>` (admin check applies). |
| `/details` | Show all current group settings (shorteners, API keys, verify times, tutorials, log channel, fsub, template, caption), with a **Reset Settings** button. Auto-deletes after 5 min. |
| `/set_template <text>` | Custom IMDb result template. Placeholders: `{title} {year} {rating} {genres} {languages} {url} {votes} {runtime} {cast} {director} {plot} {release_date} {countries} {certificates} {kind} {imdb_id} {query}`, etc. |
| `/set_caption <text>` | Custom caption for sent files. Placeholders: `{file_name}`, `{file_size}`, `{file_caption}`. |
| `/set_shortner <site> <api_key>` | 1st verification shortener (e.g. `/set_shortner gplinks.com abc123`). |
| `/set_shortner_2 <site> <api_key>` | 2nd verification shortener. |
| `/set_shortner_3 <site> <api_key>` | 3rd verification shortener. |
| `/set_tutorial <link>` | "How to verify" link for verification 1. |
| `/set_tutorial_2 <link>` | Tutorial link for verification 2. |
| `/set_tutorial_3 <link>` | Tutorial link for verification 3. |
| `/set_time <seconds>` | Gap after the 1st verify before the **2nd** verify is required. |
| `/set_time_2 <seconds>` | Gap after the 2nd verify before the **3rd** verify is required. |
| `/set_log_channel <-100id>` | Channel where this group's verifications are logged. The bot must be admin there. |
| `/set_fsub <id1> [id2 …]` | Up to 5 force-subscribe channels for this group (in addition to global `AUTH_CHANNELS`). |

> ⚠️ `/remove_fsub` and `/reset_group` appear in the bot's help text but **are not implemented** (bug B6). To reset, use the button in `/details`.

---

## 3. Bot admins 👑 (`ADMINS` only)

### Files & indexing
| Command | Where | What it does |
|---|---|---|
| `/delete` (reply to a file) | anywhere | Delete that one file from the database. |
| `/deletefiles <keyword>` | **PM only** | Find all files matching the keyword (e.g. `predvd`, `camrip`) and delete them after confirmation. |
| `/deleteall` | anywhere | Delete **every** indexed file (asks Yes/No). |
| `/setskip <number>` | anywhere | Message ID to start from on the next channel index (default from `SKIP`, i.e. 2). |
| `/del_msg` | anywhere | Clear the movie-update notification history (asks Yes/No). |

Also: forward a channel message or send its link in PM → "Index this channel?" (see Automatic triggers).

### Bot toggles
| Command | Where | What it does |
|---|---|---|
| `/pm_search on\|off` | **PM only** | Allow or block searching in the bot's PM (saved in DB; overrides `PM_SEARCH`). |
| `/movie_update on\|off` | **PM only** | Turn movie-update posts to `MOVIE_UPDATE_CHANNEL` on or off (saved in DB; overrides `MOVIE_UPDATE_NOTIFICATION`). |
| `/verify on\|off` | **Group only** | Turn shortener verification on or off for that group. |
| `/resetallgroup` | **PM only** | Delete custom settings of **all** groups (they fall back to env defaults). |
| `/commands` | anywhere | Publish the bot's command menu (from `Bot_cmds` in `info.py`) to Telegram. |
| `/restart` | anywhere | Restart the bot process. |
| `/logs` | anywhere | Send the error log file `DreamXlogs.txt`. |

### Users & groups
| Command | What it does |
|---|---|
| `/stats` | Users, groups, premium users, file count, DB size/free space (both DBs if `MULTIPLE_DB`), uptime, RAM, CPU. |
| `/users` | List all users (as a file if too long). |
| `/chats` | List all groups (as a file if too long). |
| `/ban <user_id\|@user> [reason]` | Ban a user from the bot. |
| `/unban <user_id\|@user>` | Unban a user. |
| `/leave <chat_id>` | Bot posts a goodbye and leaves the chat. |
| `/disable <chat_id> [reason]` | Ban a group: the bot leaves and refuses to work there again. |
| `/enable <chat_id>` | Re-allow a disabled group. |
| `/invite <chat_id>` | Generate an invite link for a chat where the bot is admin. |
| `/send <user_id>` (reply to a message) | Copy the replied message to one user (only if they have started the bot). |
| `/admin_cmd` | Shows the admin command list. |

### Broadcasts & cleanup
| Command | What it does |
|---|---|
| `/broadcast` (reply to a message) | Send it to all users; asks whether to pin. Live progress with a Cancel button. |
| `/grp_broadcast` (reply to a message) | Send it to all groups; asks whether to pin. |
| `/clear_junk` | Remove deleted or blocked users from the DB. |
| `/junk_group` or `/clear_junk_group` | Remove dead groups from the DB and leave them. |
| `/delreq` | **PM only**. Clear the stored join-request list (request-to-join force-sub). |

### Premium management
| Command | Example | What it does |
|---|---|---|
| `/add_premium <user_id> <n> <unit>` | `/add_premium 123456 1 month` | Give premium. Units: `s`, `min`, `hour`, `day`, `month`, `year`. Notifies the user and `PREMIUM_LOGS`. |
| `/remove_premium <user_id>` | `/remove_premium 123456` | Remove premium and notify the user. |
| `/get_premium <user_id>` | | Show a user's premium expiry and time left. |
| `/premium_users` | | List all active premium users. |
| `/add_redeem <duration> <count>` | `/add_redeem 1day 5` | Generate one-time redeem codes (stored **in memory only**: lost on restart). |
| `/trial_reset [user_id]` | | Reset free-trial eligibility for one user, or everyone if no ID is given. |

---

## 4. Payment flows (buttons)
| Flow | How it works |
|---|---|
| **Telegram Stars** | `/plan` → Buy Premium → Star → choose 10/20/40/55/75 ⭐ → Telegram invoice → premium for 7/15/30/45/60 days is added automatically. Prices are in `STAR_PREMIUM_PLANS` (`info.py:65`). |
| **UPI / QR** | `/plan` → Buy Premium → UPI shows `OWNER_UPI_ID`. The user sends a screenshot to `OWNER_LNK`, then an admin runs `/add_premium`. |
| **Free trial** | `/start` → Upgrade → Free Trial gives 5 minutes of premium, once per user (reset with `/trial_reset`). |
| **Referral** | `/start` → Upgrade → Refer Friends gives a personal `reff_` link. 10 points per new user; 100 points = 1 month (⚠️ bug B4). |
| **Donation** | `/start` → About → Donation shows `QR_CODE` and `OWNER_UPI_ID`. |

---

## 5. Commands listed in old docs that do NOT exist
`/verification`, `/top` (real name: `/top_search`), `/link`, `/batch`, `/remove_fsub`, `/reset_group`.
