# Changelog

Notable changes to Violin. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/);
versions follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.2] — 2026-09-03

Alongside the model fix, this release ships everything that landed on `main` after 0.1.1 but
was never published to PyPI.

### Fixed

- **Default translation and chat models replaced — the old ones were retired by Together and no longer resolve.**
  `deepseek-ai/DeepSeek-V4-Pro` → `deepseek-ai/DeepSeek-V4-Pro-0813`, and
  `Qwen/Qwen3.5-397B-A17B` → `zai-org/GLM-5.3-Flash`. Transcription (`openai/whisper-large-v3`)
  and TTS (`cartesia/sonic-3`) are unchanged.
- `chat.max_tokens` raised 512 → 4096. The default chat model reasons before it answers and
  ignores the `enable_thinking` hint, so a tight budget was spent entirely on thinking and the
  API returned an empty answer with `finish_reason: length`. The cap is an upper bound, not a
  prepayment — only generated tokens bill.
- Voice matching hit the same wall: its `max_tokens` went 400 → 4000.
- Together's translation cost rate in `pipeline/pricing.py` still matched the retired model.
  `DeepSeek-V4-Pro-0813` bills $1.32/$3.96 per million input/output tokens. Cost figures are
  informational, so this corrects reported numbers only.
- Job stats recorded an invalid status value.

### Added

- Per-job usage tracking for BYOK (bring-your-own-key) requests.
- Queue position is surfaced to clients while a job waits for a worker.
- Orphaned temp directories (`audiochunk_*`, `vidmerge_*`, `vidtrans_*`) left behind by crashed
  or SIGKILLed runs are now cleaned up at API startup and periodically thereafter, so a
  long-running server no longer fills its disk.

### Changed

- `api.max_workers` default raised 1 → 2, roughly doubling throughput on a 4-vCPU host.

### Known issues

- `cartesia/sonic-3` is unreliable on Chinese text containing Arabic numerals: it may drop
  trailing characters, misread the digits, or run away to a ~40-second mostly-silent clip.
  Which of the three happens varies per call. Text without digits is unaffected.
  `cartesia/sonic-3.5` is not a workaround — it is not available serverless and needs a
  dedicated endpoint.

## [0.1.1] — 2026-05-14

### Changed

- README: blog post link and minor fixes.

## [0.1.0] — 2026-05-14

Initial release.
