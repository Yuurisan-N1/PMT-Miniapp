<div align="center">

<img width="100%" alt="header" src="https://capsule-render.vercel.app/api?type=waving&height=210&text=PMT%20Bot&fontAlign=50&fontAlignY=36&fontSize=56&desc=Daily%20Check-in%20%7C%20Mining%20%7C%20Ads%20%7C%20Tasks%20%7C%20Games%20%7C%20Multi-Account&descAlign=50&descAlignY=58"/>

<img alt="typing" src="https://readme-typing-svg.demolab.com?font=Inter&size=18&duration=3000&pause=650&center=true&vCenter=true&width=900&lines=Auto+Daily+Check-in+%7C+Earn+PMT+Reward;Auto+Mining+%7C+Claim+%26+Restart+Session;Auto+Ads+%7C+Cloudflare+Challenge+Solved+Per+Slot;Auto+Tasks+%7C+Start+Wait+%26+Verify;Auto+Play+4+Games+%7C+Gem+%2F+Wheel+%2F+XO+%2F+Fruit"/>

<p>
  <img alt="platform" src="https://img.shields.io/badge/Platform-PMT%20Miniapp-111111"/>
  <img alt="multi-account" src="https://img.shields.io/badge/Multi--Account-Supported-111111"/>
  <img alt="proxy" src="https://img.shields.io/badge/Proxy-Supported-111111"/>
  <img alt="author" src="https://img.shields.io/badge/by-Yuurisandesu-111111"/>
</p>

<p>
  <b>PMT Bot</b> is a full automation bot for the PMT Telegram Miniapp.<br/>
  It handles the complete cycle: claiming the daily check-in, managing the mining session, claiming ad rewards with automatic Cloudflare challenge solving per slot, completing all pending tasks, and playing all four mini-games with automatic challenge solving per round, all running automatically across multiple accounts with per-account device fingerprint caching, proxy support, and a live countdown between cycles.<br/>
  Built and distributed by <b>Yuurisandesu</b>.
</p>

</div>

---

## Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Bot](#running-the-bot)
- [Features](#features)
- [File Structure](#file-structure)
- [Disclaimer](#disclaimer)

---

## Requirements

- Python `3.12+` (only needed to run the downloader script)

---

## Installation

**Clone the repository:**

```bash
git clone https://github.com/Yuurisan-N1/PMT-Miniapp.git
cd PMT-Miniapp
```

**Install downloader dependencies:**

```bash
pip install requests colorama yuurisan
```

**Download the binary for your platform:**

```bash
python bot.py
```

The script shows a numbered menu:

```
1. PMT Linux ARM64
2. PMT Linux AMD64
3. Windows (PowerShell / CMD)
```

Enter the number for your platform. The binary downloads with a live progress bar and is set to executable automatically on Linux.

Or download manually from the Releases page:
https://github.com/Yuurisan-N1/PMT-Miniapp/releases/latest

| File | Platform |
|---|---|
| `PMT.exe` | Windows x86_64 |
| `pmt-linux-amd64` | Linux x86_64 |
| `pmt-linux-arm64` | Linux ARM64 |

**Linux after manual download:**

```bash
chmod +x pmt-linux-amd64
```

Place the binary in the same folder as your `data.txt`, `proxy.txt`, and `config.json` before running.

---

## Configuration

### 1. Accounts (data.txt)

Fill `data.txt` with Telegram WebApp `initData` for each account, one per line:

```
user=%7B%22id%22...&hash=abc123
user=%7B%22id%22...&hash=def456
```

> `initData` can be obtained from the browser DevTools when opening PMT on Telegram Web.

### 2. Proxy (proxy.txt)

Fill `proxy.txt` with proxies, one per line (optional, leave empty to run without proxy):

```
host:port
host:port:user:pass
http://user:pass@host:port
```

Proxies are assigned to accounts by index in round-robin order.

### 3. Bot Settings (config.json)

`sleep_seconds` controls how many seconds the bot waits between cycles. If `config.json` is missing, the bot falls back to a default of `3000` seconds.

---

## Running the Bot

**Linux:**

```bash
./pmt-linux-amd64
```

**Linux ARM64:**

```bash
./pmt-linux-arm64
```

**Windows:**

```bash
.\PMT.exe
```

Press `Ctrl+C` at any time to stop the bot cleanly.

---

## Features

### Daily Check-in
The bot reads the `claimed` flag from the account state. If not yet claimed today, it submits the daily check-in and logs the PMT reward. If already claimed, it is skipped.

### Auto Mining
If no session is running, the bot starts a new mining session and logs the expected PMT reward. If a session is active and has completed its duration, the bot claims the reward and immediately starts a new session. If still running, the remaining time is logged in `HH:MM:SS` format.

### Auto Ads
The bot reads all ad company configs from the account state and calculates remaining slots per company for the day. For each slot, it solves the Cloudflare challenge automatically before submitting the ad claim. Up to 3 retries are made per slot if the challenge solve fails. Each claimed slot logs the company name and updated PMT balance. A 2-second delay is applied between slots.

### Auto Tasks
The bot compares the full task list against completed task IDs. For each pending task, it sends a start request, waits the server-specified `waitSeconds` plus 1 second, then sends a verify request to claim the PMT reward. Each verified task logs the reward amount. A 1-second delay is applied between tasks.

### Auto Games
The bot plays all four mini-games: Gem, Wheel, XO, and Fruit. The daily play limit per game is read from the server config. For each remaining turn per game, the bot solves the Cloudflare challenge automatically before submitting the play with a fixed score per game type. Up to 3 retries are made per turn if the challenge solve fails. Each successful play logs the game name and updated PMT balance. A 2-second delay is applied between turns.

### Per-Account Device Fingerprint
Each account gets a unique device profile generated on first run from a pool of 22 Android devices (Samsung, Xiaomi, OnePlus, Oppo, Vivo, Realme, Google Pixel), 4 Android versions, 14 Telegram versions, 7 Chromium versions, and 16 WebView app packages. The profile is saved to `device.json` keyed by Telegram user ID and reused on all subsequent cycles to keep the device identity consistent.

### Multi Account
All accounts in `data.txt` are processed sequentially within every cycle. Username and PMT balance are logged at the start of each account. The cycle number and total account count are logged at the start of each round.

### Proxy Support
Proxies are loaded from `proxy.txt` and assigned to accounts by position in round-robin order. Proxy credentials are masked in log output. Running without proxies is fully supported.

### Auto Countdown
After all accounts complete a cycle, the bot displays a live `HH:MM:SS` countdown until the next cycle starts.

---

## File Structure

```text
PMT-Miniapp/
├── PMT.exe              # Windows binary
├── pmt-linux-amd64      # Linux x86_64 binary
├── pmt-linux-arm64      # Linux ARM64 binary
├── bot.py               # Interactive downloader script
├── config.json          # Sleep duration between cycles
├── data.txt             # Account initData, one per line
├── proxy.txt            # Proxy list, one per line (optional)
├── device.json          # Per-account device fingerprint cache (auto-generated)
├── LICENSE              # License file
└── utils/
    └── banner.py        # Banner using yuurisan module
```

---

## Disclaimer

This tool is built for educational and technical exploration purposes. Use it wisely and at your own responsibility.

---

<div align="center">
<img width="100%" alt="footer" src="https://capsule-render.vercel.app/api?type=waving&height=120&section=footer"/>
</div>