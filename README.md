# 🎯 Strict Pure Stocks — NSE 500 GTF Strict Top-3 (v4.0)

**Backtest-tested** swing scanner. NSE 500 me se sirf woh stocks filter karta hai jo
2.5 saal ke backtest me profitable nikle — weak picks dikhana band.

## 🖱 Dashboard UI (user-friendly, purane dashboard ke sab best features)

- 🗂 **Sector card click = table filter** (card select karo, zone table wahi sector dikhayega; dobara click = clear)
- 📅 **DATE-WISE HISTORY FILTER**: zone table ke strip me date dropdown — purane din ka poora scan dekho
  (top-3, watchlist, sector cards, zone table sab us din ka). Scanner har din ka scan `history/all.json` me
  save karta hai (last 250 scans) aur GitHub Pages se live load hota hai. Seed me ~76 din ki history pehle se
  hai (May–Aug 2026 backtest replay) — live scans aage apne aap judte jayenge.
- 🏆 **TOP-3 HISTORY DROPDOWN**: STRICT TOP-3 section me bhi apna 📅 date dropdown — purane din ki
  picks dekho (dono dropdowns sync me hain).
- 🆕 **FIRST IN TOP-3 COLUMN**: har pick ke saath wo date jab stock <b>pehli baar strict top-3</b> me aaya tha
  (poori history se scanner khud compute karta hai + dashboard client-side fallback). Pehli baar aane
  wale pick pe "🆕 FIRST TIME" badge dikhta hai. Watchlist me bhi ye column hai.
- 📌 **SYMBOL column fixed** — table left/right slide karo, pehla column apni jagah rehta hai
- 📊 **Stock name click = TradingView chart** (har row me 📊 link)
- ⬇ **Filtered CSV download** — zone table ka current filter jaisa dikh raha hai, waisa CSV
- 🔒 **Har baar open karne pe password** (auto-unlock hata diya gaya)
- 📅 **Scan date line** (header me): scan date + "Aaj ka scan ✅ / Last scan ⏳" + auto-scan time
- 🔄 **Refresh feedback**: button "⏳ Refreshing…" hota hai, phir toast me "✅ Refreshed — scan date: X"
  aur table subtitle me bhi scan date likhi hoti hai (kal ka data kyun lag raha hai — ye ab saaf dikhega)

> ⚠️ Agar 15:45 IST se pehle kholte ho to data pichhle din ka hona NORMAL hai —
> ab dashboard khud bata dega. Naya scan har trading din 15:45 ke baad aata hai.

> Backtest (Jan 2024 → Aug 2026, 783 filled trades):
> | | Old v3.19 | **Strict v4.0** |
> |---|---|---|
> | Win rate | 36% | **42%** |
> | Avg R/trade | +0.02R | **+0.31R** |
> | T1 hit | 26% | **41%** |
> | Total (2.5 saal) | +12R | **+38R** |
> | Max drawdown | -81R | **-16R** |

## 📖 Course Mode (Naya — GTF Ep 5/6/8 vetoes)

GTF course transcript (Episodes 3–19) ke rules data pe validate karke scanner me add kiye gaye hain:

```python
COURSE_MODE_ENABLED = True   # False = veto band (purana behavior)
GARBAGE_BASE_LIMIT  = 5      # >5 base candles = garbage zone (Ep 5/6)
MIN_ZONE_SCORE      = 5.5    # course 7-pt trade score minimum (Ep 8)
```

**Kaise kaam karta hai:** strict filter pass karne wale har candidate ka course-faithful zone
detector (`gtf_v2.py`) se re-check hota hai — base candle count + 7-point trade score.
Garbage ya weak zone → pick **REJECT** (log me `[COURSE VETO]` dikhega).

**Backtest result (2.5 saal, same picks):**
- Veto OFF: avg +0.32R, win 42%, PF 1.58
- Veto ON:  avg **+0.61R**, win **50%**, PF **2.29**

⚠️ **Trade-off:** signals ~74% kam (~1 pick/month expected). Ye course ka apna philosophy hai —
"har trade ko reject karne ki koshish karo, quality pe focus" (Ep 9). Kuch din/weeks
**0 picks = WAIT** dikhega — ye feature hai, bug nahi.

