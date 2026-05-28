# News Commander — Release Notes (English)

## Why this skill exists

### The challenge

Anyone working professionally with the Microsoft ecosystem — Cloud Architect,
Consultant, Modern-Work Specialist, Security Engineer, or similar — knows the drill:

- **~50 relevant RSS/Atom feeds** across the Microsoft universe (blog.microsoft.com,
  techcommunity.microsoft.com, devblogs, learn.microsoft.com/updates, Roadmap feeds,
  Security updates, industry blogs). Plus tech Twitter, plus newsletters, plus
  YouTube channels.
- **Heavy redundancy.** A single Defender announcement shows up in parallel on the
  Tech Community board, the Microsoft Blog, the Security Blog, and sometimes the
  Roadmap feed — same content, four sources, four clicks to "already read it".
- **No topic focus.** RSS readers sort chronologically. But you don't want
  everything — you want *"what's new today on Defender XDR, Copilot, Azure OpenAI,
  Entra ID?"* The things you're tracking **right now**.
- **Shifting focus over time.** Today you're tracking GPT-5 rollouts; in three
  months it's Copilot Agents; in six months something else entirely. Maintaining
  filters across five tools? Nobody does it.
- **Doom-scroll risk.** Open the reader in the morning and you lose 30 minutes.
  Don't open it and you fall behind.
- **Newsletter spam days.** Some days simply have nothing. Inbox still full.
  Push notifications still firing. The vague sense of missing something stays.

The problem isn't a lack of information. It's **poor signal-to-noise ratio and
missing personalization** to what the role actually needs.

### The approach

News Commander flips the logic:

1. **Topic-centric, not source-centric.** You define topic bundles
   (Defender, Copilot, Azure OpenAI, Roadmap, …) with weights. Every item gets a
   score = Σ (topic weight × keyword hits). Digest order reflects *your* interests,
   not publication date.

2. **Aggressive deduplication.** SequenceMatcher-based fuzzy title matching
   (threshold ≥0.78) catches cross-posts between TC boards and Microsoft blogs.
   You see each announcement once, from the best source.

3. **Three buckets instead of a flood.** HIGH (score ≥2.0) → top of digest.
   MEDIUM (≥1.0) → solid context. **Suggestions** (just below the filter, ≥0.5)
   → *"would this be relevant? Want to add the topic?"*. The last bucket is the
   most honest feature: it shows you what you almost missed.

4. **Self-tunable via chat.** `News Commander Topic Add gpt-5` in the Teams
   self-chat is enough. The next run reads the message, mutates `topics.json`,
   done. No settings UI, no redeploy, no *"think first, configure later"*.

5. **Empty-day suppress.** Nothing important today? Then **nothing** gets sent.
   No empty mail, no "0 items" push. Silence is also an answer.

6. **One-shot channel.** Exactly **one** message in the morning. Teams self-chat,
   mail, or both. Push notification instead of app toggle.

### What you get out of it

| Before | With News Commander |
|--------|---------------------|
| 50 RSS tabs to click through | 1 digest, done in 3 minutes of reading |
| Same Defender news across 4 sources | One copy, deduplicated |
| "What's new on topic X today?" → three searches | Topic score answers it |
| Filter maintenance in reader UI | `Topic Add <keyword>` in self-chat |
| Inbox spam on quiet days | Empty-day suppress |
| News-FOMO without a plan | Structured morning briefing |

Concretely: **3–5 minutes of reading, once a day, everything important inside,
nothing twice.**

### Architectural decisions that mattered

- **Atomic writes.** Every JSON/JSONL write goes through tempfile + fsync +
  `os.replace`. A crash mid-write cannot corrupt state. Important because the
  skill runs daily and is never manually supervised.
- **Append-only mirror.** `items.jsonl` is only appended, never rewritten
  (except during archive rotation). The history stays reconstructable.
- **Monthly archive with crash-safe write order.** Items older than 90 days are
  moved to `_archive/items-YYYY-MM.jsonl` every 30 days — but in an order where
  a crash during rotation can at worst produce duplicates in the archive, never
  data loss. Write order: (1) archive append + fsync, (2) atomic rewrite of
  `items.jsonl`, (3) meta update.
- **Gap-aware pull window.** If the last run was missed, the next run
  automatically extends the window — nothing slips through.
- **Per-source health tracking.** Three consecutive failures → auto-deactivation
  on Monday, with a self-chat notification. Dead feeds die quietly; you find
  out once a week instead of never or constantly.
- **Cross-skill integration is optional and read-only.** Other skills can read
  the latest digest but cannot write. If News Commander is missing, they keep
  working unchanged.

---

## v1.1.2 — Source-Discipline Patch

