# Security & Malware Audit

**Scope:** every file in this repository (55 tracked files, ~15,000 lines), plus `.git/config` and `.git/hooks`.
**Method:** manual line-by-line reading of every file, then cross-repo searches to confirm nothing was missed.
**Rules followed:** no code was executed, no repo command or script was run, and no source file was modified.

---

## 1. Verdict

**No malware was found.**

The audit specifically looked for and did **not** find:

| Check | Result |
|---|---|
| Obfuscated code (base64/hex/`marshal`/`zlib` payloads, `chr()` chains) | None. The only `base64` use is Telegram file-ID encoding (`ia_filterdb.py`, `commands.py:388`). |
| Dynamic code execution (`eval`, `exec`, `compile`, `__import__`) | None. `bot.py:52-54` uses `importlib` only to load the repo's own `plugins/*.py`. |
| Shell / OS command execution (`subprocess`, `os.system`, `os.popen`) | None. The only `os.exec*` is `commands.py:1002` (`os.execl`), which restarts the bot itself. It is admin-only (`/restart`). |
| Hidden backdoor admins (hardcoded user IDs granted admin/premium) | None. Every admin check uses the `ADMINS` env variable. |
| Credential / token exfiltration (bot token, DB URI, session sent elsewhere) | None. Tokens are only used to connect to Telegram and your MongoDB. |
| Sockets, ctypes, keyloggers, crypto-miners | None. |
| Malicious git hooks | None. All 14 files in `.git/hooks/` are inactive `*.sample` files. `.git/config` points only to `github.com/Escanor244/telegram-filter-bot`. |
| Hidden files (`.env`, dot-files) | None in the working tree. |

### Outbound network calls (complete list)

| File:line | Destination | Purpose | Concern |
|---|---|---|---|
| `utils.py:369` | `google.com/search` | Spell-suggestion scraping (function `search_gagala`, never called) | None |
| `plugins/Dreamxfutures/Imdbposter.py:33` | Poster image URL | Download and resize posters | None |
| `plugins/Dreamxfutures/Imdbposter.py:143` | **`bharath-boy-api.vercel.app`** | Movie metadata | **See R2** |
| `plugins/Dreamxfutures/telegraph.py:21` | **`api.imgbb.com`** with a hardcoded key | `/img` uploads | **See R3** |
| `plugins/__init__.py:43`, `dreamxbotz/util/keepalive.py:16` | Your own `URL` | Keep-alive ping | None |
| `dreamxbotz/util/render_template.py:34` | Your own `URL` | Read file size for download page | None |
| `imdb` (Cinemagoer library) | imdb.com | IMDb lookups | See R5 |
| Pyrogram / Motor | Telegram / your MongoDB | Normal operation | None |

### Local file writes (complete list)

Only temporary or report files: `DreamXlogs.txt` (log), `users.txt`, `chats.txt`, `usersplan.txt`, `reason.txt`, `junk.txt`, and downloaded media in `/img` and `/info`, which are deleted after use. Nothing writes outside the working directory.

---

## 2. Risks that benefit or expose third parties (not malware, but should be fixed)

