# 🏠 Homelab Infrastructure Inventory
> **Last Updated:** 2026-04-13  
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

| System | Type | Purpose | Hostname / IP | Status |
|---|---|---|---|---|
| Modem – Spectrum | Modem | 1 Gbps broadband uplink | [redacted]/22 | ✅ Active |
| OPNsense Firewall | Firewall | Core gateway – routing + VLAN segmentation | 10.10.10.1 | ✅ Active |
| Unifi Switch | Managed Switch | VLAN distribution | 192.168.1.51 | ✅ Active |
| Unifi AP | Access Point | Wireless access | 192.168.1.53 | ✅ Active |
| Unifi Controller | Network Controller | Switch & AP management | 192.168.1.50 | ✅ Active |

> ⚠️ Unifi Switch mgmt IP migration to 10.10.10.x range in progress.  
> 🔒 OPNsense config backup stored locally in `05_Configs/` — never pushed to GitHub.

---

## <a id="compute"></a> 💻 Compute

### Physical Nodes

| System | Type | CPU | Purpose | IP | Status |
|---|---|---|---|---|---|
| prox-main | Proxmox Node | AMD Ryzen AI 9 HX 370 – 24t | Primary virtualisation host | 10.10.10.2 | ✅ Active |
| prox-node | Proxmox Node | Intel i9-13900HK – 20t | Secondary host – majority of VMs | 10.10.10.3 | ✅ Active |
| prox-alien | Proxmox Node | Intel i9-12900H – 20t + RTX 3080 | GPU passthrough workloads | 10.10.10.4 | ✅ Active |
| Raspberry Pi 5 | Physical | RPi 5 | Unifi Controller + Twingate connector | 192.168.1.50 | ✅ Active |

### Virtual Machines

| VM ID | Name | OS | CPU | RAM | Storage | IP | VLAN | Purpose | Status |
|---|---|---|---|---|---|---|---|---|---|
| 100 | Ubuntu-Media | Linux | 4 | 6 GB | 300 GB | 10.10.10.69 | Mgmt (10) | Video transcription | ✅ Active |
| 102 | Amazon-PC | Windows | 4 | 12 GB | 200 GB | 10.10.20.250 | Home (20) | Amazon shopping + NordVPN | ✅ Active |
| 104 | PBS | Linux | 4 | 6 GB | 64 GB | 10.10.10.50 | Mgmt (10) | Proxmox Backup Server | ✅ Active |
| 105 | Traefik-Proxy | Linux | 2 | 2 GB | 32 GB | 10.10.10.6 | Mgmt (10) | Reverse proxy – internal DNS routing | ✅ Active |
| 106 | Win-PC11 | Windows | TBD | TBD | TBD | 10.10.20.x | Home (20) | Windows 11 – Power BI + Windows tools | ✅ Active |
| — | WIN-SON-PC | Windows | 4 | 12 GB | 100 GB | 10.10.60.50 | Dev (60) | Dev workstation for JJ | ✅ Active |

> ⚠️ VM 106 Win-PC11 IP needs to be confirmed and updated.

---

## <a id="storage"></a> 🗄️ Storage

| System | Type | CPU | RAM | Purpose | IP | Status |
|---|---|---|---|---|---|---|
| HOME-NAS (Synology DS923+) | NAS | AMD R1600 2.6 GHz | 32 GB | Primary storage – media, ISOs, VM backups | 10.10.10.13 | ✅ Active |

> 📦 NAS serves as primary backup destination for Proxmox Backup Server.

---

## <a id="platform--services"></a> ⚙️ Platform & Services

| Service | Type | Host | IP | Purpose | Status |
|---|---|---|---|---|---|
| Traefik v3 | Reverse Proxy | VM 105 | 10.10.10.6 | HTTPS ingress + *.lab.bkre8tive.com routing | ✅ Active |
| Jellyfin | Media Server | VM (VLAN 20) | 10.10.20.2 | Self-hosted media streaming | ✅ Active |
| Twingate | Zero-Trust VPN | RPi 5 (Docker) | N/A | Remote access to homelab | ✅ Active |
| Docker | Container Runtime | TBD | N/A | Future container hosting | ⚠️ In Progress |

---

## <a id="cloud"></a> ☁️ Cloud

| Service | Provider | Purpose | Environment | Status |
|---|---|---|---|---|
| Azure Portal | Microsoft | AZ-104 study + hands-on labs | Lab | ✅ Active |
| Intune | Microsoft | MDM study + device management practice | Lab | ✅ Active |
| GCP | Google | Hosts self-hosted RustDesk relay server | Prod | ✅ Active |

> ⚠️ Azure P2 licence expires June 2026 – transitioning to pay-as-you-go.

---

## <a id="automation"></a> 🤖 Automation

| Name | Type | Language | Purpose | Location | Status |
|---|---|---|---|---|---|
| labctl | CLI Script | Python | Infrastructure automation | TBD – confirm host VM | ✅ Active |
| services.yaml | Config | YAML | Service definitions | TBD – confirm location | ✅ Active |

> ⚠️ labctl host location needs to be confirmed and documented.

---

## <a id="identity"></a> 🔑 Identity

| Tool | Type | Purpose | Status |
|---|---|---|---|
| Bitwarden | Password Manager | Source of truth for all credentials, SSH keys, secrets | ✅ Active |

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

| Container ID | Image | Name | Host | Ports | Purpose | Status |
|---|---|---|---|---|---|---|
| 6cc71ba7da9d | traefik:v3.0 | traefik | VM 105 | 80, 443 | Reverse proxy – *.lab.bkre8tive.com | ✅ Active |
| 4d174236bc66 | twingate/connector:latest | twingate-connector | RPi 5 | N/A | Zero-trust remote access connector | ✅ Active |
| 4f1e5fe05d74 | linuxserver/unifi-network-application | unifi-controller | RPi 5 | 6789, 8080, 8443, 3478/udp | Unifi network controller | ✅ Active |
| facb593d965c | mongo | unifi-mongo | RPi 5 | 27017/tcp | MongoDB – required by Unifi controller | ✅ Active |

---

## <a id="open-items"></a> 📝 Open Items

- [ ] Confirm and update VM 106 Win-PC11 IP address
- [ ] Confirm labctl script host location
- [ ] Migrate Unifi Switch mgmt IP to 10.10.10.x range
- [ ] Set up Docker on dedicated VM
- [ ] Move Unifi Controller from 192.168.1.x to mgmt VLAN
- [ ] Store OPNsense config backup in OneDrive as offsite copy

---

*Maintained in: `01_Homelab/00_Docs/homelab-inventory.md`*  
*Sensitive data (IPs, configs, certs) stored locally only — never pushed to GitHub.*
