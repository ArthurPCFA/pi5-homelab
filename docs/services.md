# Services

What runs on the Pi, which interface it should answer on, its ports, and where
its config lives. The guiding rule: **everything user-facing answers on `eth1`
(LAN) only; nothing answers on `eth0` (WAN) except what routing strictly
requires.**

## Service map
| Service | Purpose | Binds to | Ports | Config |
|---|---|---|---|---|
| `nftables` | NAT + firewall (the router core) | kernel, both NICs | n/a (not a listener) | `/etc/nftables.conf` |
| `dnsmasq` | DHCP + DNS for the LAN | `eth1` only | 67/UDP (DHCP), 53 TCP+UDP (DNS) | `/etc/dnsmasq.conf`, `/etc/dnsmasq.d/` |
| OpenMediaVault web UI (nginx) | NAS admin interface | `eth1` (enforced via firewall) | 80/TCP (HTTP) | OMV-managed; master `/etc/openmediavault/config.xml` |
| Samba (`smbd`, `nmbd`) | SMB/CIFS file shares | `eth1` (enforced via firewall) | 445/TCP, 139/TCP, 137-138/UDP | OMV-generated `/etc/samba/smb.conf` |
| `ssh` (`sshd`) | Remote admin | LAN only (recommended) | 22/TCP | `/etc/ssh/sshd_config` |
| `smartd` (smartmontools) | NVMe SMART health monitoring | n/a (local) | n/a | `/etc/smartd.conf` (OMV can manage) |

## Notes per service

### nftables — the router core
Not a listening service; it is the kernel's packet filter. Responsibilities:
masquerade (NAT) outbound traffic on `eth0`, forward `eth1 -> eth0`, and filter
the INPUT chain so the Pi itself only accepts management/NAS traffic on `eth1`.
**Detailed rules live in the router-config notes, not here.**

### dnsmasq — DHCP + DNS
Hands out leases to LAN clients and resolves DNS for them. Must be bound to
`eth1` only (`interface=eth1`, `bind-interfaces`) so it never offers DHCP onto
the WAN. Planned pool: `192.168.10.50` – `192.168.10.200` (confirm with
router-config notes).

### OpenMediaVault web UI
Served by nginx on port 80. **Critical:** OMV has no setting to bind its web UI
to a single NIC, so by default it answers on every interface. The fence is a
firewall rule (Step 8) dropping ports 80/443 on `eth0`. Do not rely on an OMV
setting for this — there isn't one.

### Samba
`smbd`/`nmbd` provide the actual file shares. OMV regenerates `smb.conf` from
its own config, so hand-edits there get overwritten — the reliable way to keep
Samba off the WAN is the same `eth0` firewall block (Step 8), not editing
`smb.conf`.

### SSH
Lock to the LAN once routing is live (firewall, or `ListenAddress`). Key-based
auth only; password login disabled.

### smartd
Watches the NVMe's SMART attributes and can email/alert on early drive trouble.
No network exposure.

## The WAN exposure checklist
After Step 8, **nothing** in this list should answer on `eth0` except the
minimum the router needs. Verify from an outside host with:
`nmap <WAN-IP>` — expect a closed/filtered result on 22, 53, 80, 139, 445.
