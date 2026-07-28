# APODE — Implementation Plan (WPAI)

**Product:** Autonomous Print-on-Demand Enterprise  
**Version:** Plan 1.0 · 2026-07-10  
**Owner:** Rob Bulkley (Director) · WPAI Software division  
**Spec source:** `APODE.md` (v3.0), `APODE_BP.md`, `ChatLog.md`  
**Status:** Spec complete · **code not started** · gated behind revenue rules  

---

## 0. One-sentence mission

Ship a **human-gated** print-on-demand pipeline that turns approved designs into live listings and fulfilled orders — automate the boring 90%, never the decisions that can ban the store or burn cash.

---

## 1. Fit with WPAI (do not skip)

### 1.1 Standing rules (from `REVENUE-PLAN.md`)

| Rule | Implication for APODE |
|------|------------------------|
| Distribution beats product count | Do **not** open APODE build sprints while RepoForge / MFM / stems still need marketing oxygen |
| Nothing new until 1–3 are live | RepoForge + MFM are live; **stems + music cadence still ahead**. APODE is parking-lot until **activation gate** |
| AI disclosed | Every listing and ad creative discloses AI-assisted design |
| Quality bar | No generic slop; Checkpoint 1 is non-negotiable |

### 1.2 Activation gate (when APODE may consume real build hours)

All of the following must be true:

1. **Marketing motion stable** for live products (≥2 weeks of shipping/promotion cadence, not zero posts).  
2. **Weekly build budget** available without starving music release #1 or stem listings.  
3. **Hard cash cap** reserved for pilot: **$100 total ad spend** + ≤$100 tooling for 14 days (from BP Sprint 5).  
4. Director explicitly flips STATUS: `APODE = ACTIVE`.

Until then: plan and light research only; no Shopify bill, no proxy spend, no multi-agent framework sprawl.

### 1.3 Positioning under WPAI

| | |
|--|--|
| Brand | Sell as WPAI merch **or** a thin DBA storefront linked from wpaistudio.net — decide at Phase 1 store open |
| Division | Software (ops automation) + Graphics (design quality) |
| Success metric (12 mo) | System pays its own API + Shopify bill and returns **any** positive net after ads — not $12.5k fantasy |

---

## 2. Reality cut: blueprint → buildable slices

The v3.0 spec lists **11 agent teams**. For a one-person studio that is a company org chart, not a v0.1 backlog.

### 2.1 Collapse map

| Spec “Team” | Phase 0–1 (MVP) | Phase 2 | Phase 3+ |
|-------------|-----------------|---------|----------|
| 1 Web Scrapers | **Manual** niche list + light research | Optional: one marketplace RSS/scrape | Full scrapers + proxies |
| 2 Social Listeners | **Manual** (you + saved searches) | Optional Reddit/TikTok search export | API listeners |
| 3 Designers | **Local/API design gen** + file vault | Batch jobs + queue | Full cluster |
| 4 Compliance | **Human at CP1** + simple USPTO web check | Scripted trademark keyword list | USPTO API + CV |
| 5 Storefront | **Shopify + Printify** (one product type) | Multi-blank, SEO templates | Multi-store |
| 6 Marketing | **Off** or manual $5/day test | One ads API or native UI only | Auto scale |
| 7 Finance | **Spreadsheet + hard $ caps** | Simple ledger table | Full controllers |
| 8 CX | Platform defaults | Canned replies | AI triage |
| 9 Organic | **Banned in MVP** (spam risk) | Manual only | Carefully rate-limited |
| 10 Legal/DMCA | **Skip** | Manual Google reverse image | Semi-auto |
| 11 Logistics | **Printify routing only** | Failover provider | Multi-provider optimizer |

### 2.2 Three checkpoints (keep forever)

| Gate | When | Human action | Automate later? |
|------|------|--------------|-----------------|
| **CP1 Creative/IP** | Before any listing goes live | Approve / reject / regen design | Never fully |
| **CP2 Capital** | Before any daily ad budget > test cap | Approve scale / deny | Soft-automate only under hard account caps |
| **CP3 Logistics** | Address fail / stockout | Fix or cancel | Partial |

These are the product. Everything else is optional acceleration.

---

## 3. MVP definition (Phase 1) — “Thin APODE”

### 3.1 In scope