Small patch release driven by real-use feedback. In v1.1.1, on one follow-up question, the skill autonomously enriched its answer with content from the open web that did not come from the curated sources. That is exactly what News Commander must not do: it is a triage system over a curated source list, not a generic web researcher.

### Changed

- **New HARD RULE "Source Discipline" in `SKILL.md`** — the skill operates exclusively on `data/sources.json` and `data/mirror/items.jsonl`. External tools (`web_search`, `web_fetch`, `deep-research`, `SearchM365` for news lookups) are **forbidden by default** unless the user explicitly authorizes them for the current question.
- **Explicit ask on gaps** — When mirror data cannot answer a user question, the skill MUST issue exactly one `AskUserQuestion` offering: (a) research externally, (b) stay within the defined sources, or (c) add a topic and wait for the next pull. Only after an explicit "yes" do web tools fire — and only for that one question, not as a standing permission.
- **Pre-training knowledge is not presented as a source** — If the skill "knows" something that is not in the mirror, it must not present it as if it came from the curated sources. Either mark it as uncertain AND ask, or omit it.
- **Startscreen banner extended with skill description** — Mirroring the AccountRadar startscreen, the banner now includes short documentation (what it pulls, filter logic, buckets, delivery) plus an explicit v1.1.2 line noting that source discipline is now a HARD RULE. Footer: "More Playful AI at https://jaysons.dev".

### Migration v1.1.1 → v1.1.2

No file or schema migration needed. Only `SKILL.md` (new HARD RULE + banner) and `README.md` / release notes change. Existing `items.jsonl`, `topics.json`, `source-state.json`, `_meta.json`, `preferences.json` remain valid unchanged.

---

## v1.1.1 — Usability Patch

Small patch release based on real-use feedback. Content and architecture identical
to v1.1.0; three usability fixes:

### Changed

- **Banner display is now a HARD RULE.** The ASCII-art banner is mandated as the
  first output of every manual trigger response (before any other text, before
  any tool calls). In v1.1.0 the banner occasionally didn't show on user
  triggers; this is fixed.
- **Explicit "open full article" link per item.** Every item in the digest now
  has a dedicated call-to-action link to the full article, in addition to the
  title-as-link. Applies to both the Teams self-chat Markdown and the HTML mail
  render.
- **More breathing room between topic blocks.** Section dividers (`---`) between
  counts and HIGH/MEDIUM/WATCH blocks, double blank lines between items in
  Markdown. In HTML mail: larger `<hr>` margins (36px), item containers with
  28px bottom margin + border, abstract with line-height 1.5.

### Migration v1.1.0 → v1.1.1

No migration needed. Template and render changes plus SKILL.md header only.
Existing `items.jsonl`, `topics.json`, `source-state.json`, `_meta.json` stay
valid.

---

## v1.1.0 — Community Release

First fully public community release. Bundles all v0.2.0 features in a packaged
state, adds consistency and performance hardening, ships as a ZIP.

### New

- **Public-repo ready.** Skill is fully scrubbed — no internal paths, mail
  addresses, names, or tenant IDs. All user data lives in `data/preferences.json`
  (gitignored). The template `data/preferences.example.json` is copy-and-edit-ready.

- **Hardened cross-skill integration.** Optional, read-only hooks for
  `daily-ops-ms`, `account-radar`, and `fasttrack`/`fasttracker`. Detection is
  silent and miss-silent — if the other skill isn't installed, News Commander
  runs unchanged. No question, no noise.

- **Consistency audit passed.** All Python scripts py_compile-clean. All JSONs
  valid. Cross-references (SKILL.md ↔ scheduled-prompts ↔ scripts ↔ data/) are
  consistent against the v1.1 layout.

- **Performance audit passed.** Scoring loop is linear over recent items × topics
  × keywords. Fuzzy dedup is O(n²) but bounded by bucket survivors (n<30 in
  practice, <100 ms). Archive rotation is a single linear scan. All file writes
  atomic.

### Changed from v0.2.0

- **Versioning.** Skill level is now v1.1.0 (frontmatter, banner, headings) —
  a semantic signal that the API and file structure are stable and ready for
  community adoption.
- **User-Agent string** in `pull_feeds.py` bumped to `NewsCommander/1.1` for
  source-side traceability.
- **Documentation.** The cross-skill integration table in `SKILL.md` now covers
  all three consumers (instead of only DOPS). The file-structure tree reflects
  the atomic-writes module, archive rotation, and the template file.

### Known limits (unchanged from v0.2.0)

- One schedule instance per skill.
- No cross-tenant tagging (multi-customer workflows are targeted no earlier
  than v1.3).
