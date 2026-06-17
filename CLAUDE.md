# Mock Exam Quiz Player

A simple mock-exam quiz player, built for my wife's **UPTET/SUPERTET** exam prep. Each practice set
has questions *and* a notes space, so she can read study notes right where she practises.

## Philosophy — read this first

- **Not production-grade. It just needs to work.** Prefer the smallest change that does the job over
  clever abstractions. There is no framework, no build step, no tests, no backend code.
- **One file does everything.** All HTML, CSS, JS, and a default exam live in `index.html` (~1650 lines).
- **Works fully offline.** The cloud (Supabase) is an optional bonus; localStorage is the source of truth.
- **The generic name is on purpose** — it can be reused for any multiple-choice exam, not just UPTET.
- **Notes-in-the-same-space is a core comfort feature**, not an add-on. Don't break it.

## Run & deploy

- **Run locally:** open `index.html` directly, or `python3 -m http.server 8000` then visit
  `http://localhost:8000`. Cloud login/sync needs HTTPS, so it works on the hosted URL but not over `file://`.
- **Hosted:** GitHub Pages, served from the **`main`** branch → https://deependra191.github.io/mock_exam_player/
- **⚠️ `main` is the live branch.** Changes only go live after merging to `main` — not from feature branches.
- Repo: `git@github.com:deependra191/mock_exam_player.git`

## Architecture

Everything is in `index.html`. Two CDN scripts (lines 187-188): `@supabase/supabase-js@2` (cloud) and
`marked` (markdown for notes). The default exam is inline JSON in `<script id="default-set">` (line 192,
parsed at line 646).

It's a tiny state machine:

- **One global `state` object** (lines 648-675) holds everything — current view, sets, active exam,
  answers, timer, logged-in user, notes draft, etc.
- **`render()`** (lines 1611-1629) reads `state.view` and swaps `#app`'s innerHTML by calling the matching
  `render<View>()` function. Views: `home`, `login`, `dashboard`, `import`, `resume`, `exam`, `result`,
  `history`, `redo`, `notes`.
- **`window.actions.*`** (starts line 1115) are the event handlers. The rendered HTML wires buttons via
  inline `onclick="actions.foo()"`. An action mutates `state`, persists, then calls `render()`.
- **Navigation** uses the History API so the phone's back button works: `render()` does
  `history.pushState` on view change; a `popstate` listener (lines 1631-1640) restores `state.view`
  (with a safety fallback to `home` for exam/result/resume views that need an active set).

## Data model

**Question** (inside a set's `questions[]`):
```json
{ "subjectId": "MATH", "topicId": "NUM", "question": "…?",
  "options": ["…","…","…","…"], "answer": 1, "explanation": "…" }
```
`answer` is a **0-based index** into `options`. `subjectId`/`topicId` must exist in `TAXONOMY`.

**Set:** `{ "title": "…", "durationMinutes": 30, "questions": [ … ] }`.

`normalizeSet()` (line 747) validates a set and **rejects the whole set** if any question has a bad
`subjectId`/`topicId` (lines 758-762). It also stamps each question with a stable `qid` = hash of
question text + options (`hashQ`, line 741), so the *same* question across different sets shares one
entry in the question bank (this powers the cross-set redo deck). It resolves the human-readable
`subject`/`topic` names from `TAXONOMY`.

**`TAXONOMY`** (lines 631-645) is the single source of truth for subjects/topics. Subject codes:
`CDP, HIN, ENG, SKT, URD, MATH, EVS, SCI, SST, GK, REAS, COMP, SUP` — each with its own topic codes.

**localStorage keys** (declared lines 619-625, all prefixed `uptet_quiz_player_`):

| Constant | Holds |
|---|---|
| `SETS_KEY` | all practice sets |
| `CURRENT_KEY` | selected set id |
| `SESSION_KEY` | per-set in-progress exams (so you can pause/resume each exam independently) |
| `RESULTS_KEY` | completed attempt history |
| `QBANK_KEY` | question bank keyed by `qid`, with per-question outcome history (drives redo) |
| `CLOUD_BANK_KEY` | ids of cloud attempts already merged in (dedup) |
| `NOTES_KEY` | per-set notes, `{ [setId]: markdownText }` |

## Feature map (where things live)

- **Take an exam:** `startSet()` (1076) + `startTimer()` (1059) → `renderExam()` (1354). Answers save to
  the session on every tap (`saveSession`, 997; also on tab-hide, line 1642).
- **Scoring/results:** `score()` (1112) counts correct, `attempted()` (1113) counts answered.
  `recordResultOnce()` (916) writes the result + per-topic breakdown and updates the question bank.
  `renderResult()` (1403) shows the score and lets you review wrong/skipped/correct with explanations.
- **Redo deck:** `getMistakeRecords()` (965) + `buildRedoSet()` (985) build a practice set from past
  mistakes. Redo sets carry `isRedo: true` (line 990), and `recordResultOnce` skips saving results for
  them (line 920) — practising mistakes shouldn't pollute history.
- **History/analytics:** `aggregateTopics()` (954) rolls attempts up by topic; `renderHistory()` (1437)
  shows weak topics and past scores.
- **Notes:** `readNotes`/`saveNoteForSet`/`getNoteForSet` (816-818), keyed by `setId`. `renderNotes()`
  (1313) has an edit/view toggle; view mode renders markdown via `marked.parse` (line 1326).
- **Cloud sync (Supabase):** config at lines 627-629 (the publishable key is fine to commit — real
  security is login + Row-Level Security). Helpers: `pullCloudSets` (678), `pushCloudSet` (702),
  `pushCloudNote` (711), `pushCloudAttempt` (715). Auth is email/password (`signInWithPassword`, 1203).
  Tables: `exam_sets` (title, duration_minutes, questions, **note**) and `attempts` (score, total,
  per_topic, outcomes, taker). **All cloud calls are best-effort and fail silently** — never let the UI
  block on the network.

## Adding a new question set

1. In the app: **Home → Import**, paste a JSON set (raw, or inside a ```` ```json ```` fence — both work),
   it validates and saves.
2. **Authoring the JSON:** questions are written externally (e.g. via a ChatGPT prompt) using the
   `subjectId`/`topicId` **codes from `TAXONOMY`**. The validator is strict, so the codes must match exactly.
3. **Adding a new subject/topic:** add it to `TAXONOMY` in `index.html` (lines 631-645) **and** to the
   external authoring prompt — they must stay in sync (see the code comment at line 630).

## Conventions & gotchas

- **Code comments are bilingual** (Hindi + English) — this is normal, keep the style.
- **Escape all user/content text** through `esc()` (line 735) before putting it in HTML.
- **Subject/topic keys are normalized** via `topicNorm`/`topicKey` (737-738) so casing/spacing collapse
  (`"Polity"`, `"polity "`, `" Polity"` → one key).
- Redo sessions never record results; `main` is the live branch; cloud is optional.
- No build/test commands exist — verify by opening the app in a browser and clicking through.