1. **One niche** chosen by Director (not scraped) — e.g. WPAI / gamer / metal-adjacent merch that matches brand.  
2. **Design pipeline:** prompt → generate → upscale/export **print-ready transparent PNG** → store in local vault + optional object storage.  
3. **Checkpoint 1 UI:** local Streamlit (or simple FastAPI + HTML) listing `AWAITING_REVIEW` designs with Approve / Reject / Regen.  
4. **Publish path:** on Approve → create Printify product on one blank (e.g. Bella+Canvas 3001) → push to **one Shopify store**.  
5. **Order path:** Shopify → Printify fulfillment (default).  
6. **Ledger:** SQLite or Postgres table of listings + costs; CSV export.  
7. **Ad path:** **disabled by default**; optional manual Meta/TikTok ads with **$5/day/product** and **$100 total pilot** hard stop (human-only).  
8. **AI disclosure** on every product description footer.

### 3.2 Explicitly out of scope (MVP)

- Multi-agent frameworks (CrewAI/AutoGen/LangGraph “11 teams”)  
- Residential proxy farms  
- Automated social commenting / community spam (Team 9)  
- DMCA bots  
- Multi-provider routing  
- Auto budget scaling  
- pgvector trend clustering  

### 3.3 MVP exit criteria (done means)

- [ ] 10 designs through CP1 in one sitting  
- [ ] ≥3 live Shopify products fulfilled by Printify (test order OK)  
- [ ] Zero unapproved designs can reach Shopify (enforced in code)  
- [ ] Kill switch: one config flag stops all publish + ads jobs  
- [ ] Runbook: how Rob reviews CP1 in ≤15 minutes  

---

## 4. Phased roadmap

### Phase 0 — Gate & prep (0–1 week of calendar, low hours)

**Goal:** Decide go/no-go without spending.

| Task | Owner | Output |
|------|--------|--------|
| Confirm activation gate vs revenue plan | Director | STATUS: ACTIVE or WAIT |
| Choose **one** niche + brand voice for merch | Director | `APODE/NICHE.md` |
| Choose blank + print provider | Director | Printify account + one blueprint product |
| Choose store identity | Director | WPAI Shopify vs separate store name |
| Legal skim: AI merch + trademark basics | Director | Notes in `APODE/RISKS.md` |
| Repo scaffold only if ACTIVE | Executor | `Software/APODE/app` skeleton |

**Exit:** ACTIVE + niche + Printify + Shopify decided — or explicit WAIT.

---

### Phase 1 — Thin pipeline (3–5 weeks @ 5–10 hrs/wk)

**Goal:** Design → CP1 → Printify → Shopify works.

```
[CLI / job: generate_design]
        ↓
[storage: designs/{id}/master.png + meta.json]
        ↓
[CP1 dashboard: Streamlit]
   APPROVED ──▶ [publish_job] ──▶ Printify ──▶ Shopify
   REJECTED ──▶ archive
   REGEN    ──▶ back to generate with notes
```

#### Sprint 1.1 — Data + vault (3–5 days)

| Deliverable | Detail |
|-------------|--------|
| Schema v0 | SQLite OK: `designs`, `listings`, `events` (see §5) |
| File layout | `data/designs/{uuid}/master.png`, `meta.json`, `mockup.jpg` |
| Config | `.env.example`: paths, kill switch, no secrets in git |
| CLI | `apode design list \| show \| status` |

#### Sprint 1.2 — Generation (5–7 days)

| Deliverable | Detail |
|-------------|--------|
| Generator adapter | **One** backend first: local SDXL **or** commercial API (pick by cost/quality) |
| Prompt templates | Niche-specific; force “flat graphic, print tee, transparent bg, no photoreal person logos of brands” |
| Post-process | Size to print provider DPI/template; fail closed if alpha/contrast checks fail (simple OpenCV or PIL) |
| Write `designs` row | `status=AWAITING_REVIEW` |

#### Sprint 1.3 — Checkpoint 1 UI (3–5 days)

| Deliverable | Detail |
|-------------|--------|
| Streamlit app | Gallery of pending designs; large preview; notes field |
| Actions | Approve / Reject / Regen (writes status + `reviewed_at`) |
| Auth | Local password or bind localhost only for MVP |
| Notify | Optional: desktop beep / ntfy.sh on new pending |

#### Sprint 1.4 — Publish + fulfill (5–8 days)

| Deliverable | Detail |
|-------------|--------|
| Printify API | Create product from approved master + fixed blank |
| Shopify | Publish via Printify channel or Admin API — **one path only** |
| Idempotency | Re-approve must not duplicate listings |
| Test order | Place real low-cost test order; document tracking |
| Description template | Title + bullets + **AI disclosure** + WPAI link |

#### Sprint 1.5 — Pilot hardening (2–4 days)

