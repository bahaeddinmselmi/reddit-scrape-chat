# 💬 Reddit Scraper & Chat Converter

> A robust pipeline to scrape Reddit threads (using Playwright) and convert them into high-quality **Chat SFT (Supervised Fine-Tuning) Datasets**.

[![Python](https://img.shields.io/badge/Python-3.11%2B-blue)](https://python.org)
[![Playwright](https://img.shields.io/badge/Playwright-Enabled-green)](https://playwright.dev)
[![Format](https://img.shields.io/badge/Format-JSONL_Chat-orange)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 📖 Overview

This tool allows you to build conversational datasets from any Subreddit. It solves two main problems:
1.  **Deep Scraping**: using a headless browser to expand comments and load nested replies (which API-only scrapers often miss).
2.  **Chat Formatting**: automatically converting "Post + Top Comments" into `{messages: [...]}` JSONL format, ready for training Chat models (like Llama 3, Mistral, or Qwen).

## ✨ Key Features
*   **Headless Browsing**: Bypasses strict API limits using real browser sessions (Playwright).
*   **Cookie Authentication**: Supports exporting your own `cookies.json` to scrape restricted/age-gated content.
*   **Smart Conversion**: Filters low-quality comments and structures threads as user-assistant turns.
*   **No Credentials Leaked**: Designed to keep cookies local and ignored by git.

## 🛠 Installation

1.  **Install Python Dependencies**:
    ```bash
    pip install -r requirements.txt
    ```

2.  **Install Playwright Browsers**:
    ```bash
    python -m playwright install chromium
    ```

## 🚀 Usage Guide

### 1. Cookies Setup (Optional but Recommended)
For best results (and to avoid rate limits), login to Reddit on your browser, use a "Cookie-Editor" extension to export cookies as JSON, and save them as `cookies.json` in the root folder.

### 2. Scrape Data
Collection script supports `old.reddit.com` for faster DOM parsing.

```bash
python collectors/collect_reddit_playwright.py \
  --sub Tunisia \
  --limit 500 \
  --sort top \
  --with_comments \
  --out_posts data/raw/posts.jsonl \
  --out_comments data/raw/comments.jsonl \
  --cookies_file cookies.json \
  --headed
```

### 3. Convert to Training Data
Transform raw scrapes into training-ready JSONL.

```bash
python tools/convert_reddit_threads_to_chat.py \
  --posts data/raw/posts.jsonl \
  --comments data/raw/comments.jsonl \
  --out_dir data/splits \
  --min_len 20 \
  --val_ratio 0.1
```

**Output Example (`data/splits/reddit_train.jsonl`):**
```json
{
  "messages": [
    {"role": "user", "content": "How do I make a Kafka consumer in Python?"},
    {"role": "assistant", "content": "You can use the confluent-kafka library..."}
  ]
}
```

## 📂 Project Structure
*   `collectors/`: Playwright scripts for scraping.
*   `tools/`: Conversion logic for Chat SFT formatting.
*   `data/`: Storage for raw and processed datasets.

## 📄 License
MIT
