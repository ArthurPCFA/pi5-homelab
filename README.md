# pi5-homelab
Raspberry Pi 5 as a combined home router + NAS

A Raspberry Pi 5 that runs as both a home router/firewall and a
network-attached storage (NAS) server.

## Status
🚧 In progress — currently at Step 1 (physical assembly).

## Hardware
- Raspberry Pi 5 (8GB) — Vilros Starter Kit MAX (active cooler, 27W USB-C PSU, 128GB micro-SD)
- Geekworm X1001 PCIe-to-M.2 HAT
- Crucial P3 Plus 1TB NVMe SSD (CT1000P3SSD8)
- TP-Link UE306 USB 3.0-to-Gigabit Ethernet adapter

## Architecture (summary)
- **OS:** Raspberry Pi OS Lite 64-bit, booting from the NVMe SSD
- **WAN:** `eth0` (built-in port) -> modem
- **LAN:** `eth1` (TP-Link UE306) -> home network
- **Routing:** nftables (NAT + firewall) + dnsmasq (DHCP + DNS)
- **NAS:** OpenMediaVault serving SMB/Samba shares from the NVMe, fenced off from `eth0`

## Repo layout
- `docs/architecture.md` — network diagram + interface map
- `docs/services.md` — what runs where (ports, paths)
- `docs/setup/` — numbered build steps
- `docs/lessons-learned.md` — gotchas and fixes
- `configs/` — sanitized config files
- `scripts/` — helper scripts

## Progress
- [x] Step 0 — Repo created
- [ ] Step 1 — Physical assembly
- [ ] Step 2 — First boot + firmware check
- [ ] Step 3 — OS on NVMe
- [ ] Step 4 — Base system prep
- [ ] Step 5 — NVMe data partition
- [ ] Step 6 — OpenMediaVault + shares
- [ ] Step 7 — Router (nftables + dnsmasq)
- [ ] Step 8 — Fence OMV from WAN
- [ ] Step 9 — Integration test + lessons learned
