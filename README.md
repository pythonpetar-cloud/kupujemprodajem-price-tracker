# Price-Tracking Bot

A Selenium-based tool that searches KupujemProdajem (a Serbian classifieds site)
for any item you choose, and emails you when a listing drops below a price
you set.

## Why

Started as a script tracking one specific lens (Tamron 28-75mm f/2.8) at a
fixed price threshold. Rebuilt to work for any item, any price, any
recipient — so the same tool can be reused for anything instead of editing
code every time you want to track something new.

## How it works

1. Prompts for a search term, a maximum price (EUR), and a recipient email
   (with a `.env`-configured default you can just accept)
2. Opens a headless Chrome session and searches KupujemProdajem for that term
3. Scrolls the page to trigger lazy-loaded listings until no new results
   appear, so nothing gets missed
4. Parses each listing's price, converting RSD to EUR where needed
5. Emails a summary of all matching listings, flagging which ones are below
   your target price

## Features

- Fully interactive: no hardcoded item or price, works for anything sold on
  the site
- Handles both EUR and RSD-denominated listings, converting to a common
  currency for comparison
- Scrolls to load all listings before parsing, rather than only the first
  visible batch
- Graceful per-listing error handling — one malformed listing doesn't stop
  the whole run
- Falls back to environment variables (`SEARCH_TERM`, `MAX_PRICE_EUR`,
  `GMAIL_PRIMALAC`) when run non-interactively, so it can still run in a
  headless/CI environment without hanging on `input()`

## Tech stack

Python · Selenium · smtplib (Gmail SMTP) · python-dotenv

## Running locally

```bash
git clone https://github.com/pythonpetar-cloud/kupujemprodajem-price-tracker.git
cd kupujemprodajem-price-tracker
pip install selenium python-dotenv
```

Create a `.env` file:

```
GMAIL_KORISNIK=your_gmail_address@gmail.com
GMAIL_APP_PASSWORD=your_gmail_app_password
GMAIL_PRIMALAC=default_recipient@example.com
```

(`GMAIL_APP_PASSWORD` is a Gmail [App Password](https://myaccount.google.com/apppasswords),
not your regular account password.)

Run it:

```bash
python main.py
```

You'll be prompted for what to search, your price threshold, and who to
email (press Enter to use the `.env` default recipient).

## History

This originally ran on an hourly schedule via GitHub Actions, tracking one
fixed lens listing. That automation has since been retired in favor of
running the tool on demand for whatever item is currently of interest —
simpler to reason about, and avoids the complexity of keeping scheduled
credentials and headless-input handling in sync with an interactive tool.

## Status

Personal-use tool, stable. Not actively adding features, but open to it if
a new use case comes up.
