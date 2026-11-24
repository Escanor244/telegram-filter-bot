# Third-party references: full inventory

Scan date: **24 Nov 2026**
Scope: all 53 git-tracked files, every line. The scan covered URLs, `t.me` links, `@usernames`, brand names, API keys, sticker IDs and numeric IDs.
Brand: **HanakoMovix**

Status:
- ✅ **Replaced**: already changed to yours or removed.
- ⏳ **Pending**: waiting on something from you (images, page rebrand) or on a separate decision.
- ➖ **Keep**: not pointing to another person, or needed for the bot to work.

---

## 1. Links / keys pointing to someone else

| # | File:line | Was | Now | Status |
|---|---|---|---|---|
| 1 | `info.py:108` | `SHORTENER_API` = someone else's gplinks key | `Your_Shortener_API` (set your real key in env) | ✅ |
| 2 | `info.py:103-105` | Tutorial links `t.me/+GeFoPGMglA1kNTZl` | `t.me/+tMAyQb3wxmsyMjg1` (your updates) | ✅ |
| 3 | `plugins/Dreamxfutures/telegraph.py` | Hardcoded imgbb key (`/img`, `/cup`, `/telegraph`) | File deleted | ✅ |
| 4 | `Script.py:379` | Free-trial owner `t.me/royalkrrishna` | `t.me/Escanor_24` | ✅ |
| 5 | `Script.py:404` | Source link `github.com` labelled "Royal" | `t.me/Escanor_24` labelled "HanakoMovix" | ✅ |
| 6 | `plugins/pmfilter.py:1571` | Source Code button `t.me/royalkrrishna` | `t.me/Escanor_24` | ✅ |
| 7 | `plugins/commands.py:1066` | Help example `t.me/dreamxbotz` | your updates link | ✅ |
| 8 | `plugins/Dreamxfutures/Imdbposter.py:138` | `bharath-boy-api.vercel.app` receives your TMDB key | Empty placeholder; returns "not configured" so the bot falls back to IMDb | ✅ (own TMDB lookup to be added later) |
| 9 | `app.json:2,18-19` | DreamxBotz name + `github.com/DreamXBotz/Auto_Filter_Bot.git` | HanakoMovix + `github.com/Escanor244/telegram-filter-bot` | ✅ |
| 10 | `requirements.txt:41` | `git+https://github.com/Joelkb/cinemagoer` | – | ➖ Keep for now. This is the IMDb library the bot needs. Later: pin it to a fixed commit (R5) |

## 2. Your own links: wrong target fixed

| File:line | Was | Now | Status |
|---|---|---|---|
| `Script.py:227` file caption | movie group `+ryDE7AwX…`, "[ Updates ]" | updates channel `+tMAyQb3w…`, "[ HanakoMovix ]" | ✅ |
| `Script.py:288` premium proof | movie group | proof channel `+NX7VWZZ9zSozOGY9` | ✅ |
| `Script.py:297` | `@Escanor_24` | unchanged (yours) | ➖ |
| `info.py:123-125` | `GRP_LNK`, `UPDATE_CHNL_LNK`, `OWNER_LNK` | unchanged (yours) | ➖ |

## 3. Brand names / credits