| ID | Severity | Location | Issue |
|---|---|---|---|
| **R1** ✅ FIXED (24 Nov 2026: default replaced with placeholder `Your_Shortener_API`) | High | `info.py:108` | Default `SHORTENER_API = "2469484d258897da1dc9edaf4face6f466301f39"` (gplinks). If you don't set your own key, every verification link is shortened with **someone else's account**, so they likely receive the earnings. `SHORTENER_API2/3` defaults (`yei5ei5eie6id6d`, `5353e68e866ee`) are junk placeholders. The same defaults are re-applied by "Reset settings" (`commands.py:1244-1261`) and by the shortener error fallback (`commands.py:1119-1121`). |
| **R2** | High | `plugins/Dreamxfutures/Imdbposter.py:138-143` | `get_movie_detailsx()` sends **your `TMDB_API_KEY`** plus every search query to `https://bharath-boy-api.vercel.app/api/movie-posters`, a private third-party server, not TMDB. Active when `TMDB_POSTER` (movie-update posts) or `TMDB_ON_SEARCH` (search results) is enabled. |
| **R3** ✅ FIXED (24 Nov 2026: file deleted, commands removed) | Medium | `plugins/Dreamxfutures/telegraph.py:6` | Hardcoded `IMGBB_API_KEY = "d4cc3d793cb68b2c6cdc2197588e895c"`. Every image uploaded via `/img`, `/cup` or `/telegraph` goes into **someone else's imgbb account**, and that person can see the uploads. Anyone can use the command. |
| **R4** | Medium | `dreamxbotz/template/dl.html:9-10,207`; `req2.html:9-10,205` | The stream/download web pages load CSS and JavaScript from personal hosts: `biisal.github.io/Resources/StreamJs.js` (dl.html) and `streamlink-two.vercel.app/548/551/Streams.js` (req2.html). Whoever controls those hosts can change the script running on your pages at any time. `req2.html` is not used by any code; only `req.html` (video/audio) and `dl.html` (other files) are rendered. `controlKit.js@master` and unversioned `sheryjs` are also unpinned. |
| **R5** | Low | `requirements.txt:41` | `git+https://github.com/Joelkb/cinemagoer` installs an unpinned personal fork of Cinemagoer. A future commit there would be installed automatically. |
| **R6** | Low | `commands.py:1113-1118` | When a group admin runs `/set_shortner`, their shortener **API key is posted to `LOG_API_CHANNEL`**. That channel is yours, but the bot collects other people's keys there. |

---

## 3. Security weaknesses in the bot's own logic

| ID | Location | Issue |
|---|---|---|
| S1 | `pmfilter.py:926-931` | The `autofilter_delete` callback (drops **all** indexed files) has no admin check of its own. It relies on only admins seeing the `/deleteall` button. **FIXED (24 Nov 2026): `/deleteall` and its button were removed completely.** |
| S2 | `dreamxbotz/template/req.html:1054,1096-1097` + `render_template.py:38` | The file name is placed inside JavaScript strings (`'{{file_name}}'`), and `jinja2.Template` is created without autoescaping. A file name containing `'` or `</script>` could inject script into the stream page (XSS). File names come from your indexed channels. |
| S3 | `commands.py:629-634` | `/reload` used in a group has no admin check, so any member can link that group to their PM settings list. Actual settings changes still check admin rights. |
| S4 | `pmfilter.py:873,1000,1023` | Admin checks use `str(userid) in ADMINS`, but `ADMINS` holds integers, so this check is always False (it only matters as a fallback). |
| S5 | `index.py:19-51` | The `index#accept` callback has no admin check. The button is only shown in the index-request channel and to admins. |

---

## 4. Functional bugs found during the audit