- HTML scraping is fragile; health tracking catches the failure mode, but a
  manual selector update is still needed when a source redesigns.

### Migration v0.2 → v1.1

No code migration needed. Pure version re-labeling plus community hardening.
Existing `items.jsonl`, `topics.json`, `source-state.json`, `_meta.json` stay
valid.

---

## v0.2.0 — Initial Public Release

### New

- **Atomic write module (`_atomic.py`).** Shared module for the tempfile + fsync
  + `os.replace` pattern, exposed as `atomic_save_json()` and `atomic_write_text()`.
  Used by all four mutation scripts (`pull_feeds`, `filter_and_rank`,
  `intake_topics`, `send_digest`) plus `archive_items`.

- **Monthly archive rotation (`archive_items.py`).** Every 30 days, items older
  than 90 days are moved to `data/mirror/_archive/items-YYYY-MM.jsonl`. Crash-safe
  write order: archive append → items.jsonl rewrite → meta update. Worst case is
  a duplicate in the archive, never data loss. Cadence and minimum age are
  override-able via CLI flags (`--interval-days`, `--min-age-days`).

- **Auto-trigger in `pull_feeds.py`.** At the end of every pull,
  `archive_items.maybe_archive()` is called automatically. No separate schedule,
  no cron entry. Errors in the archive step are explicitly non-fatal — pull stays
  green even if rotation crashes.

- **Gap-aware pull window.** `compute_window_start()` checks
  `_meta.last_run.morning_pull_ts`. If the last successful run is >36h in the
  past (schedule down, manual skip), the window expands to
  `(now − last_run) + 6h overlap`. Missed items get picked up.

- **Per-source health tracking.** `data/mirror/source-state.json` tracks
  `last_pull_ts`, `last_status` (`working` / `empty` / `failing`), and
  `consecutive_failures` per source. After 3 consecutive failures, the source
  is automatically deactivated on the next Monday with a self-chat notification.

- **Cross-skill integration hooks.**
  - `daily-ops-ms`: can read the latest digest via the `DOPS news` trigger.
    News Commander in turn reads `daily-ops-ms/data/links.json` as an additional
    topic signal (+0.3 score boost on tag match).
  - `account-radar`: when building a customer snapshot, MS-news items that
    match the customer's topics are surfaced as an "MS-side context" attachment.
  - `fasttrack`: before a deliverable brief, the digest is checked for relevant
    roadmap/wave items for that deliverable in the last 14 days.

  All three integrations are **optional, silent, and read-only** — if News
  Commander is missing, the other skills work unchanged.

- **`preferences.example.json` template.** First-time setup via copy-and-edit.
  The real `preferences.json` is gitignored so nothing personal lands in the repo.

### Changed

- **`scheduled-prompts/daily-news-run.md` STEP 2.** Documents the auto-archive
  trigger in the pull script. No separate archive step needed.

- **`scheduled-prompts/daily-news-run.md` STEP 6.** Simplified. `morning_pull_ts`
  and `total_pull_runs` are already written by the pull script. Only
  `total_runs += 1` remains as a top-level counter.

- **`source-state.json` schema.** `last_guid` field removed (redundant — mirror
  dedup covers it). Schema reduced to what really matters: health tracking.

### Migration v0.1 → v0.2

No migration needed. All changes are additive:

- Existing `items.jsonl` files stay valid.
- `source-state.json` without `last_guid` is read automatically; the field is
  just ignored.
- First run after upgrade sets `_meta.last_archive_ts`; rotation then runs every
  30 days automatically.

### Known limits

- **One schedule instance per skill.** Anyone wanting multi-schedules (e.g.
  morning + evening digest) needs two separate scheduled prompts with different
  modes.
- **No cross-tenant tagging.** Multi-customer workflows are targeted no earlier
  than v0.4.
- **HTML scraping is fragile.** Sources without RSS/Atom are parsed via
  BeautifulSoup — site redesigns break parsing. Health tracking catches it, but
  a manual selector update is still required.

---

## Coming next (not committed)

- **v0.3** — per-item sentiment classification: announce vs. fix vs. EOL vs.
  preview. Weekly digest mode (Monday morning with a week summary).
- **v0.4** — cross-tenant tagging for multi-customer workflows. Adaptive-Card
  GUI in the self-chat instead of plain Markdown.
- **Open** — topic bundles for other roles: Security Architect, Modern-Work
  Specialist, Data-and-AI Lead. Got one? Send it in as a PR.

---

## License

MIT. Sources in `sources.json` are exclusively public Microsoft domains
(blog.microsoft.com, techcommunity.microsoft.com, learn.microsoft.com, etc.);
their content is subject to the respective Microsoft terms.
