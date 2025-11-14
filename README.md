# reddit-scrape-chat

Playwright-based scraper for Reddit threads (old.reddit.com) and converter to chat-format JSONL for supervised fine-tuning (SFT).

- Scrapes r/Tunisia (or any subreddit) posts and expands comments.
- Supports login via browser cookies JSON (no credentials required).
- Converts (post → best comment) into `messages` JSONL suitable for chat SFT.
- Ships without any secrets. Cookies are not committed and are ignored by .gitignore.

## Quick start (Windows, Python 3.11)

1) Create and activate a virtualenv
```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

2) Install dependencies and Chromium
```powershell
pip install -r requirements.txt
python -m playwright install chromium
```

3) Export Reddit cookies with Cookie-Editor
- Install the "Cookie-Editor" browser extension (Chrome/Edge/Firefox).
- Log in to https://www.reddit.com/.
- Open the Cookie-Editor popup → Export → Copy as JSON.
- Save to a file at: `cookies.json` in the project root (or any path you prefer).

4) Scrape posts + comments
```powershell
python collectors\collect_reddit_playwright.py ^
  --sub Tunisia ^
  --limit 300 ^
  --sort new ^
  --with_comments ^
  --out_posts data\raw\reddit_posts.jsonl ^
  --out_comments data\raw\reddit_comments.jsonl ^
  --storage .cache\reddit_storage.json ^
  --cookies_file cookies.json ^
  --headed
```

5) Convert threads to chat SFT JSONL
```powershell
python tools\convert_reddit_threads_to_chat.py ^
  --posts data\raw\reddit_posts.jsonl ^
  --comments data\raw\reddit_comments.jsonl ^
  --out_dir data\splits ^
  --min_len 24 ^
  --val_ratio 0.05 ^
  --min_score 1
```
Outputs:
- `data/splits/reddit_train.jsonl`
- `data/splits/reddit_val.jsonl`

## Notes
- Cookies are sensitive. Do NOT commit them. This repo ignores `cookies.json` and `.cache/` by default.
- If cookies don’t work, add `--interactive_login` to complete a manual login in the opened browser window; the session will be saved to `.cache/reddit_storage.json`.
- You can change subreddit, sorting, and limits via CLI flags.
