# Jidevm2 — free Windows cloud PC on GitHub

A Windows PC that runs for about 5h50 per session on GitHub's machines,
reachable only through Jide's private Tailscale network.

**Current build: just the PC and the login.** Nothing else installs.
(Roblox Studio + MCP + RustDesk were designed and may come back later —
that full draft is kept outside this repo as a backup. This file is the
simple one that is proven to work.)

## Start it

1. Actions tab → **Cloud PC v2** → **Run workflow**
   - `chain = 0` → one session (~5h50)
   - `chain = 2` → this session plus 2 more, back to back
2. **Always start a FRESH run.** The "Re-run" button replays the exact
   file from the old run — if the file has changed since, re-run uses
   the OLD one. That mistake cost us a night.

## Connect

1. Wait ~3 minutes after starting the run.
2. Open Tailscale and find the newest `jide-cloudpc` machine (old dead
   ones linger for a while with names like `jide-cloudpc-1`, `-2`... —
   the newest, highest number is the live one). Or read the exact
   address in the run's "CLOUD PC READY" banner.
3. RDP to that address:
   - User **RDP** (Jide) or **CLAUDE** (Muse) — both can be in at once
   - Password: the `$testPw` value written in `.github/workflows/main.yml`

## The password rule (learned the hard way)

The password lives **in the workflow file on purpose** — secrets kept
getting mangled or mistyped on the way in, so we removed the handoff.
The PC can only be reached through the private Tailscale network,
which is the real wall.

**Any password put in the file must be 14 characters or fewer.**
The Windows command that creates the users stops and asks
"password is longer than 14 characters, continue? (Y/N)" — and inside
a GitHub run nobody can answer, so the users silently never get
created and every login fails. Short and mixed beats long here.

To change the password: edit the `$testPw` line, keep it ≤14 chars
with capitals, small letters and a number or symbol, and start a
fresh run.

## The one remaining secret

`TAILSCALE_AUTHKEY` (repo Settings → Secrets) — how the PC joins the
network. Reusable + Ephemeral, generated in Tailscale → Settings →
Keys. If a run fails at "Join Tailscale" with "invalid key", the key
was deleted or replaced — generate a fresh one and update the secret.

## Honest notes

- Each machine is fresh — files do **not** carry over between
  sessions. Copy out anything you need before a session ends.
- GitHub's rules say Actions is for building and testing software;
  using it as a remote PC is outside that. Jobs can be killed and
  heavy abuse can get a repo or account limited. Keep it modest.
- The repo is public, so the password in the file is public too.
  That is accepted for this throwaway PC because Tailscale is the
  door — never reuse this password anywhere else.
