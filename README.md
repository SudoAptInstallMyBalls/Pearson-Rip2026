# Pearson+ eTextbook Screenshot Scraper

Automated full-page screenshot tool for Pearson+ eTextbooks. Opens Firefox, lets you log in, then captures every page of your textbook as clean PNG screenshots with the navigation UI hidden.

## What It Does

1. Opens the Pearson+ reader in a real Firefox browser (headed, so you can log in)
2. Attempts to extract all page URNs from the Table of Contents for direct URL navigation
3. Falls back to clicking the Next arrow if TOC extraction fails
4. For each page:
   - Expands clipped/scrollable containers and iframes so nothing is cut off
   - Scrolls the page to trigger lazy-loaded images and figures
   - Hides the bottom nav bar and top "Continue in the app" banner
   - Takes a screenshot of just the content area (or full page)
   - Restores the UI for continued navigation

## Requirements

- Python 3.8+
- [Playwright](https://playwright.dev/python/)

## Setup

### Option A: Use the setup script (Windows)

Drop all files into `C:\Users\<you>\Desktop\JavaTextBook\` and double-click `setup.bat`. It installs Python dependencies and the Firefox browser for Playwright.

### Option B: Manual install

```bash
pip install playwright
playwright install firefox
```

## Usage

### Quick start (Windows)

Double-click `run.bat` — it asks for your URL and page count, then runs the scraper.

### Command line

```bash
# Basic — captures all pages
python pearson_scraper.py --url "https://plus.pearson.com/products/YOUR-PRODUCT-ID/pages/urn:pearson:entity:YOUR-PAGE-ID?userPreferredType=read"

# Limit to 30 pages
python pearson_scraper.py --url "..." --pages 30

# Resume from page 16 (if it crashed)
python pearson_scraper.py --url "..." --skip 15

# Full-page screenshots instead of just the content area
python pearson_scraper.py --url "..." --full-page

# Skip TOC extraction, just click Next repeatedly
python pearson_scraper.py --url "..." --no-toc

# Slower page loads? Increase the delay
python pearson_scraper.py --url "..." --delay 5
```

### Flags

| Flag | Description |
|------|-------------|
| `--url` | **(required)** URL of any page in your Pearson+ eTextbook |
| `--pages N` | Max number of pages to capture (default: all) |
| `--skip N` | Skip the first N pages (useful for resuming) |
| `--out DIR` | Output directory (default: `pearson_screenshots/`) |
| `--full-page` | Screenshot the entire page, not just the content area |
| `--no-toc` | Skip Table of Contents extraction, use Next button only |
| `--delay N` | Override wait time (seconds) after each page load |

## Output

Screenshots are saved as numbered PNGs:

```
pearson_screenshots/
  page_0001.png
  page_0002.png
  page_0003_Introduction.png
  ...
```

If TOC extraction succeeds, filenames include the section title.

## Files

| File | Purpose |
|------|---------|
| `pearson_scraper.py` | Main scraper script |
| `setup.bat` | One-time Windows setup (installs dependencies) |
| `run.bat` | Windows launcher (prompts for URL, runs the scraper) |

## Troubleshooting

**"Next button not found"** — The script tries many selectors, but Pearson may update their UI. Right-click the Next arrow in Firefox → Inspect, and note the element's `aria-label` or class. Update `NEXT_BUTTON_SELECTORS` at the top of `pearson_scraper.py`.

**Screenshots are blank or tiny** — Try `--full-page` to fall back to a full-page capture instead of targeting `.player-content`.

**Nav bar still showing** — The hide logic targets `position: fixed/sticky` elements. If Pearson changes their layout, the selectors in `HIDE_UI_JS` may need updating.

**Script crashes mid-run** — Use `--skip N` to resume from where it left off.

## Disclaimer

This tool is for personal, educational use with textbooks you have purchased access to. Respect Pearson's Terms of Service and copyright.
