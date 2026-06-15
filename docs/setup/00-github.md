# Step 0 — Documentation Repo

This repository is the build log and reference for the Pi 5 router + NAS
project. This file records how the repo is organized and the conventions the
rest of the docs follow.

## Purpose
Every decision, config, and gotcha is written down as the build progresses, so
the project stays reproducible and reviewable later — and doubles as a
portfolio piece.

## Layout
| Path | Holds |
|---|---|
| `README.md` | Project summary, hardware, architecture snapshot, progress checklist |
| `sitemap.md` | Quick map of the repo structure |
| `docs/architecture.md` | Network topology, interface map, design decisions |
| `docs/services.md` | Each service: interface, ports, config paths |
| `docs/setup/0X-*.md` | One file per build step, in order |
| `docs/lessons-learned.md` | Mistakes, fixes, and gotchas as they come up |
| `configs/` | Sanitized config files (nftables, dnsmasq, smb.conf, ...) |
| `scripts/` | Helper scripts |

## Conventions
- Setup files are numbered (`00-`, `01-`, ...) so they read in build order.
- The README progress checklist is the source of truth: tick a box when a step
  is fully done and verified, not when it is merely started.
- Keep commits small, and say what changed and why in the message.
- Placeholder files exist only so Git can track otherwise-empty folders; delete
  each one when real content replaces it.

## Security — sanitize before committing
This is a PUBLIC repo. Before any real config lands in `configs/`, remove:
- passwords, password hashes, and Wi-Fi / WPA pre-shared keys
- private keys and SSH host keys
- the public WAN IP address and any ISP account details
- device MAC addresses
- tokens or API keys

Replace each with an obvious placeholder such as `<REDACTED>` or a
documentation-only IP like `192.0.2.10`. When in doubt, leave it out.

## Setup record
- Created on GitHub as a public repo named `pi5-homelab`.
- Visibility chosen as public so it can be shared directly as portfolio work.
- Folders seeded via the web UI (GitHub cannot store an empty folder).

## Clone

    git clone https://github.com/ArthurPCFA/pi5-homelab.git
