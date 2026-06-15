# Architecture

## Purpose
A single Raspberry Pi 5 acting as both the home **router/firewall** and a
**NAS** (network-attached storage) server, with the storage role prioritized
over peak routing performance.

## Hardware -> role
| Component | Role |
|---|---|
| Raspberry Pi 5 (8GB) | Compute / host for all services |
| Built-in Ethernet port (`eth0`) | **WAN** — uplink to the modem |
| TP-Link UE306 USB 3.0 adapter (`eth1`) | **LAN** — home network side |
| Crucial P3 Plus 1TB NVMe (on X1001 HAT) | Boot drive + NAS data storage |
| 128GB micro-SD card | Recovery / fallback boot only |

## Network topology
             Internet
                |
          +-----+-----+
          |   Modem   |
          +-----+-----+
                |  (WAN)
          eth0  |  built-in port, DHCP from modem
    +------------+------------------------+
    |          Raspberry Pi 5 (8GB)       |
    |                                     |
    |   nftables        -> NAT + firewall |
    |   dnsmasq         -> DHCP + DNS     |
    |   OpenMediaVault  -> SMB shares     |
    |        (data on NVMe SSD)           |
    +------------+------------------------+
          eth1  |  TP-Link UE306, static 192.168.10.1
                |  (LAN)
          +-----+-----+
          | Switch /  |
          | Wi-Fi AP  |
          +-----+-----+
                |
    +-----------+-----------+
    Laptop      Desktop      Phone
    (DHCP from dnsmasq, 192.168.10.x)


## Interface map
| Interface | Hardware | Role | Address |
|---|---|---|---|
| `eth0` | Built-in RJ45 | WAN | DHCP from upstream modem |
| `eth1` | TP-Link UE306 (USB 3.0) | LAN | Static `192.168.10.1/24` |

> LAN subnet `192.168.10.0/24` is chosen to avoid colliding with the typical
> `192.168.0.x` / `192.168.1.x` of an existing home router. Final subnet and
> DHCP pool are owned by the router-config notes — confirm before locking in.

## Storage / boot architecture
- The NVMe holds **both** the OS (root partition) and a **separate data
  partition** that OpenMediaVault claims for shares.
- The Pi boots from the **NVMe**, with the micro-SD card left in place as a
  recovery fallback (boot order: NVMe first, SD second).
- PCIe link is pinned to **Gen 2.0** (see decisions below).

## Build order (NAS-first)
The NAS is built and verified before the router layer is added, so the
existing home router keeps the household online during the routing work, and a
usable file server exists early. During the NAS-first phase, `eth1` temporarily
takes a DHCP lease from the existing router; it switches to the static
`192.168.10.1` when the router layer goes live. Devices that mapped a share by
IP will need a one-time update at that switch-over.

## Key design decisions
1. **NAS-first build order** — faster usable result; no downtime risk to the
   household network during the trickier routing work.
2. **OpenMediaVault fenced off from the WAN** — OMV binds its web UI to all
   interfaces by default and has no native per-NIC bind toggle, so nftables
   must explicitly block OMV's management and share ports on `eth0`. This is a
   dedicated build step, not something that happens automatically.
3. **PCIe Gen 2.0, not Gen 3.0** — the Pi 5's PCIe lane is officially rated for
   Gen 2; Gen 3 is unofficial and can cause intermittent I/O errors with some
   NVMe drives. For a NAS, stability beats peak throughput.
4. **Single NVMe for OS + data** — simpler than a separate boot device; the
   micro-SD remains as a recovery boot option.
5. **Manual nftables + dnsmasq instead of OpenWrt** — OpenWrt does not host
   OpenMediaVault / Samba comfortably, and the combined router + NAS role is
   the whole point of the project.

## Status
🚧 In progress. See `README.md` for the step-by-step progress checklist.
