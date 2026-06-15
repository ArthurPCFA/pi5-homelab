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
