# Pexels Video Downloader

**Free open-source Python tool to bulk download Pexels stock videos** by category — perfect for YouTube Shorts, Instagram Reels, TikTok backgrounds, and content automation.

[License: MIT](LICENSE)
[Python 3.10+](https://www.python.org/)
[Pexels API](https://www.pexels.com/api/)
[Free forever](LICENSE)

Configure everything in `config.yaml`. The only thing you need is a **free** [Pexels API key](https://www.pexels.com/api/).

## Why this tool?


| Need                        | What you get                                         |
| --------------------------- | ---------------------------------------------------- |
| Bulk download Pexels videos | Category folders + multiple search queries           |
| Vertical / Shorts footage   | `orientation: portrait` (or landscape / square)      |
| Organized library           | Auto `LIBRARY.md` with per-clip **Scene** titles for AI matching |
| Safe to re-run              | Dedupes IDs, resumes missing clips, rate-limit aware |
| Free to use                 | MIT license — use commercially, modify, share        |


This is **not** a scraper of the website. It uses the official **Pexels Video API**.

## Features

- Download **portrait**, **landscape**, or **square** stock videos
- Multiple search queries per category for better variety
- Filter by duration and preferred resolution (e.g. 1080×1920)
- Deduplicate across categories (same Pexels ID used once)
- Resumable downloads — skip folders that are already full
- Rate-limit friendly delays + automatic backoff on HTTP 429
- Security-minded: API key stays in `.env`, path traversal blocked, download hosts allowlisted
- Generates `LIBRARY.md` with **per-clip Scene descriptions** (from Pexels URL titles) for AI matching
- `--enrich-library` / `--enrich-state` upgrade an existing catalog without re-downloading videos

## Quick start (free)

```bash
# 1. Clone
git clone https://github.com/anaskld/pexels-video-downloader.git
cd pexels-video-downloader

# 2. Install dependencies
pip install -r requirements.txt

# 3. Add your free API key (never commit this file)
cp .env.example .env
# edit .env → PEXELS_API_KEY=...

# 4. Optional: edit categories / filters
nano config.yaml

# 5. Download
python download_pexels.py
```

Get a free API key: [https://www.pexels.com/api/](https://www.pexels.com/api/)

Videos are saved to `downloads/<category>/` plus `downloads/LIBRARY.md`.

## Configuration (`config.yaml`)

No code changes required — edit YAML only.


| Setting                                | Description                                          | Default               |
| -------------------------------------- | ---------------------------------------------------- | --------------------- |
| `per_category`                         | Videos per category                                  | `10`                  |
| `orientation`                          | `portrait` · `landscape` · `square`                  | `portrait`            |
| `size`                                 | Pexels min size: `large` / `medium` / `small` / `""` | `""`                  |
| `min_duration` / `max_duration`        | Duration filter (seconds)                            | `4` / `15`            |
| `preferred_width` / `preferred_height` | Prefer closest MP4 resolution                        | `1080` / `1920`       |
| `request_delay_seconds`                | Delay between API calls                              | `1.2`                 |
| `output_dir`                           | Output folder (relative to project)                  | `downloads`           |
| `state_file`                           | Resume / dedupe state                                | `download_state.json` |




### Example category

```yaml
categories:
  - folder: nature-calm
    description: Calm nature b-roll for Shorts backgrounds.
    count: 5                      # optional override of per_category
    queries:
      - calm forest trees
      - gentle ocean waves
```

The starter config includes Shorts-friendly mood folders (people, city, nature, abstract) plus psychology-oriented ones (relationship conflict/closeness, phone anxiety, insomnia, mirror, journaling, and more). Swap them for your niche.

## CLI reference

```bash
python download_pexels.py
python download_pexels.py --dry-run
python download_pexels.py --category nature-calm
python download_pexels.py --per-category 5
python download_pexels.py --config /path/to/other-config.yaml
python download_pexels.py --regen-manifest
python download_pexels.py --enrich-library /path/to/LIBRARY.md
python download_pexels.py --enrich-state
```


| Flag | Meaning |
| ---- | ------- |
| `--dry-run` | Search only; do not download |
| `--category NAME` | Download one category folder |
| `--per-category N` | Override count for this run |
| `--config PATH` | Use another YAML config |
| `--regen-manifest` | Rebuild `LIBRARY.md` only |
| `--enrich-library PATH` | Rewrite Scene titles from Pexels URLs (videos untouched) |
| `--enrich-state` | Backfill scenes in `download_state.json` + regenerate `LIBRARY.md` |

### AI matching tip

Pexels does not return titles via API — this tool parses the descriptive slug from each video URL (official Pexels guidance). `LIBRARY.md` stores that as **Scene**, so your matcher can distinguish “five women eating cakes” from “friends toasting drinks” inside the same category.

## Output layout

```
downloads/
  people-laughing/
    01-five-women-eating-cakes-together-7337217.mp4
  nature-calm/
    …
  LIBRARY.md
```

Filename pattern: `{index}-{scene-slug}-{pexels-id}.mp4`

## Rate limits

Pexels free tier is typically **200 requests/hour** and **20,000/month**. This tool:

- spaces requests (`request_delay_seconds`)
- retries after `429 Too Many Requests`
- prints remaining quota when the API provides it

A library of ~200 videos (e.g. 19 categories × 10) is well within the free tier.

## Security

- **API key** lives only in `.env` (gitignored). Never put it in `config.yaml` or the README.
- **No key logging** — the script never prints your secret.
- **Path safety** — `output_dir`, `state_file`, and category folder names cannot escape the project (`../` blocked).
- **Download allowlist** — files are fetched only from HTTPS Pexels / Vimeo CDN hosts.

See [SECURITY.md](SECURITY.md) for reporting issues.

## Free to use (MIT)

This project is **100% free and open source** under the [MIT License](LICENSE):

- Use it personally or commercially
- Modify and redistribute
- No attribution required for *this* code (though stars are appreciated ⭐)

Downloaded videos remain under the [Pexels License](https://www.pexels.com/license/). Credit creators when you can.

## Tips

1. Start with `--dry-run` or `--per-category 2` to test queries.
2. Delete clips you dislike, then re-run to refill the folder.
3. New downloads already use the Pexels scene title in the filename + `LIBRARY.md`.
4. Run `--regen-manifest` after manual cleanup; use `--enrich-library` on older catalogs.
5. Keep `.env` private. If you ever commit a key by mistake, revoke it in your Pexels dashboard and create a new one.
6. Download new psychology categories one at a time, e.g. `--category couple-conflict --per-category 20`.

## Disclaimer

Unofficial helper tool. Not affiliated with Pexels.

## Contributing

Issues and PRs welcome. Keep secrets out of commits; update `config.yaml` examples instead of hardcoding niche queries in the script.