# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this project is

BI Taiwan「短影音生成平台 3.0」— a single-page web app that drives an AI-assisted
short-video production workflow for financial/news content:

1. Editor enters a topic, angle, platform, tone, and duration (選題輸入).
2. The form POSTs to a **Make.com webhook** that runs a multi-agent editorial
   meeting (OpenAI draft → Claude review → Gemini traffic judgment → analyst →
   retail-investor view → editor-in-chief final script).
3. The confirmed script feeds a second webhook (or a local fallback) that
   generates storyboard scenes, B-roll keywords, captions, thumbnail, BGM, and
   title suggestions plus a production checklist.
4. Optionally the narration script is sent directly to the **HeyGen API** to
   generate an avatar video (9:16, 1080×1920).

## Architecture

- **Single file: `index.html`** — all markup, CSS, and vanilla JavaScript live
  here. There is no build step, no framework, no package.json, and no tests.
- Backend logic lives in external Make.com scenarios reached via user-supplied
  webhook URLs; this repo only handles the front-end and response rendering.
- Expected webhook response fields: `final_script` (or `result`/`text`), plus
  optional agent summaries `draft_summary`, `claude_review`, `gemini_judgment`,
  `analyst_view`, `retail_view`. The video agent returns `scenes[]`,
  `broll_keywords`, `captions`, `thumbnail`, `bgm`, `titles[]`.
- If the video webhook is empty or fails, `generateVideoSuggestionsLocally()`
  produces heuristic suggestions client-side — keep this fallback working.

## Conventions

- All UI copy is **Traditional Chinese (zh-Hant)**; keep new UI text in the
  same register (editorial/newsroom tone).
- Styling uses CSS custom properties defined in `:root` (brand blues/purples,
  teal `--video` palette for the video section). Reuse them rather than adding
  new hard-coded colors.
- Always escape user/webhook-provided strings with the existing `esc()` helper
  before inserting into `innerHTML`.
- Secrets: the HeyGen API key is entered by the user at runtime and must never
  be persisted, logged, or committed. Do not hard-code API keys or webhook URLs.
- No dependencies: keep the page self-contained (no CDN scripts) so it can be
  opened directly from disk or static hosting.

## Testing changes

Open `index.html` in a browser (`python3 -m http.server` works). Without real
webhooks, verify the form validation, the local video-suggestion fallback, and
the copy/clear buttons.
