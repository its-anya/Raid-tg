# Raid-tg — Telegram Raid botnet

**WARNING: This repository contains code that automates large-scale Telegram account actions (spamming, mass reporting, poll voting, forwarding, join/leave, etc.). Running this code against real users, channels, or groups may violate Telegram's Terms of Service, local laws, or platform policies. Do not use this project to harass, spam, or attack others.**

**Purpose**
- `raid.py` is a command-line controller that coordinates many automated actions via the `raidfunctions/` package. It reads `config.toml`, enumerates account session files in `tgaccs/`, and dispatches tasks (register, spam, reports, stickers, profile updates, views, polls).

**Quick overview of structure**
- `raid.py` — main CLI and control flow
- `config.toml` — configuration (API credentials, locale, raid options)
- `tgaccs/` — account session files (one per account)
- `spamblock/` — accounts marked as spam-blocked
- `raidfiles/` — media files used for media spam
- `args.txt`, `name.txt`, `surname.txt` — data files used for messages and name generation
- `raidfunctions/` — implementation modules: `register`, `tgraid`, `start_spam`, `report`, `addsticker`, `leave`, `see`, `polls`, `joinleave`, `additional`.

**Notable behavior**
- `raid.py` supports Russian (`lang = "ru"`) or English (any value other than `ru`).
- The script removes stray `.session-journal` files in `tgaccs` at startup and prints counts of active accounts and spamblocked accounts.
- Most actions iterate over all sessions in `tgaccs` and call a class/method from `raidfunctions`. Many per-account loops use broad `except:` which quietly skips failures.

**Setup (safe, local testing)**
1. Install Python 3.8+ and create a virtual environment (recommended).
2. Install dependencies:

```powershell
pip install -r requirements.txt
```

3. Configure `config.toml` before running. Example (replace placeholders):

```toml
[authorization]
# Replace with your Telegram API credentials (from https://my.telegram.org)
api_id = 123456789            # integer
api_hash = "your_api_hash_here"  # quoted string

[locale]
lang = "ru"  # or "en" (any non-"ru" value triggers English UI)

[raid]
message = "repeat text"
```

- Important: `api_id` must be an integer and `api_hash` must be a quoted string. If `api_id` is left unquoted text, the TOML parser will raise an error.

**Run**
- To start the CLI (again: do not point this at real users unless you have explicit permission and a legitimate, legal reason):

```powershell
python raid.py
```

**Safety & auditing guidance (recommended before running)**
- Treat this repo as potentially dangerous: do not execute it on a network or with real accounts unless you understand the consequences.
- For safe code analysis or testing, replace network calls in `raidfunctions/` with no-op stubs or mocks. For example, in each module that calls Telethon/requests, temporarily return success without sending traffic.
- Inspect imports in `raidfunctions/` to see which external libraries are used (e.g., `telethon`) and where sessions or network I/O occur.
- Keep all testing confined to an isolated environment and test accounts you control and are permitted to use.

**How to switch language to English**
- Edit `config.toml` and set `lang = "en"` (or any value other than `"ru"`). The script shows English strings when `lang != "ru"`.

**How to fix the TOML parse failure shown in runtime**
- If you see an error like `TomlDecodeError: invalid literal for int() with base 0: 'api id'`, that means `api_id` is not set to a valid integer. Open `config.toml` and replace the placeholder line like:

```toml
api_id = api id
```

with

```toml
api_id = 123456789
```

(Replace `123456789` with the actual integer `api_id` from https://my.telegram.org.)

**Next steps / options I can help with**
- Provide a per-file summary of `raidfunctions/` modules (read-only analysis).
- Create a safe mock version of the network layer so `raid.py` can be executed locally without sending network traffic.
- Patch `config.toml` to a valid placeholder (I can update it for you).

If you want me to proceed with any of the above, tell me which option to take.

---
*This README is informational and intentionally avoids instructions for malicious use.*
