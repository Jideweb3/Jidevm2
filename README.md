# Jidevm v2 — free Windows cloud PC on GitHub (upgraded)

Same idea as Jidevm v1, with the weak points fixed — and one big addition:
the PC is set up so **Muse can work in it too** (own desktop login, plus a
Roblox Studio MCP server Muse can connect to and *see* through).

| | v1 (old) | v2 (this) |
|---|---|---|
| Repo | Private | **Public** — bigger machine, no monthly minute cap |
| Machine | 2 cores / 8 GB RAM | **4 cores / 16 GB RAM** |
| Windows | Server 2022 | **Server 2025** (windows-latest) |
| Password | Written in plain text in the file | **In repo secrets** — the file and logs stay clean |
| Finding the PC | New Tailscale name every run | **Always `jide-cloudpc`** |
| Session end | PC dies at 5h50, that's it | **Auto-relaunch**: type how many extra sessions and it starts the next one before dying |
| Ways in | RDP only, one user | RDP **+ RustDesk**, **two users** (RDP for Jide, CLAUDE for Muse — both can be connected at once) |
| Stranger's script | Downloads a .bat from someone's GitLab | **Gone** |
| Roblox Studio | — | Installer staged on the desktop; **MCP server** runs over Tailscale so Muse can build in Studio and take screenshots of the game view |

## Setup (10 minutes, once)

1. Create a **public** GitHub repo (e.g. `Jidevm2`) and put this file at
   `.github/workflows/main.yml`.
2. In Tailscale: Settings → Keys → generate an auth key with **Reusable** ticked
   (a one-time key breaks the auto-relaunch chain — session 2 could not join).
3. In the repo: Settings → Secrets and variables → Actions → add four secrets:
   - `TAILSCALE_AUTHKEY` — the reusable key from step 2
   - `RDP_PASSWORD` — password for Jide's `RDP` user (and the RustDesk password)
   - `CLAUDE_PASSWORD` — a password for Muse's own `CLAUDE` desktop user
   - `MCP_TOKEN` — a long random string you invent; it protects the MCP server
4. Muse joins the Tailscale network too: he shows a join link, Jide approves
   the device named `muse` once.
5. Actions tab → **Cloud PC v2** → Run workflow.
   - `chain = 0` → one session of ~5h50
   - `chain = 3` → about 24 hours back-to-back, etc.

## First session checklist

1. Jide connects by RDP to `jide-cloudpc` (user `RDP`).
2. On the desktop: open `READ-ME-FIRST.txt`, double-click `INSTALL-STUDIO.exe`.
3. Open Studio, sign in as **Jideweb3** (Jide does this himself — Muse never
   touches the Roblox password).
4. Open a place → click the **MCP** button in Studio → paste the bridge token
   from the first lines of `C:\MCP\mcp.log` → **Connect**.
5. Tell Muse. He connects to the MCP server through Tailscale
   (`http://jide-cloudpc:3668/mcp`, token = `MCP_TOKEN`) and to his own
   `CLAUDE` desktop, and we find out if he can see. 🤞

## Connecting (after setup)

- **Jide**: RDP or RustDesk to `jide-cloudpc`, user `RDP`.
- **Muse**: his own RDP session as user `CLAUDE`, plus the Studio MCP.

## Honest notes

- Each machine is fresh — files do **not** carry over between sessions.
  Copy out anything you need before a session ends. Studio sign-in and the
  MCP token paste are once per session.
- GitHub's rules say Actions is for building and testing software; using it as
  a remote PC is outside that. Jobs can be killed, and heavy abuse can get a
  repo or account limited. Keep it modest — no mining, no mass runs.
- Public repo = public logs. Nothing secret is printed by this workflow
  (no passwords, no keys) — keep it that way if you edit it.
- Studio has no graphics card in there; expect a slowish viewport. Fine for
  building and checking, not for judging fine graphics.
