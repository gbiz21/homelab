# 🏠 Homelab Infrastructure Inventory
> **Last Updated:** 2026-04-17  
> **Status Key:** ✅ Active | ⚠️ In Progress | ❌ Inactive

---

## 📋 Table of Contents
- [Network](#network)
- [Compute](#compute)
- [Storage](#storage)
- [Platform & Services](#platform--services)
- [Cloud](#cloud)
- [Automation](#automation)
- [Identity](#identity)
- [VLAN Map](#vlan-map)
- [Docker Containers](#docker-containers)

---

## <a id="network"></a> 🌐 Network

### Core Infrastructure

| System | Type | Purpose | Status |
|---|---|---|---|
| Cable&nbsp;Modem | Modem | 1&nbsp;Gbps broadband uplink | ✅ |
| OPNsense&nbsp;Firewall | Firewall | Core gateway – routing + VLANs | ✅ |

### Switching & Wireless

| System | Type | Purpose | Status |
|---|---|---|---|
| USW-16-PoE | Managed&nbsp;Switch | VLAN distribution | ✅ |
| U6&nbsp;Pro | Access&nbsp;Point | WiFi (Home, Guest, IoT SSIDs) | ✅ |
| RAXE500 | Access&nbsp;Point | WiFi (Home network) | ✅ |

### Network Controller

| System | Type | Host | Status |
|---|---|---|---|
| Unifi&nbsp;Controller | Docker | RPi&nbsp;5 | ✅ |
| Twingate | Docker | RPi&nbsp;5 | ✅ |

> 🔒 OPNsense config backups stored locally — never pushed to GitHub.

---

## <a id="compute"></a> 💻 Compute

### Physical Nodes

| System | Type | CPU | Purpose | Status |
|---|---|---|---|---|
| prox-main | Proxmox&nbsp;Node | AMD Ryzen AI&nbsp;9 HX&nbsp;370 – 24t | Primary virtualisation host | ✅ |
| prox-node | Proxmox&nbsp;Node | Intel i9-13900HK – 20t | Secondary host – majority of VMs | ✅ |
| prox-alien | Proxmox&nbsp;Node | Intel i9-12900H – 20t + RTX&nbsp;3080 | GPU passthrough workloads | ✅ |
| Raspberry&nbsp;Pi&nbsp;5 | RPi&nbsp;5 | ARM Cortex-A76 | Unifi Controller + Twingate | ✅ |

### Virtual Machines

| VM&nbsp;ID | Name | OS | Host | CPU | RAM | Storage | VLAN | Purpose | Status |
|---|---|---|---|---|---|---|---|---|---|
| 104 | PBS | Linux | prox-node | 4 | 6&nbsp;GB | 64&nbsp;GB | Mgmt&nbsp;(10) | Proxmox Backup Server | ✅ |
| 105 | Traefik-Proxy | Linux | prox-node | 2 | 2&nbsp;GB | 32&nbsp;GB | Mgmt&nbsp;(10) | Reverse proxy | ✅ |
| 108 | mgmt-server | Linux | prox-node | 2 | 4&nbsp;GB | 60&nbsp;GB | Mgmt&nbsp;(10) | Infra automation platform | ✅ |
| 101 | pgsrv01 | Linux | prox-main | TBD | TBD | TBD | Mgmt&nbsp;(10) | Local database server | ✅ |
| 102 | Amazon-PC | Windows | prox-node | 4 | 12&nbsp;GB | 200&nbsp;GB | Home&nbsp;(20) | General purpose | ✅ |
| 106 | Media&nbsp;WinPC | Windows | prox-node | 6 | 12&nbsp;GB | 200&nbsp;GB | Home&nbsp;(20) | Power BI + Windows tools | ✅ |
| 100 | Ubuntu-Media | Linux | prox-node | 4 | 6&nbsp;GB | 300&nbsp;GB | Mgmt&nbsp;(10) | Video transcription | ✅ |
| 109 | Win-Son-GPU | Windows | prox-alien | 8 | 16&nbsp;GB | 250&nbsp;GB | Dev&nbsp;(60) | GPU workstation | ✅ |

---

## <a id="storage"></a> 🗄️ Storage

| System | Type | CPU | RAM | Purpose | Status |
|---|---|---|---|---|---|
| Synology&nbsp;DS923+ | NAS | AMD R1600 2.6&nbsp;GHz | 32&nbsp;GB | Primary storage – media, ISOs, backups | ✅ |

> 📦 NAS serves as primary backup destination for Proxmox Backup Server.

---

## <a id="platform--services"></a> ⚙️ Platform & Services

| Service | Type | Host | VLAN | Purpose | Status |
|---|---|---|---|---|---|
| Traefik&nbsp;v3 | Reverse&nbsp;Proxy | VM&nbsp;105 (prox-node) | Mgmt&nbsp;(10) | HTTPS ingress + internal DNS routing | ✅ |
| Jellyfin | Media&nbsp;Server | VM (prox-node) | Home&nbsp;(20) | Self-hosted media streaming | ✅ |
| Twingate | Zero-Trust&nbsp;VPN | RPi&nbsp;5 (Docker) | Mgmt&nbsp;(10) | Remote access to homelab | ✅ |
| Unifi&nbsp;Controller | Network&nbsp;Mgmt | RPi&nbsp;5 (Docker) | Mgmt&nbsp;(10) | Switch + AP management | ✅ |
| Docker | Container&nbsp;Runtime | TBD | TBD | Future container hosting | ⚠️ |

---

## <a id="cloud"></a> ☁️ Cloud

| Service | Provider | Purpose | Environment | Status |
|---|---|---|---|---|
| Azure&nbsp;Portal | Microsoft | AZ-104 study + hands-on labs | Lab | ✅ |
| Intune | Microsoft | MDM study + device mgmt | Lab | ✅ |
| GCP | Google | Self-hosted RustDesk relay | Prod | ✅ |

---

## <a id="automation"></a> 🤖 Automation

| Name | Type | Language | Purpose | Status |
|---|---|---|---|---|
| labctl | CLI&nbsp;Script | Python | Infrastructure automation | ✅ |
| services.yaml | Config | YAML | Service definitions | ✅ |

---

## <a id="identity"></a> 🔑 Identity

| Tool | Type | Purpose | Status |
|---|---|---|---|
| Bitwarden | Password&nbsp;Manager | Source of truth for credentials, SSH keys, secrets | ✅ |

> 🔒 All credentials, SSH keys, and secrets stored in Bitwarden only.  
> Never stored in GitHub, SharePoint, or plain text files.

---

## <a id="vlan-map"></a> 🗺️ VLAN Map

| VLAN | Name | Purpose | Inter-VLAN Access |
|---|---|---|---|
| 10 | MgmtInfrastructure | Core infra – Proxmox, OPNsense, NAS, PBS | Blocked from Home; DNS allowed |
| 20 | HomeNetwork | Personal devices, media VMs | Blocked from Mgmt, Recovery, Dev |
| 30 | ServerNetwork | Server workloads | TBD |
| 40 | GuestNetwork | Guest WiFi – internet only | Isolated |
| 50 | IOTNetwork | IoT devices | Isolated |
| 60 | DevelopmentNetwork | Dev workstations | Isolated from production |

---

## <a id="docker-containers"></a> 🐳 Docker Containers

### RPi 5

| Image | Name | Purpose | Status |
|---|---|---|---|
| linuxserver/unifi-network-application | unifi-controller | Unifi controller | ✅ |
| mongo | unifi-mongo | MongoDB for Unifi | ✅ |
| twingate/connector:latest | twingate-connector | Zero-trust remote access | ✅ |

### Traefik-Proxy VM

| Image | Name | Purpose | Status |
|---|---|---|---|
| traefik:v3.0 | traefik | Reverse proxy | ✅ |

---

## 📝 Open Items

### Completed

- [x] Confirm and update VM IPs
- [x] Confirm automation script locations
- [x] Migrate Unifi Switch to mgmt VLAN
- [x] Move Unifi Controller to mgmt VLAN
- [x] Move Unifi AP to mgmt VLAN
- [x] Fix Ubuntu-Media VLAN assignment
- [x] Document and confirm all VM IDs

### Remaining

- [ ] Store firewall config backup offsite
- [ ] Set up Docker on dedicated VM (VLAN 30)
- [ ] Move IoT devices to IoT VLAN (50) — in progress
- [ ] Migrate trunk native VLAN — then disable legacy interface

> 💡 Jellyfin staying on Home VLAN — TVs stream over WiFi on the same VLAN, avoids inter-VLAN routing latency.

---

*Sensitive data (IPs, configs, certs, domains) stored locally only — never pushed to GitHub.*