| Deliverable | Detail |
|-------------|--------|
| Kill switch | `APODE_ENABLED=false` stops generate/publish |
| Audit log | Append-only `events` for every state change |
| Runbook | `APODE/RUNBOOK.md` — daily 15‑min CP1 |
| Pilot metrics | Sheet: designs in, approved, listed, orders, refunds |

**Phase 1 exit:** MVP exit criteria (§3.3).

---

### Phase 2 — Catalog velocity + light money loop (4–8 weeks)

Only after Phase 1 exit and ≥1 organic or manual sale path proven.

| Workstream | Scope |
|------------|--------|
| Batch generate | N designs/night offline |
| SEO templates | Keyword injection from niche doc |
| Mockup automation | Lifestyle mockups for PDP |
| Finance lite | COGS + retail in DB; weekly P&amp;L CSV |
| Ads (optional) | Manual campaigns; CP2 = any budget > $5/day or total > $50 |
| Trademark assist | Local blocklist of brand names / characters (not full USPTO API) |

**Still out:** Team 9 organic bots, DMCA automation, multi-agent orchestration frameworks.

**Phase 2 exit:** 15–20 approved products live; process time from idea→live **&lt; 48 hours** with CP1; pilot ad spend (if any) within cap and documented ROAS.

---

### Phase 3 — Selective automation (months 4–9)

Unlock **one** stream at a time, each with a kill criteria:

| Stream | Unlock condition | Kill criteria |
|--------|------------------|---------------|
| Trend assist (not full scrape) | Phase 2 exit | &gt;10% CP1 reject for IP |
| Second product type (hoodie/mug) | Tee margin proven | Negative margin 2 weeks |
| Second print provider | Stockout hit CP3 twice | Complexity &gt; benefit |
| Paid ads API | Manual ads ROAS &gt; 1.5 for 30 days | Any day over hard account cap |
| Simple multi-agent jobs | Single-process pipeline bottleneck | Ops time &gt; automation savings |

Full 11-team architecture remains the **north-star diagram** in `APODE.md`, not the sprint board.

---

## 5. Technical architecture (MVP)

### 5.1 Principles

- **One process model:** CLI + scheduled jobs + one small web UI.  
- **Postgres later:** start SQLite; schema written so migration is trivial.  
- **Providers behind interfaces:** `DesignBackend`, `PrintProvider`, `Storefront` — swap without rewrite.  
- **Secrets out of git.**  
- **Human state is first-class:** no silent auto-publish.

### 5.2 Suggested monorepo layout

```
Software/APODE/
  PLAN.md                 ← this file
  APODE.md                ← architecture north star
  APODE_BP.md             ← business model
  README.md
  app/
    pyproject.toml
    src/apode/
      __init__.py
      config.py
      db.py
      models.py
      generate.py
      publish.py
      providers/
        printify.py
        shopify.py
        design_sdxl.py      # or design_api.py
      jobs/
        generate_batch.py
        sync_orders.py
    ui/
      cp1_app.py            # Streamlit Checkpoint 1
    tests/
  data/                   # gitignored designs + sqlite
  docs/
    RUNBOOK.md
    NICHE.md
    RISKS.md
```

### 5.3 Schema v0 (SQLite / Postgres-compatible)

```sql
CREATE TABLE designs (
  id TEXT PRIMARY KEY,
  title TEXT NOT NULL,
  prompt TEXT NOT NULL,
  niche TEXT,
  master_path TEXT NOT NULL,
  mockup_path TEXT,
  status TEXT NOT NULL DEFAULT 'AWAITING_REVIEW',
  -- AWAITING_REVIEW | APPROVED | REJECTED | REGEN_REQUESTED
  reviewer_notes TEXT,
  reviewed_at TEXT,
  created_at TEXT NOT NULL
);

CREATE TABLE listings (
  id TEXT PRIMARY KEY,
  design_id TEXT NOT NULL REFERENCES designs(id),
  printify_product_id TEXT,
  shopify_product_id TEXT,
  retail_price_cents INTEGER NOT NULL,
  cogs_cents INTEGER,
  status TEXT NOT NULL DEFAULT 'ACTIVE',
  -- ACTIVE | PAUSED | SUNSETTED
  created_at TEXT NOT NULL
);

CREATE TABLE events (
  id TEXT PRIMARY KEY,
  entity_type TEXT NOT NULL,
  entity_id TEXT NOT NULL,
  event_type TEXT NOT NULL,
  payload_json TEXT,
  created_at TEXT NOT NULL
);
```

### 5.4 Config / guardrails

