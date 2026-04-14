# bkre8tive Homelab

Platform engineering homelab — 3-node Proxmox cluster, OPNsense firewall,
6 VLANs, Docker, Traefik v3, and AI agent integration.

## 🌐 Network Topology
[View animated network diagram →](https://gbeazer.github.io/homelab/04_Diagrams/network-topology.html)

## 🏗️ Infrastructure
- 3-node Proxmox cluster (prox-main · prox-node · prox-alien)
- OPNsense firewall with 6 VLANs (Mgmt · Home · Server · Guest · IoT · Dev)
- Synology DS923+ NAS · Proxmox Backup Server
- Traefik v3 reverse proxy with TLS
- Twingate zero-trust remote access
- Jellyfin media server

## ⚙️ Stack
Proxmox · OPNsense · Docker · Traefik · Synology NAS ·
Azure · Twingate · Bitwarden · Cloudflare

## 📄 Docs
- [Inventory](00_Docs/homelab-inventory.md)
