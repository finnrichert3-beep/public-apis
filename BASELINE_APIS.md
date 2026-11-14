# Baseline: API Reference

APIs worth building on for Baseline, an app about hormone optimization and environmental exposures. Picked from this repo's list, with additions the list is missing, and checked against current docs as of **November 2026**.

> Prices and terms change. Re-check each provider's own docs before you commit to one.

---

## Tier 1: Start here (free, works today)

### Open Food Facts: product and ingredient scanner
The core of a "what's in my food?" feature.
- **What:** About 3M+ packaged products by barcode, with ingredient lists, additives (E-numbers), NOVA processing level, Nutri-Score, packaging and allergens.
- **Auth:** None. You **must** send a custom `User-Agent` identifying your app (e.g. `Baseline/0.1 (contact@yourdomain)`).
- **Limits:** 100 req/min for product lookups. Search is much tighter, so cache aggressively.
- **Endpoint:** `GET https://world.openfoodfacts.org/api/v2/product/{barcode}.json`
- **Baseline use:** Scan a barcode, flag seed oils, artificial dyes, certain preservatives and "ultra-processed" (NOVA 4), and give the product a score.
- **Docs:** https://openfoodfacts.github.io/openfoodfacts-server/api/
- **Data license:** ODbL. You must give attribution, and if you redistribute the database itself, it stays open.

### USDA FoodData Central: nutrient data
- **What:** The official US nutrient database, covering micronutrients (zinc, magnesium, vitamin D and others relevant to hormone health) for whole and branded foods.
- **Auth:** Free API key from api.data.gov. `DEMO_KEY` works for testing (verified working).
- **Limits:** 1,000 req/hour per key.
- **Endpoint:** `GET https://api.nal.usda.gov/fdc/v1/foods/search?query=...&api_key=...`
- **Baseline use:** Show "foods high in zinc / magnesium / vitamin D" and micronutrient tracking.
- **Docs:** https://fdc.nal.usda.gov/api-guide

### openFDA: drugs, recalls, adverse events
- **What:** FDA data on drug labels, adverse-event reports, food/drug/device recalls and cosmetics adverse events.
- **Auth:** Optional free key (240 req/min with a key, 1,000/day without). Verified working.
- **Endpoints:** `/drug/event.json`, `/drug/label.json`, `/food/enforcement.json` (recalls), `/cosmetic/event.json`
- **Baseline use:** "Does this medication list endocrine side effects on its label?" and recall alerts for foods and supplements.
- **Docs:** https://open.fda.gov/apis/
- **Caveat:** Adverse-event reports are unverified and **don't prove causation**. Present them that way.

### OpenAQ: air quality
- **What:** Global PM2.5, ozone, NO₂ and other readings from government and research monitors.
- **Auth:** Free API key (key-less access is heavily throttled).
- **Limits:** 60 req/min, 2,000/hour.
- **Docs:** https://docs.openaq.org/
- **Baseline use:** Daily "environment load" from local air quality. Air pollution has some published associations with endocrine markers, so cite carefully.

---

## Tier 2: Wearables (sleep, HRV, recovery)

Sleep and recovery data is the strongest hook for a hormone app, because sleep has a large, well-documented effect on testosterone.

| Source | Status (Nov 2026) | Notes |
|---|---|---|
| **Fitbit Web API** | ❌ **Shut down Nov 2, 2026** (returns 410 Gone) | Listed in this repo, but dead. Replaced by the Google Health API, which reportedly isn't onboarding new projects right now. |
| **Oura API v2** | ✅ Free | OAuth. 10 users until Oura approves your app. Users on Gen3+ rings need an active membership. **Read the June 2026 developer agreement** because it reportedly restricts AI processing and monetization of the data. |
| **WHOOP API v2** | ✅ Free for now | OAuth. 10 users in development. WHOOP can add charges with notice. |
| **Apple HealthKit** | ✅ Native only | No web API. You need a native iOS app (Swift/React Native/Expo module). Most of your target users are on iPhone, so this is the big one. |
| **Android Health Connect** | ✅ Native only | Android counterpart to HealthKit. |
| **Garmin** | ⚠️ Paused | New Connect Developer Program applications are reportedly paused. |
| **Terra** (aggregator) | 💰 From ~$399/mo | One API covering many wearables. It saves a lot of integration work, but it's expensive before you have revenue. Ask about their startup accelerator. |

**Recommendation:** Go native-first with HealthKit and Health Connect. They're free and cover most devices because Oura, WHOOP and Garmin all sync into them. Add direct Oura/WHOOP later, and Terra only once revenue covers it.

---

## Tier 3: Lab results (the actual hormone data)

Nothing in this repo covers labs. Options:

- **Manual entry / PDF upload (start here).** Users type in or upload their bloodwork: total and free testosterone, SHBG, estradiol, LH, FSH, TSH, vitamin D and so on. It's free, legally simple, and enough for an MVP.
- **Junction (formerly Vital):** A lab-ordering API with at-home kits and Quest/Labcorp draws, plus normalized results and a sandbox. Coverage varies by state. Ordering tests involves a physician network and medical/regulatory overhead. Docs: https://docs.junction.com/lab
- **Terra Lab Reports add-on:** Parses lab PDFs (~$499/mo + usage).

---

## Other entries from this repo's list

| API | Verdict |
|---|---|
| Edamam Nutrition / Recipes | Good for meal analysis; paid tiers |
| Spoonacular | Recipes and meal planning, with a free tier |
| Nutritionix | Natural-language food logging ("2 eggs and toast"); confirm current pricing |
| Chomp | Grocery product data; paid |
| IQAir | Air quality alternative to OpenAQ; commercial use is paid |
| Strava | Workouts. Its terms restrict use of the data in AI/ML; read them first. |
| Wger | Open-source exercise database; free |
| BreezoMeter (Environment) | ❌ Acquired by Google; folded into Google Maps Platform's Air Quality/Pollen APIs |
| ~15 COVID APIs in "Health" | ❌ Mostly dead or irrelevant |

---

## Not an API, but a gap worth filling

The research on endocrine disruptors in personal-care products (parabens, phthalates, oxybenzone, BPA in receipts and plastics) isn't available from any good free API. EWG Skin Deep has no public API. **A curated ingredient-flag list you build yourself**, with each flag tied to a PubMed citation, could be Baseline's real moat. A barcode lookup through Open Food Facts (and its sister project **Open Beauty Facts**) plus your own flag database is something competitors can't easily copy.

---

## Legal guardrails for a health app

- **FTC Health Breach Notification Rule:** This applies to health apps even when HIPAA doesn't. If health data is breached or shared without consent, you must notify users. The FTC has also penalized health apps for sharing data with advertisers (GoodRx, BetterHelp).
- **Don't make medical claims.** "This product contains a compound associated with X in studies [link]" is fine. "This will lower your testosterone" or "Baseline treats low T" crosses into FDA/FTC territory.
- **Wearable terms:** Oura and Strava restrict AI processing or resale of their data. Read them before building AI features on top.
