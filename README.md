# NullClaw on Windows — Complete Setup Guide

> A step-by-step walkthrough of setting up NullClaw (Zig-based AI assistant infrastructure) on Windows 11, integrating Telegram bot, Groq AI, and fixing the `NameTooLong` provider bug.

---

## What is NullClaw?

[NullClaw](https://github.com/nullclaw/nullclaw) is the fastest, smallest fully autonomous AI assistant infrastructure — a static 678 KB Zig binary that boots in under 2ms, runs on anything with a CPU, and supports 22+ AI providers and 18+ messaging channels out of the box.

---

## My Setup

- **OS:** Windows 11
- **AI Provider:** Groq (free tier)
- **Model:** `llama-3.3-70b-versatile`
- **Channels:** Telegram
- **NullClaw Version:** `2026.3.7`
- **Zig Version:** `0.15.2` (exact — required)

---

## Prerequisites

Before starting, make sure you have:

- Windows 11 (x86_64)
- PowerShell (run as Administrator for PATH setup)
- Git installed
- A free [Groq API key](https://console.groq.com/keys)
- A Telegram bot token from [@BotFather](https://t.me/BotFather)

---

## Step 1 — Install Zig 0.15.2

NullClaw requires **exactly Zig 0.15.2**. No other version works.

1. Download from: https://ziglang.org/download/0.15.2/zig-windows-x86_64-0.15.2.zip
2. Extract to `C:\zig`
3. Add to PATH (run PowerShell as Admin):

```powershell
[Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\zig", "Machine")
```

4. Restart PowerShell and verify:

```powershell
zig version
# Must print: 0.15.2
```

---

## Step 2 — Clone and Build NullClaw

```powershell
git clone https://github.com/nullclaw/nullclaw.git
cd nullclaw
zig build -Doptimize=ReleaseSmall
```

Verify the binary:

```powershell
dir zig-out\bin\nullclaw.exe
# Should be ~678 KB
```

---

## Step 3 — Add NullClaw to PATH

**Option A — User-level (no Admin needed):**

```powershell
[Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\Users\YOUR_USERNAME\nullclaw\zig-out\bin", "User")
```

**Option B — Run with full path (no PATH setup needed):**

```powershell
C:\Users\YOUR_USERNAME\nullclaw\zig-out\bin\nullclaw.exe --version
```

After PATH is set, reload in current session:

```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
nullclaw --version
# Should print: nullclaw 2026.3.7
```

---

## Step 4 — Run Interactive Onboarding

```powershell
nullclaw onboard --interactive
```

Follow the 8-step wizard. Here are the exact choices I made:

| Step | Choice |
|------|--------|
| Step 1 — Provider | `2` → Groq |
| Step 2 — API Key | Paste your `gsk_...` key |
| Step 3 — Model | `4` → `llama-3.3-70b-versatile` |
| Step 4 — Memory | `2` → SQLite with FTS5 (recommended) |
| Step 5 — Tunnel | `2` → Cloudflare |
| Step 6 — Autonomy | `3` → fully_autonomous |
| Step 7 — Channels | `1` → Telegram → paste bot token → allow_from `*` |
| Step 8 — Workspace | Press Enter (use default) |

---

## Step 5 — Create Telegram Bot

1. Open Telegram → search `@BotFather`
2. Send `/newbot`
3. Give it a name and username
4. Copy the token (looks like `123456789:ABCdef...`)
5. Paste when onboarding asks for Telegram bot token



## Step 6 — Test and Run

powershell
# Run diagnostics
nullclaw doctor

# Test the AI responds
nullclaw agent -m "Hello! Who are you?"

# Start gateway (Telegram goes live)
nullclaw gateway

# Run as background service
nullclaw service install
nullclaw service start