| ID | Location | Bug |
|---|---|---|
| B1 | `pmfilter.py:1903,1977` | `TMDB_POSTERS` is used but **never defined** (info.py defines `TMDB_POSTER`). Group search results crash with a `NameError` when the group's IMDB setting is on. |
| B2 | `info.py:29,30,86,88-90,92,93,148,159,160,162,163,206,227` | `bool(environ.get(...))`: any non-empty string, including `"False"`, becomes `True`. Affects `USE_CAPTION_FILTER`, `SAVE_CAPTION`, `MOVIE_UPDATE_NOTIFICATION`, `DREAMXBOTZ_IMAGE_FETCH`, `LINK_PREVIEW`, `ABOVE_PREVIEW`, `TMDB_POSTER`, `LANDSCAPE_POSTER`, `NO_RESULTS_MSG`, `PM_SEARCH`, `EMOJI_MODE`, `STREAM_MODE`, `PREMIUM_STREAM_MODE`, `NO_PORT`, `HAS_SSL`. |
| B3 | `custom_dl.py:53` vs `pmfilter.py:1396` / `render_template.py:15` | Stream links copy files into `BIN_CHANNEL`, but the byte streamer reads from `LOG_CHANNEL`. Streaming/download only works if both are the same channel. |
| B4 | `commands.py:187,190` | Referral reward at 100 points crashes: `message.reply_text(user_id, ...)` uses the wrong signature, and `datetime.datetime.now()` fails because `datetime` is already the class. |
| B5 | `Premium.py:178` | The "Free Trial" button in `/plan` uses `callback_data='free'`, which has no handler (the menu from `/start` correctly uses `give_trial`). |
| B6 | `Script.py:423-424,521-522` | Help text advertises `/remove_fsub` and `/reset_group`, which don't exist as commands. Reset is only available as a button in `/details`. |
| B7 | `Premium.py:258` | `message.user.mention` should be `message.from_user.mention`. The premium log after a Stars payment fails (the user still gets premium). |
| B8 | `utils.py:126,153` | FloodWait retries call `users_broadcast(user_id, message)` / `groups_broadcast(...)` without the required `is_pin` argument, so they raise a `TypeError`. |
| B9 | `dreamxbotz/Bot/clients.py:40` | `MULTI_CLIENT = True` sets a local variable, so the global flag never changes (logging only). |
| B10 | No `.env` loader | `python-dotenv` is in requirements but never called. A local `.env` file is **not** read; values must be real environment variables (hosting panel, Docker `environment:`, or exported in the shell). |
| B11 | `docker-compose.yml:14` | Passes `HEROKU_API_KEY`, which no code reads. |
| B12 ✅ FIXED (24 Nov 2026) | `app.json:53` | Declared `AUTH_CHANNEL`; the code reads `AUTH_CHANNELS`. Now `AUTH_CHANNELS` + `AUTH_REQ_CHANNELS`. |

---

## 5. Licence note

`LICENSE` is the unmodified **GNU AGPL-3.0**. Section 13 requires that if you run a **modified** version as a network service, you must offer users access to its source code. Keep this in mind before rebranding.

---

## 6. Third-party branding, links and credits found

These are the references to other channels, YouTubers or developers. They are the targets for the "make it editable" step.

### Telegram / YouTube links
| Reference | Locations |
|---|---|
| `t.me/+GeFoPGMglA1kNTZl` | `info.py:103-105` (TUTORIAL, TUTORIAL_2, TUTORIAL_3 defaults) |
| `t.me/+tMAyQb3wxmsyMjg1` | `info.py:123,125` (GRP_LNK, UPDATE_CHNL_LNK defaults) |
| `t.me/Escanor_24` / `@Escanor_24` | `info.py:124` (OWNER_LNK default), `Script.py:297` (PREMIUM_STAR_TEXT) |
| `t.me/+ryDE7AwX-Jg3MTA1` | `Script.py:227` (CAPTION: every file caption, twice), `Script.py:288` (premium "proof" link) |
| `t.me/royalkrrishna` | `Script.py:379` (FREE_TXT), `pmfilter.py:1571` (Source Code button) |
| `t.me/dreamxbotz` / `@DREAMXBOTZ` | `commands.py:1066` (example text), comments: `p_ttishow.py:16`, `join_req.py:1` |
| `t.me/dreamcinezone`, `t.me/Contact_dreamcinezone_bot`, YouTube `@dreamcinezone0` | `dl.html:26,98-100,119,146,173,177,181`; `req2.html:24,96,97,104,117,171,175,179` |
| `t.me/+PA8OPL2Zglk3MDM1` | `req2.html:144` |
| `t.me/technokrrishofficial` ("Techno Krrish") | `req.html:8 (title),726,731,742,825,826,838,839,840,850` |
| `github.com/DreamXBotz/Auto_Filter_Bot` | `app.json:18-19` |
| `https://github.com` labelled "Royal" | `Script.py:404` (SOURCE_TXT) |

