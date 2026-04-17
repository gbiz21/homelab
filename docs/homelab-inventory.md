# 🏠 Homelab Infrastructure Inventory
> **Last Updated:** 2026-04-16  
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

| System | Type | Purpose | Hostname&nbsp;/&nbsp;IP | Status |
|---|---|---|---|---|
| Modem&nbsp;–&nbsp;Spectrum | Modem | 1&nbsp;Gbps broadband uplink | [redacted]/22 | ✅ |
| OPNsense&nbsp;Firewall | Firewall | Core gateway – routing + VLANs | 10.10.10.1 | ✅ |
| Unifi&nbsp;Switch | Managed&nbsp;Switch | VLAN distribution | 192.168.1.51 | ✅ |
| Unifi&nbsp;AP | Access&nbsp;Point | Wireless access | 192.168.1.53 | ✅ |
| Unifi&nbsp;Controller | Network&nbsp;Controller | Switch & AP management | 192.168.1.50 | ✅ |

> ⚠️ Unifi Switch mgmt IP migration to 10.10.10.x range in progress.  
> 🔒 OPNsense config backup stored locally in `05_Configs/` — never pushed to GitHub.

---

## <a id="compute"></a> 💻 Compute

### Physical Nodes

| System | Type | CPU | Purpose | IP | Status |
|---|---|---|---|---|---|
| prox-main | Proxmox&nbsp;Node | AMD Ryzen AI&nbsp;9 HX&nbsp;370 – 24t | Primary virtualisation host | 10.10.10.2 | ✅ |
| prox-node | Proxmox&nbsp;Node | Intel i9-13900HK – 20t | Secondary host | 10.10.10.3 | ✅ |
| prox-alien | Proxmox&nbsp;Node | Intel i9-12900H – 20t + RTX&nbsp;3080 | GPU passthrough workloads | 10.10.10.4 | ✅ |
| Raspberry&nbsp;Pi&nbsp;5 | Physical | RPi&nbsp;5 | Unifi Controller + Twingate | 192.168.1.50 | ✅ |

### Virtual Machines

| VM ID | Name | OS | CPU | RAM | Storage | IP | VLAN | Purpose | Status |
|---|---|---|---|---|---|---|---|---|---|
| 100 | Ubuntu-Media | Linux | 4 | 6&nbsp;GB | 300&nbsp;GB | 10.10.10.69 | Mgmt&nbsp;(10) | Video transcription | ✅ |
| 102 | Amazon-PC | Windows | 4 | 12&nbsp;GB | 200&nbsp;GB | 10.10.20.93 | Home&nbsp;(20) | Amazon shopping | ✅ |
| 104 | PBS | Linux | 4 | 6&nbsp;GB | 64&nbsp;GB | 10.10.10.50 | Mgmt&nbsp;(10) | Proxmox Backup Server | ✅ |
| 105 | Traefik-Proxy | Linux | 2 | 2&nbsp;GB | 32&nbsp;GB | 10.10.10.6 | Mgmt&nbsp;(10) | Reverse proxy | ✅ |
| 106 | Media&nbsp;WinPC | Windows | 6 | 12&nbsp;GB | 200&nbsp;GB | 10.10.20.250 | Home&nbsp;(20) | Power BI + Windows tools | ✅ |
| 109 | Win-Son-GPU | Windows | 8 | 16&nbsp;GB | 250&nbsp;GB | 10.10.60.51 | Dev&nbsp;(60) | GPU workstation (JJ) | ✅ |
| TBD | mgmt-server | Linux | 2 | 4&nbsp;GB | 60&nbsp;GB | 10.10.10.71 | Mgmt&nbsp;(10) | labctl + lab-platform | ✅ |

---

## <a id="storage"></a> 🗄️ Storage

| System | Type | CPU | RAM | Purpose | IP | Status |
|---|---|---|---|---|---|---|
| HOME-NAS (Synology&nbsp;DS923+) | NAS | AMD R1600 2.6&nbsp;GHz | 32&nbsp;GB | Primary storage – media, ISOs, backups | 10.10.10.13 | ✅ |

> 📦 NAS serves as primary backup destination for Proxmox Backup Server.

---

## <a id="platform--services"></a> ⚙️ Platform & Services

