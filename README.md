[back home](https://github.com/JaysonBucket/CoworkCustoms)  
Content freshness: 28.05.2026 (v1.2.2 release)

# news-commander v1.2.2

> **A Cowork Custom Skill for anyone working in the Microsoft ecosystem.**
> One curated source catalog, topic-centric filtering, one digest per day — no doom-scrolling, no duplicate announcements, no empty-day spam.

A personal news-triage system for cloud architects, consultants, modern-work specialists, security engineers, and anyone else who needs to stay on top of the Microsoft ecosystem without losing an hour a day to RSS readers.

Current version: **1.2.2** — see [news-commander-RELEASE_NOTES.en.md](news-commander-RELEASE_NOTES.en.md) · full source catalog in [news-commander-sources.en.md](news-commander-sources.en.md) · grab the packaged skill from .

</br>

<img width="914" height="418" alt="image" src="https://github.com/user-attachments/assets/c4c5e7ef-dc41-411f-bacf-ae4473c40e91" />

</br>

## What you get: a Cowork skill that triages your morning MS-news in three minutes

- **One curated catalog** — 42 active public Microsoft sources across 14 categories (blogs, TechCommunity boards, dev blogs, Roadmap JSON, Azure Updates JSON, MSRC CVRF JSON, HTML scrapes). Inactive industry verticals you can flip on, removed dead feeds documented in `removed[]`.
- **One pull per day** — 03:00 Europe/Berlin, parallelized across 10 workers (~10–15 s for the full catalog). Gap-aware window: if the schedule missed a day, the next run extends the window automatically and adds 6 h overlap.
- **Topic-centric filtering** — you define topic bundles with weights (e.g. `defender = 1.0`, `copilot = 1.5`, `gpt-5 = 0.8`). Score per item = Σ (topic weight × keyword hits) over title + abstract + source tags. Order in the digest follows your interest, not the publication date.
- **Three buckets, never more** — HIGH (score ≥ 2.0) → top of digest. MEDIUM (≥ 1.0) → context. **Suggestions** (≥ 0.5) → "would fall out under current filters — add this topic?". You see what you almost missed without having to widen the filter manually.
- **Aggressive deduplication** — fuzzy title match (SequenceMatcher ratio ≥ 0.78) catches cross-posts between TechCommunity boards and the corporate blog. Each announcement shows up once, with the highest-priority source winning.
- **Empty-day suppress** — nothing important today? No digest is sent. Silence is a valid answer.
- **One delivery channel** — Teams 1:1 self-chat OR Outlook mail, configurable. One message per morning, push-notification instead of an app toggle.
- **Self-tuning by chat** — `News Commander Topic Add gpt-5` in the Teams self-chat is enough. The next run reads the message, mutates `topics.json`, done. No settings UI, no re-deploy.

How you feed it:
- Topic bundles in `topics.json` (weights, blocklist, max suggestions).
- Self-chat keywords: `News Commander Topic Add <kw>` / `Drop <kw>` — picked up at the next 03:00 run.
- That's it. No mail rules to maintain, no per-source whitelist juggling.

Storage: plain JSON / JSONL under the skill folder. Atomic writes everywhere (tempfile + fsync + `os.replace`) so a crash mid-write can't corrupt state. Monthly archive rotation moves items >90 days into `_archive/items-YYYY-MM.jsonl` with a crash-safe write order.

Net effect: **3–5 minutes of reading per day, once, with everything important in it and nothing duplicated.**

</br>

## What it solves

- **Signal-to-noise** — 50 RSS tabs collapse into one ranked, deduped digest
- **Same announcement, four sources** — Defender news in TechCommunity + the corporate Security blog + the Roadmap + sometimes Mechanics → seen once, with the best source picked
- **No topic focus** — RSS readers sort chronologically; the digest sorts by your topic weights so the things you actually track end up at the top
- **Shifting priorities** — what you track today (GPT-5 rollouts) isn't what you'll track in three months (agent platforms) — `Topic Add` / `Topic Drop` in the self-chat is the whole UI
- **Doom-scroll risk** — one digest, three buckets, two horizontal rules. No reader to open, no infinite list to swipe.
- **Empty-day spam** — empty days send nothing. Push silence is the lightest possible touch.
- **Source rot** — every source carries a health record (`last_pull_ts`, `last_status`, `consecutive_failures`). Three failures in a row → auto-deactivation on the next Monday with a self-chat notice. Dead feeds die quietly; you find out once a week instead of never or constantly.
- **Mirror integrity** — atomic writes, append-only `items.jsonl`, monthly archive rotation with a crash-safe write order (archive-append → items rewrite → meta update). Worst case is a duplicate row in the archive, never data loss.
- **Source discipline** *(HARD RULE since v1.1.2)* — the skill answers questions only from the curated catalog + mirror. Autonomous web lookups for follow-up questions are blocked; if the mirror has a gap, the skill asks once whether to widen — no surprise pretraining knowledge served as if it came from your sources.
- **Link hardening** *(since v1.1.3)* — items with broken or empty URLs render with a fallback to the source page plus a visible "direct link missing — fallback to source page" hint. No silent click-into-nothing.
- **Live fetch on the curated list** *(carve-out of the source-discipline rule)* — `web_fetch` on any URL already in `sources.json` is allowed without asking. Same domains the mirror already pulls, just on demand instead of from the last scheduled run. Useful for refreshing the mirror between runs or verifying a single item without breaking the discipline rule.

</br>

## Day cycle

```
  03:00 ─────► 03:01 ──────► 03:02 ──────► 03:03 ──────► 03:04
  Intake       Pull          Filter        Send          Wrap-up
  (chat scan)  (parallel,    (score, dedup,(digest if    (_meta +
  Topic Add/   gap-aware,    suggestions,  not empty;    digests/
  Drop folded  archive       cut-offs)     suppress      YYYY-MM-
  into         auto-trigger                empty-day)    DD.json)
  topics.json) at end)
```

**Filter buckets:**
- `HIGH` — score ≥ 2.0 (top of digest)
- `MEDIUM` — 1.0 ≤ score < 2.0 (context)
- `WATCH / Suggestions` — 0.5 ≤ score < 1.0 (one-click-add hint)
- below 0.5 — dropped

**Self-chat intake (24/7, processed at the next 03:00):**

```
News Commander Topic Add gpt-5
News Commander Topic Add defender xdr
News Commander Topic Drop xbox
```

`Add` creates a new topic if the keyword is new (weight 1.0, `source: "user_intake"`) or appends to an existing topic. `Drop` removes the keyword from every topic; if a topic ends up empty AND was not auto-created from a role bundle, it gets deleted. History is logged in `topics.json.history[]`.

---

## Command cheatsheet

`News Commander` / `NC` as a namespace — does not collide with the built-in `daily-briefing` skill.

| Command | What it does |
|---------|--------------|
| `News Commander` / `NC start` / `NC briefing` | Trigger a manual pull + filter + send |
| `NC status` | Show `_meta.json` + last run + stats, no pull |
| `NC sources` | Show the active source catalog grouped by category |
| `NC topics` | Show active topics + weights |
| `NC topic add <keyword>` | Manual topic add (alternative to the self-chat keyword) |
| `NC topic drop <keyword>` | Manual topic drop |
| `NC digest` | Show the latest digest from `data/digests/<today>.json` |
| `NC pause` / `NC resume` | Pause / resume the scheduled prompt |

Natural-language triggers also work: *"MS-News heute"*, *"what's new at Microsoft today"*, *"Copilot news this week"*, *"Defender advisories since Monday"*.

---

## Quick start

1. **Download** [news-commander-v1.2.1.zip](news-commander-v1.2.1.zip), unzip, and import the skill folder per your Cowork environment's conventions (typically `/.../skills/news-commander/`).
2. **Copy `data/preferences.example.json` → `data/preferences.json`** and fill in: your time zone, delivery channel (`teams_self_chat` / `mail` / `both`), your own mail address for the mail channel, and the initial topic bundles.
3. **First trigger** runs onboarding — three questions (channel, topic init mode, industries on/off), then the scheduled prompt is auto-created via `SetupScheduledPrompt`.
4. **(Optional)** Create an Outlook rule on `Subject contains "News Commander Digest"` → move to a `News/News Commander/` folder for a tidy mailbox.

Detailed walkthrough with troubleshooting → [SETUP.md](SETUP.md).

---

## What's in the ZIP

```
news-commander/
├── SKILL.md                              # Skill manifest (name, version, triggers, hard rules)
├── SETUP.md                              # Detailed setup guide
├── news-commander-RELEASE_NOTES.en.md    # Version history (Semantic Versioning)
├── news-commander-sources.en.md          # Full source catalog (companion to this file)
├── README.md                             # This file
├── LICENSE                               # MIT
│
├── scripts/
│   ├── _atomic.py                 # Shared tempfile + fsync + os.replace module
│   ├── pull_feeds.py              # RSS / Atom + HTML scrape + JSON APIs, parallel, gap-aware
│   ├── filter_and_rank.py         # Topic scoring + dedup + suggestion detection
│   ├── intake_topics.py           # Teams self-chat scan for Topic Add / Drop
│   ├── archive_items.py           # Monthly rotation: items > 90 d → _archive/, crash-safe
│   └── send_digest.py             # Adaptive Card + mail render + send
│
├── templates/
│   ├── digest-teams.md            # Teams self-chat layout
│   └── digest-mail.md             # Outlook mail layout (HTML)
│
├── scheduled-prompts/
│   └── daily-news-run.md          # Prompt body for the 03:00 run
│
└── data/
    ├── _meta.json                 # Schema / skill version, last_run, stats, last_archive_ts
    ├── sources.json               # Source catalog (42 active, 14 inactive, 12 removed)
    ├── topics.json                # Topic bundles with weights + blocklist + history
    ├── preferences.example.json   # Template — copy to preferences.json and edit
    ├── mirror/                    # Runtime state (gitignored)
    │   ├── items.jsonl            # Append-only mirror, 1 line = 1 item
    │   ├── source-state.json      # Per source: last_pull_ts, last_status, consecutive_failures
    │   └── _archive/
    │       └── items-YYYY-MM.jsonl
    └── digests/                   # Daily digest snapshot for audit / replay
        └── YYYY-MM-DD.json
```

### What each file does

**`SKILL.md`** — Cowork reads name, version, description, and trigger phrases from here. Also encodes the two HARD RULES (Banner-first display, Source discipline). Only edit if you want to change triggers or scope.

**`SETUP.md`** — step-by-step first install plus inter-version upgrades. Mail-rule snippets, placeholder replacements, channel switching, troubleshooting.

**`news-commander-sources.en.md`** — readable catalog of every active source: title, category, tags, link, short description. Generated from `data/sources.json` (v1.2.1).

**`scheduled-prompts/daily-news-run.md`** — the daily 03:00 routine as a prompt body to paste into your Cowork schedule. Five sequential steps (intake → pull → filter → send → wrap-up); the recommended frequency is at the top, the prompt itself in a code block at the bottom.

**`data/_meta.json`** — schema / skill version, per-run timestamps, run counter, last archive timestamp, banner-shown session list. Updated by every scheduled run.

**`data/sources.json`** — the source catalog. Each entry has `id`, `title`, `category`, `tags`, `url`, `feed_url`, `feed_type` (`rss` / `atom` / `html` / `json_m365_roadmap` / `json_azure_updates` / `json_msrc_cvrf`), `priority` (`top` / `high` / `medium` / `low`), `active` (bool). Inactive entries kept for fast on/off toggle; removed entries archived under `removed[]` with a reason string.

**`data/topics.json`** — your topic bundles (`id`, `keywords[]`, `weight`, `source`), blocklist, and `history[]` of every Add / Drop. The filter scores every item against this file.

**`data/preferences.example.json`** — copy to `preferences.json` and edit. Delivery channel, mail address, suggestion aggressiveness, max-age days, dops integration toggle, onboarding state.

**`data/mirror/items.jsonl`** — append-only mirror of every pulled item: `item_id` (sha1 of `source_id + url`, first 16 chars), `source_id`, `source_url`, `title`, `abstract`, `pub_date`, `link`, `tags`, `score`, `dedup_alias_of` (if dropped as a duplicate).

**`data/mirror/source-state.json`** — per-source health: `last_pull_ts`, `last_status` (`working` / `empty` / `failing`), `consecutive_failures`. Three failures in a row → auto-deactivation on the next Monday with a self-chat notification.

**`data/mirror/_archive/items-YYYY-MM.jsonl`** — monthly rotation target for items older than 90 days. Rotation runs every 30 days, triggered automatically at the end of the pull script.

**`data/digests/YYYY-MM-DD.json`** — one snapshot per day, lets you replay / audit any past digest.

> **Note on language:** the skill itself runs in German by default (briefings, mail copy, JSON values where noted). The repository documentation (README, SETUP, RELEASE_NOTES, SOURCES) is English. To localize the skill output, edit the `Sprache:` line at the bottom of each prompt in `scheduled-prompts/` and adjust the templates in `templates/`.

---

## Architecture — the scoring pipeline

The morning run executes five steps in this order. Order matters: intake before pull (so a fresh `Topic Add` shifts the score for items pulled in the same run), filter before send (obvious), archive after pull (so the archive sees a consistent items file).

| # | Step | Function |
|---|------|----------|
| 1 | **Self-chat intake** | Scan the Teams 1:1 self-chat since `_meta.intake_processed_ts` for `News Commander Topic Add/Drop <keyword>` messages. Apply mutations to `topics.json`, log to `history[]`. Cap: 100 messages scanned per run, 20 mutations applied per run (typo-spam safeguard). |
| 2 | **Pull** | Read every `active: true` source in `sources.json`. Window = `(now − last_pull_ts) + 6 h overlap`, default 36 h, gap-aware. Parse via `feedparser` (RSS / Atom), `BeautifulSoup` (HTML), or one of three JSON parsers (M365 Roadmap, Azure Updates, MSRC CVRF). Each item gets `item_id = sha1(source_id + url)[:16]` and is appended to `items.jsonl` if new. Runs across 10 workers via `ThreadPoolExecutor` for ~10–15 s on the full catalog (vs ~80 s sequential). At the end: `archive_items.maybe_archive()` auto-triggers if the last archive is older than 30 days. |
| 3 | **Filter & rank** | For every item in the window: normalize title + abstract, pull source tags, score against every topic in `topics.json` (`score = weight × hits` summed over matched topics), apply blocklist (any blocklist hit → score 0 → drop), apply source-priority boost (`top` +0.5, `high` +0.3, `medium` 0, `low` −0.2), apply recency cutoff (drop > `max_age_days`). Then dedup: fuzzy title match (SequenceMatcher ratio ≥ 0.78) — winner is highest score, tiebreaker source priority, tiebreaker oldest pubDate. Losers are kept in `items.jsonl` with `dedup_alias_of = <winner_id>` (audit trail, not in the digest). Classify: HIGH ≥ 2.0, MEDIUM ≥ 1.0, WATCH ≥ 0.5 (capped by `max_suggestion_items`). |
| 4 | **Send** | If HIGH + MEDIUM + WATCH = 0 AND `suppress_empty_days: true` → no-op. Otherwise render the configured template (Teams Adaptive Card or HTML mail) and send via the configured channel (`teams_self_chat` / `mail` / `both`). Each item gets a primary link; broken / empty URLs fall back to the source page with a visible "direct link missing" hint. |
| 5 | **Wrap-up** | Update `_meta.json.last_run`, increment `total_runs`, write `data/digests/<today>.json` as the audit snapshot. Update `source-state.json` per source: `last_pull_ts`, `last_status`, `consecutive_failures` (reset on success, increment on empty / failing). |

The HARD RULES live in `SKILL.md`:

- **Banner-first** — the ASCII banner is the first output of every manual trigger, in a Markdown code block, before any other text and before any tool call. Tracked per session via `_meta.json.banner_shown_session_ids[]` so it appears once per session, not on every follow-up. Suppressed for scheduled runs and self-chat intake (no human reader).
- **Source discipline** — answers, including follow-up questions, are grounded only in `data/sources.json` + `data/mirror/items.jsonl` (and `_archive/`). Autonomous external lookups (`web_search`, `deep-research`, generic search) are blocked. If the mirror has a gap, the skill asks once whether to widen — and only for that one question. Carve-out: `web_fetch` on any URL already in `sources.json` is allowed without asking (same curated list, just live).

---

## When NOT to use this skill

- **Generic daily overview without MS-news context** → the built-in `daily-briefing` skill is lighter. News Commander only fires on the `NC` / `News Commander` prefix or a clear MS-news phrasing.
- **Single-meeting recap or prep** → `meeting-intel` is more targeted.
- **Ad-hoc web research on arbitrary topics** → `web_search` / `deep-research` directly. News Commander deliberately refuses to widen to the open web.
- **General weather, sports, or non-MS news** → out of scope.

---

## Frontier note

Cowork Custom Skills currently run as a **Frontier feature** in Cowork environments where custom-skill import is enabled. If you can't import the skill on your end, check with your admin whether the feature is turned on.

---

## License

MIT — see [LICENSE](LICENSE). Use it, fork it, carve out pieces, build your own variant. Source content pulled by the skill is subject to the respective publishers' terms.

---

## Feedback

I've been running this skill in my own morning routine for a few weeks and keep evolving it whenever something shows up in real use. If you try it and notice anything — a source missing, a category that doesn't catch, a digest section that feels awkward — feel free to open an [issue](../../issues) or get in touch via [jaysons.dev](https://jaysons.dev).

Community work, no SLA, plenty of love. ✌️

---

**Source:** [github.com/JaysonBucket/NewsCommander](../../) · **License:** MIT · **Web:** [jaysons.dev](https://jaysons.dev)

⭐ If the skill helps you, give the repo a star — it helps others find it.