### Brand names / text
| Reference | Locations |
|---|---|
| "TECHNO KRISH" ASCII logo | `Script.py:258-267` (printed to console on start) |
| `ᴅʀᴇᴀᴍxʙᴏᴛᴢ` fallback "Powered by" | `utils.py:887,895,913,921` |
| `royal_search`, `royal_files` | `info.py:20,78` |
| `dreamXBotz`, `DREAMXBOTZ` | `info.py:218,220`, `route.py:26` (web root JSON reply) |
| `dreamcinezoneXBot` class name | `dreamxbotz/Bot/__init__.py:24` |
| "ALL Movies Studio" / "Dreamcinezone" page titles | `dl.html:7`, `req2.html:7` |
| "Updates" fallback | `pmfilter.py:1959-1968` |

### Developer credit comments
`@bharath_boy` (≈25 comments in `req.html`), `@biisal` (`dl.html`, `req2.html`), `@BeingXAnonymous` (`Premium.py:194`), `@dreamxbotz` (`dreamxbotz/util/*.py` line 1), Eyaadh (`custom_dl.py:29-30`).

### Hardcoded images / stickers (third-party hosted)
| Location | Asset |
|---|---|
| `info.py:34-39,101` | graph.org / telegra.ph defaults for PICS, NOR_IMG, MELCOW_PHOTO, SPELL_IMG, SUBSCRIPTION, FSUB_PICS, VERIFY_IMG |
| `pmfilter.py:131` | Referral image |
| `pmfilter.py:1433` | Premium-stream image |
| `pmfilter.py:1499` | Donation image |
| `pmfilter.py:1551` | Free-trial image |
| `Premium.py:67` | "No plan" image |
| `Premium.py:183` | `/plan` image |
| `commands.py:255` | Force-sub fallback image |
| `req.html:751` | Video poster (`envs.sh`) |
| `req.html:773-819`, `dl.html:60-75` | Player icons (`i.postimg.cc`) |
| `pmfilter.py:1765` | "Searching" sticker ID |
| `check_alive.py:25` | `/alive` sticker ID |

---

## 7. Coverage log

| File | Lines | How verified |
|---|---|---|
| `bot.py`, `info.py`, `Script.py`, `utils.py` | 101 / 283 / 527 / 937 | Read in full |
| `plugins/*.py` (13 files) | 5,583 | Read in full |
| `plugins/Dreamxfutures/*.py` except font table | 665 | Read in full |
| `plugins/Dreamxfutures/fotnt_string.py` | 2,363 | Lines 1-800 read. Then every line was classified by pattern: **2,088** are `'x': 'y'` character mappings and **275** are structure lines (`class Fonts:`, 39 × `def`/`style = {`/`}`/`for`/`replace`/`return`, one blank). 2,088 + 275 = 2,363, so every line is accounted for. A syntax-tree check also found only `style.items()` and `text.replace()` calls. |
| `database/*.py` (4 files) | 922 | Read in full |
| `dreamxbotz/**/*.py` (12 files) | 547 | Read in full |
| `dreamxbotz/template/req.html` | 1,264 | Read in full (1-699 CSS, 700-1264 HTML/JS) |
| `dreamxbotz/template/dl.html`, `req2.html` | 210 / 208 | Read in full |
| `dreamxbotz/template/favicon.ico` | binary | Identified as a genuine 3-image Windows icon (16×16, 32×32) |
| `Dockerfile`, `Procfile`, `docker-compose.yml`, `heroku.yml`, `requirements.txt`, `logging.conf`, `app.json`, `env.example` | — | Read in full |
| `README.md`, `SETUP_COMMANDS_AND_ENV.md`, `LICENSE` | 85 / 142 / 661 | Read in full |
| `.git/config`, `.git/hooks/*` | — | Config read; hooks are all `.sample` (inactive) |

Cross-checks run over all `*.py` files: every `environ`/`getenv` read, every command/callback handler, every `open()`, `requests`/`aiohttp` call, `importlib`, `exec`/`eval`/`compile`, `base64`, `os.exec*`, and all URLs and `t.me`/`@handle` strings in every file.
