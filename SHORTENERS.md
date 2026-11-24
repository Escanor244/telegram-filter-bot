# URL Shortener Options (India traffic)

Checked: **24 Nov 2026**. Every number below comes from the network's **own payout-rate page**, fetched on that date. Where the page didn't load or didn't show a figure, the table says so; nothing is filled in by guess.

> Rates change often, sometimes weekly. Open the "Evidence" link yourself before switching.
> Advertised CPM is the rate per 1,000 **completed** views. Real earnings are lower: many users close the ad page early, and networks filter out repeat or VPN visits.

## 1. Comparison from official pages

| Network | India rate / 1000 | Highest listed rate | Min payout | UPI? | Evidence |
|---|---|---|---|---|---|
| **ShrinkMe** | **$4.50** desktop / **$4.25** mobile | Greenland $22 | $5 | Not listed on page | [shrinkme.io/payout-rates](https://shrinkme.io/payout-rates) (page dated 23 Nov 2026) |
| **ShrtFly** | **$3.00** | Greenland $22 | **$1** (PayPal, UPI) | ✅ Yes | [shrtfly.com/payout-rates](https://shrtfly.com/payout-rates) ("Live CPM") |
| **GPLinks** (current) | $1 – $6. The page has 4 tables showing India at $3 / $2 / $1 / $6, and it isn't clear which table applies to which traffic type | US $18 | Not shown | Not shown | [gplinks.com/payout-rates](https://gplinks.com/payout-rates) |
| **Cuty.io** | $2.25 | US $18 | Not shown | ❌ (PayPal, USDT only) | [cuty.io/payout-rates](https://cuty.io/payout-rates). 15% referral |
| **ShrinkEarn** | No India row. "Worldwide deal" $2 – $3.50 | Monaco $40 | $5 | ✅ Yes | [shrinkearn.com/payout-rates](https://shrinkearn.com/payout-rates) |
| **Clk.sh** | No India row. "Worldwide deal" $2 – $3 | Monaco $30 | $5 | ✅ Yes | [clk.sh/payout-rates](https://clk.sh/payout-rates) |
| Ouo.io | ❓ The page blocked automated access (403) | – | – | – | Check [ouo.io/rates](https://ouo.io/rates) manually |
| ModijiURL | ❓ Blocked (403) | – | – | – | Check manually |
| LinkPays | ❓ Page not found (404) | – | – | – | – |
| IndianShortner | ❓ No rate table, just a redirect page | – | – | – | – |

### What this shows (as of the check date)
- **Highest published India rate: ShrinkMe, $4.50.** But its page doesn't list UPI as a payout method; confirm on signup.
- **Best for Indian users who want UPI and a low minimum: ShrtFly**, at $3.00 and a $1 minimum.
- **GPLinks**: India is listed between $1 and $6 depending on the table. Your own GPLinks dashboard shows which rate applies to you; compare that number with the ones above.

## 2. Independent articles (supporting evidence only)

These aren't controlled tests: neither author sent real traffic to every network. Where they disagree with the official pages above, trust the official pages.

| Article | Method | Notes |
|---|---|---|
| [linkutm.com – Highest paying URL shortener India](https://linkutm.com/blog/highest-paying-url-shortener-india) (Aug 2026) | Desk review of rate pages and other blogs | Estimates "$1–$5 per 1,000 completed views" for India. Its "up to $20" figures for ShrinkEarn and Clk.sh **don't match** today's official pages |
| [theguidex.com – Best URL shortener](https://theguidex.com/best-url-shortener-to-earn-money-online/) (Sep 2026) | Author used ShrtFly; others from rate pages | ShrtFly India $3 **matches** the official page. It claims ShrinkEarn pays $4 for India, but the official page doesn't list India, so that's unverified |

Other search-result lists, such as shrink.pe "$45", shrinkbixby "$30" or edx.ipeindia.org, were **ignored**: they are written by a shortener promoting itself, or are low-quality SEO pages.

## 3. Trust and payment reports
- GPLinks on [Trustpilot](https://www.trustpilot.com/review/gplinks.com): mixed. Some users report withdrawals refused for "rule violations"; others report fast support.
- No network here has been checked for actually paying out. **Test with a small amount first**: reach the minimum, withdraw, and confirm the money arrives before you send all your traffic to it.

## 4. Does switching need a code change?

**No.** The bot sends links to the shortener through the `shortzy` library, which works with "AdLinkFly"-style shorteners (`https://site/api?api=KEY&url=LINK`). GPLinks, ShrinkMe, ShrtFly, ShrinkEarn, Clk.sh and Cuty all run on that platform, **but confirm each one on its "Developer API" page after signup** (e.g. ShrinkMe's is [shrinkme.io/developers](https://shrinkme.io/developers)).

To switch:
1. Sign up, then copy your API token from the site's **Tools → Developer API** page.
2. Set the env variables:
   ```
   SHORTENER_WEBSITE=shrinkme.io
   SHORTENER_API=<your token>
   ```
3. Or change it per group: the admin uses the bot's shortener setting command in that group.
4. The bot supports **3 shorteners** (`SHORTENER_*`, `*2`, `*3`), which are used for verification steps 1, 2 and 3. You can mix networks, e.g. ShrinkMe for step 1 and ShrtFly for step 2.

> ⚠️ Pending fix (not done yet): `info.py:108` has someone else's default API key, and `info.py:109` uses `https://api.gplinks.com`. The format still needs checking against Shortzy. Both are on the fix list.