| File:line | Was | Now | Status |
|---|---|---|---|
| `Script.py:258-267` console logo | "TECHNO KRISH" | "HANAKOMOVIX" | ✅ |
| `utils.py:887,895,913,921` "Powered by" fallback | `ᴅʀᴇᴀᴍxʙᴏᴛᴢ` | `HanakoMovix` | ✅ |
| `info.py:218,220` `SESSION_NAME`, `name` (both unused) | `dreamXBotz`, `DREAMXBOTZ` | `HanakoMovix`, `HANAKOMOVIX` | ✅ |
| `dreamxbotz/util/*.py` line 1 (8 files) | `#Thanks @dreamxbotz…` | removed | ✅ |
| `plugins/join_req.py:1` | `#Join Telegram Channel - @DREAMXBOTZ` | removed | ✅ |
| `plugins/p_ttishow.py:16` | `"""---https://t.me/dreamxbotz---"""` | removed | ✅ |
| `plugins/Premium.py:194` | `# Credit - @BeingXAnonymous` | removed | ✅ |
| `dreamxbotz/util/custom_dl.py:28-29` | Credit to eyaadh (megadlbot, open source) | – | ➖ Open-source attribution for borrowed code. Doesn't point users anywhere |
| `info.py:20,78` | `royal_search`, `royal_files` | – | ➖ Keep. Changing `COLLECTION_NAME` would empty your file index |
| Code identifiers: `dreamxbotz` folder, functions, variables (`bot.py`, `broadcast.py`, `pmfilter.py`, `ia_filterdb.py`, …) | – | – | ➖ Internal names only, not shown to users. Renaming would break imports |
| `Dockerfile:8` `WORKDIR /DreamxBotz`; `logging.conf:43` `DreamXlogs.txt`; `route.py:26` | – | – | ➖ Internal paths. `/logs` reads `DreamXlogs.txt` by name |
| `README.md:85` "Jai Shree Krishna" | – | – | ➖ Neutral text |

## 4. Images hosted by others

These were someone else's uploads on graph.org, telegra.ph and i.ibb.co. All of them now point to your own folder through `IMG_BASE_URL` (default `https://escanor244.github.io/telegram-bot-resources/hanako-movix-images`).

| File:line | Used for | Now | Status |
|---|---|---|---|
| `info.py` `PICS` | `/start` photo | `start1-4.jpg` | ✅ |
| `info.py` `NOR_IMG`, `SPELL_IMG` (unused) | – | `start1.jpg` | ✅ |
| `info.py` `MELCOW_PHOTO` | group welcome | `welcome.jpg` | ✅ |
| `info.py` `SUBSCRIPTION` | premium menu | `premium.jpg` | ✅ |
| `info.py` `FSUB_PICS`; `commands.py:255` fallback | force-subscribe prompt | `fsub1.jpg`, `fsub2.jpg` | ✅ |
| `info.py` `VERIFY_IMG` | verification done | `verify.jpg` | ✅ |
| `Premium.py:67`, `pmfilter.py:1433` | premium-only stream | `prestream.jpg` | ✅ |
| `Premium.py:183` | `/plan` | `plan.jpg` | ✅ |
| `pmfilter.py:131, 1499, 1551` | referral, donate, trial | `refer.jpg`, `donate.jpg`, `trial.jpg` | ✅ |
| `req.html` poster, `dl.html` background | stream pages | `background.jpg` | ✅ |
| `info.py` `QR_CODE` | UPI QR | set in env | ⏳ your real QR |

## 5. Stickers

| File:line | Status |
|---|---|
| `check_alive.py:25`, `pmfilter.py:1765` | ➖ Public Telegram sticker IDs. No links, can't run code. Keep |

## 6. Stream web pages

| File | Third-party content | Status |
|---|---|---|
| `dreamxbotz/template/dl.html` (other files) | All links → yours, title/brand → HanakoMovix, 9 credit comments removed, icons → your GitHub `icons/` | ✅ |
| `dreamxbotz/template/req.html` (video/audio) | All links → yours, "Techno Krrish" → HanakoMovix, 20 credit comments removed, poster → your `background.jpg`, icons → your GitHub, "Blaze" flame logo → built-in film icon | ✅ |
| `dreamxbotz/template/req2.html` | Was unused; loaded JS from `streamlink-two.vercel.app` | ✅ deleted |
| CDN libraries: Plyr, GSAP, three.js, Font Awesome, Google Fonts, Tailwind, Shery.js, controlKit | Public libraries | ➖ Keep. Later, pin `controlKit@master` and unversioned `sheryjs` (R4) |

## 7. Placeholders (fine as is)

| File | Content | Status |
|---|---|---|
| `env.example` | `example.com`, `your_group`, `-1001234567890`, `api.gplinks.com` | ➖ Template values only |
| `commands.py:637` | `/reload 123456789` | ➖ Help example |