```env
APODE_ENABLED=true
APODE_DATA_DIR=./data
APODE_MAX_DESIGNS_PER_DAY=20
APODE_PUBLISH_ENABLED=true
APODE_ADS_ENABLED=false
APODE_ADS_MAX_DAILY_USD=5
APODE_ADS_PILOT_TOTAL_USD=100
PRINTIFY_API_TOKEN=
PRINTIFY_SHOP_ID=
SHOPIFY_*=
DESIGN_BACKEND=sdxl   # or api
```

Hard rules in code:

1. `status != APPROVED` → `publish()` raises.  
2. `APODE_ENABLED=false` → all jobs no-op.  
3. Ads code paths dead unless `APODE_ADS_ENABLED=true` **and** CP2 record exists for scale.

---

## 6. Financial pilot (from BP, tightened)

| Item | Cap |
|------|-----|
| Monthly fixed (MVP) | Prefer **&lt; $100**: Printify free tier + Shopify trial/basic only when ACTIVE |
| Pilot ads (14 days) | **$100 total**, never auto-scale |
| Per-product test ads | **$5/day** max |
| Account-level kill | Set in Meta/TikTok UI, not only in our software |
| Success for Phase 2 ads | ROAS ≥ 1.5 over 30 days before any API automation |

Unit economics check each week:

```
net = retail - cogs - fees - ads - refunds
```

If net &lt; 0 for 2 consecutive weeks → pause generate/publish; review niche.

---

## 7. Risk register (build-time)

| Risk | Mitigation |
|------|------------|
| Platform ban (IP) | CP1 mandatory; blocklist; no celebrity/brand rips |
| Ad blowup | Ads off by default; UI-level caps; CP2 |
| Scraper ToS / IP bans | No scrapers in MVP |
| Organic automation spam | Team 9 forbidden until written policy |
| AI slop brand damage | Quality bar + WPAI disclosure + CP1 taste |
| Scope creep to 11 agents | This plan; any new team needs Director sign-off |
| Opportunity cost vs music/tools | Activation gate §1.2 |

---

## 8. Decision log (fill as you go)

| Date | Decision | Rationale |
|------|----------|-----------|
| 2026-07-10 | Plan 1.0 written | Spec exists; need sequenced MVP |
| TBD | ACTIVE / WAIT | Revenue gate |
| TBD | Niche | |
| TBD | Design backend | SDXL local vs API |
| TBD | Store name | WPAI vs DBA |
| TBD | Shopify vs Etsy-first | Default plan = Shopify+Printify |

---

## 9. Immediate next actions (when Director says go)

**Do not start coding until STATUS = ACTIVE.**

When ACTIVE:

| # | Action | Owner | Done when |
|---|--------|--------|-----------|
| 1 | Write `NICHE.md` + `RISKS.md` | Director | Files exist |
| 2 | Create Printify + Shopify accounts | Director | API tokens in local `.env` |
| 3 | Scaffold `app/` + SQLite schema + tests for status machine | Executor | `pytest` green on transitions |
| 4 | Implement generate → vault → CP1 Streamlit | Executor | 5 fake designs reviewable |
| 5 | Implement approve → Printify → Shopify | Executor | 1 live product |
| 6 | Test order + RUNBOOK | Director + Executor | Physical or cancelled test documented |
| 7 | Pilot review | Director | Go Phase 2 / pause / kill |

---

## 10. What “done” looks like at 90 days (if activated Day 0)

| Horizon | Outcome |
|---------|---------|
| Day 14 | CP1 + publish path works; 1 test order |
| Day 30 | 10+ live products; weekly review habit |
| Day 60 | Process &lt; 48h idea→live; P&amp;L known |
| Day 90 | Either **positive contribution after fees** or **documented kill** with lessons — no zombie project |

---

## 11. Relationship to existing specs

| Doc | Role after this plan |
|-----|----------------------|
| `APODE.md` | North-star architecture (11 teams, hybrid loop) — **do not implement wholesale** |
| `APODE_BP.md` | Market + unit economics — use for pricing and pilot caps |
| `ChatLog.md` | Historical design conversation — archive |
| **`PLAN.md` (this file)** | **Execution source of truth** |

Update this file when phases complete or Director pivots. Parking-lot ideas go in `APODE/PARKING.md` if needed — not into sprints.

---

## 12. Summary

| | |
|--|--|
| **Now** | WAIT on activation gate; plan only |
| **First build** | Thin loop: generate → **CP1** → Printify → Shopify |
| **Never skip** | Human creative gate + spend caps + AI disclosure |
| **Do not build first** | 11 agents, scrapers, organic bots, DMCA, auto ad scale |
| **Win condition** | Reliable merch pipeline that cannot publish unapproved art and does not light money on fire |

---

*AI is in the name; the wizard is in the work.*  
*APODE automates the press — not the judgment.*