| Service | Type | Host | IP | Purpose | Status |
|---|---|---|---|---|---|
| Traefik&nbsp;v3 | Reverse&nbsp;Proxy | VM&nbsp;105 | 10.10.10.6 | HTTPS ingress + *.lab.bkre8tive.com | ✅ |
| Jellyfin | Media&nbsp;Server | VM (VLAN&nbsp;20) | 10.10.20.2 | Self-hosted media streaming | ✅ |
| Twingate | Zero-Trust&nbsp;VPN | RPi&nbsp;5 (Docker) | N/A | Remote access to homelab | ✅ |
| Docker | Container&nbsp;Runtime | TBD | N/A | Future container hosting | ⚠️ |

---

## <a id="cloud"></a> ☁️ Cloud

| Service | Provider | Purpose | Environment | Status |
|---|---|---|---|---|
| Azure&nbsp;Portal | Microsoft | AZ-104 study + hands-on labs | Lab | ✅ |
| Intune | Microsoft | MDM study + device mgmt | Lab | ✅ |
| GCP | Google | Self-hosted RustDesk relay | Prod | ✅ |

> ⚠️ Azure P2 licence expires June 2026 – transitioning to pay-as-you-go.

---

## <a id="automation"></a> 🤖 Automation

| Name | Type | Language | Purpose | Location | Status |
|---|---|---|---|---|---|
| labctl | CLI&nbsp;Script | Python | Infra automation | mgmt-server: `~/lab/repos/lab-platform/src/labctl.py` | ✅ |
| services.yaml | Config | YAML | Service definitions | mgmt-server: `~/lab/repos/lab-platform/` | ✅ |

---

## <a id="identity"></a> 🔑 Identity

| Tool | Type | Purpose | Status |
|---|---|---|---|
| Bitwarden | Password&nbsp;Manager | Source of truth for credentials, SSH keys, secrets | ✅ |

> 🔒 All credentials, SSH keys, and secrets stored in Bitwarden only.  
> Never stored in GitHub, SharePoint, or plain text files.

---

## <a id="vlan-map"></a> 🗺️ VLAN Map

| VLAN | Name | Subnet | Gateway | Purpose | Inter-VLAN Access |
|---|---|---|---|---|---|
| 10 | MgmtInfrastructure | 10.10.10.0/24 | 10.10.10.1 | Core infra – Proxmox, OPNsense, NAS, PBS | Blocked from Home; DNS allowed |
| 20 | HomeNetwork | 10.10.20.0/24 | 10.10.20.1 | Personal devices, Jellyfin, media VMs | Blocked from Mgmt, Recovery, Dev |
| 30 | ServerNetwork | 10.10.30.0/24 | 10.10.30.1 | Server workloads | TBD |
| 40 | GuestNetwork | 10.10.40.0/24 | 10.10.40.1 | Guest WiFi – internet only | Isolated |
| 50 | IOTNetwork | 10.10.50.0/24 | 10.10.50.1 | IoT devices | Isolated |
| 60 | DevelopmentNetwork | 10.10.60.0/24 | 10.10.60.1 | Dev workstations | Isolated from production |
| — | RecoveryMgmt | 192.168.50.0/24 | 192.168.50.1 | Out-of-band recovery access | Emergency only |

---

## <a id="docker-containers"></a> 🐳 Docker Containers

| Container&nbsp;ID | Image | Name | Host | Ports | Purpose | Status |
|---|---|---|---|---|---|---|
| 6cc71ba7da9d | traefik:v3.0 | traefik | VM&nbsp;105 | 80,&nbsp;443 | Reverse proxy | ✅ |
| 4d174236bc66 | twingate/connector:latest | twingate-connector | RPi&nbsp;5 | N/A | Zero-trust remote access | ✅ |
| 4f1e5fe05d74 | linuxserver/unifi-network-application | unifi-controller | RPi&nbsp;5 | 6789, 8080, 8443, 3478/udp | Unifi controller | ✅ |
| facb593d965c | mongo | unifi-mongo | RPi&nbsp;5 | 27017/tcp | MongoDB for Unifi | ✅ |

---

## 📝 Open Items

- [x] ~~Confirm and update VM 106 Win-PC11 IP address~~ → Media WinPC @ 10.10.20.250
- [x] ~~Confirm labctl script host location~~ → mgmt-server @ 10.10.10.71
- [ ] Migrate Unifi Switch mgmt IP to 10.10.10.x range
- [ ] Set up Docker on dedicated VM
- [ ] Move Unifi Controller from 192.168.1.x to mgmt VLAN
- [ ] Store OPNsense config backup in OneDrive as offsite copy

---

*Maintained in: `01_Homelab/00_Docs/homelab-inventory.md`*  
*Sensitive data (IPs, configs, certs) stored locally only — never pushed to GitHub.*