> Example (live scan 2026-08-14): M&MFIN strict filter pass karta tha, lekin course check me
> uska v2 zone `tests=3, score=3.0` (WEAK) mila — price us zone ko 3 baar bounce kar chuki thi →
> REJECT → "NO STRICT PICKS TODAY - WAIT".

## 🔒 Password
Dashboard unlock password: `7004602` (index.html me change kar sakte ho).

## ✅ Strict Filter (scanner me config — strict_pure_stocks_scanner.py)

```python
STRICT_MIN_COMBO     = 11.0   # sirf SUPER combo
STRICT_FRESH_ONLY    = True   # sirf 0-test (fresh) zones
STRICT_MIN_1D_SCORE  = 8.0    # 1D zone score >= 8  (0.0 = disable)
STRICT_SECTORS = {"OIL","FMCG","BANK","HEALTHCARE","INFRA","AUTO","FINSERVICE","IT"}
```

⚠️ Backtest me ye **ulta nuksan** karte hain, isliye remove kiye gaye:
- "sirf IN-zone (BUY READY)" → win 19%, SL hit 74%
- high-volume (2x+) picks → SL hit 84%
- dual-zone compulsory → negative edge

## 🚀 GitHub pe setup (5 minute)

1. GitHub pe naya repo banao — naam: **strict-pure-stocks** (GitHub repo naam me space nahi hota)
2. Local folder me ye files rakho, phir:
   ```bash
   git init
   git add .
   git commit -m "Strict Pure Stocks v4.0 - strict backtest-tested filters"
   git branch -M main
   git remote add origin https://github.com/<YOUR_USERNAME>/strict-pure-stocks.git
   git push -u origin main
   ```
3. **Settings → Actions → General → Workflow permissions → "Read and write contents"** ✅
   (yeh zaroori hai — tabhi bot roz JSON commit kar payega)
4. **Settings → Pages → Source: Deploy from branch → main → root** ✅
5. 2-3 minute me dashboard live: `https://<YOUR_USERNAME>.github.io/strict-pure-stocks/`

**Telegram alerts (optional):** Settings → Secrets and variables → Actions →
`TELEGRAM_BOT_TOKEN` aur `TELEGRAM_CHAT_ID` add karo.

## 📁 Files

| File | Kaam |
|---|---|
| `strict_pure_stocks_scanner.py` | Scanner v4.0 — strict filter + Course Mode vetoes (GitHub Actions se roz chalta hai, 15:45 IST) |
| `gtf_v2.py` | Course-faithful zone detector (Ep 3-8: multi-base zones, 7-pt trade score) — veto engine |
| `index.html` | Strict Pure Stocks dashboard — data `gtf_live_data.json` se load karta hai (offline demo bhi embedded hai) |
| `gtf_live_data.json` | Roz ka live scan output (bot isi ko update karta hai) |
| `history/all.json` | Date-wise history — har din ke scan ki copy (dashboard ka 📅 History filter) |
| `.github/workflows/daily_scan.yml` | Mon–Fri 15:45 IST automated scan |
| `nifty500_universe.csv` | Nifty 500 universe (Wikipedia fail hone pe offline fallback) |
| `NIFTY500_GTF_Dashboard.csv/xlsx` | Roz ka full export |

## 🖥 Local run (apne PC pe)

```bash
pip install -r requirements.txt
python3 strict_pure_stocks_scanner.py
# phir index.html browser me kholo
```

## 📊 Trade plan rules (dashboard ke saath follow karo)

- Entry **sirf limit order** pe (zone ke upar chase mat karo)
- SL strict — daily close ke basis pe, ₹1000 risk/trade = qty plan (ya 0.5–1% capital)
- T1 pe aadhi position book, baaki SL cost pe le jaake T2 tak hold
- **NO PICKS = WAIT** — not trading is also a position
- 58% SL-hit normal hai is system me — jeet 2R deti hai, haar 1R leti hai

---
⚠️ Educational project — investment advice nahi. Past performance ≠ future guarantee.
