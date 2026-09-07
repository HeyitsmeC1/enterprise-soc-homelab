# enterprise-soc-homelab
<img width="710" height="1062" alt="Untitled Diagram drawio" src="https://github.com/user-attachments/assets/7b396505-a1ec-4de6-a281-9847b8259e7b" />

Deployed on a bare-metal Proxmox VE hypervisor to simulate centralized security telemetry, and detect identity-based attacks. This virtual environment implements an OPNsense virtual firewall to segment the internal subnet operations(10.0.0.0/24) from the upstream network(home network), routes Active Directory authentication across Windows Server 2025(corp.local) and a Windows 10 endpoint, and consolidates security events/information into a centralized WAZUH SIEM. The lab was confirmed by executing brute-force to verify real-time alerts.

---
## Network Architecture & Components
| Component | Role | IP Address / Subnet | Hostname / OS |
| :--- | :--- | :--- | :--- |
| **Hypervisor** | Bare-Metal Host | `192.168.68.200` | `pve-lab` (Proxmox VE 9.1.6) |
| **Firewall / Router** | Network Gateway & NAT | `192.168.68.54` (WAN) / `10.0.0.1` (LAN) | OPNsense (`vtnet0` / `vtnet1`) |
| **Domain Controller** | Identity & DNS (`corp.local`) | `10.0.0.10` | `WIN-B7BD9UJ1IRJ` (Server 2025) |
| **Workstation** | Domain Endpoint Client | `10.0.0.11` | `DESKTOP-V1NCOBV` (Windows 10) |
| **SIEM / Telemetry** | Log Collection & Ingestion | `10.0.0.30` | WAZUH All-in-One (Ubuntu 22.04.5) |
---
## Infrastructure Verification
All four nodes running simultaneously in the Proxmox virtual environment:
<img width="2557" height="1317" alt="ProxmoxDashboard" src="https://github.com/user-attachments/assets/f0cdc0b2-4e04-4847-a83b-b925fa438fb2" />
---
## Threat Simulation: Brute-Force Authentication (Event ID 4625)
brute-force logon attack against domain endpoint `DESKTOP-V1NCOBV`. WAZUH successfully captured authentication failures and triggered rule `60122` (*Logon Failure - Unknown user or bad password*):
<img width="1833" height="1313" alt="BruteForceTestlogs" src="https://github.com/user-attachments/assets/4d501ebf-ea69-4c22-97c0-2c8d7c707582" />
### Telemetry & Event Details (JSON)
Expanding the event shows Windows Event ID `4625`, workstation identity, and timestamp:
<img width="1585" height="888" alt="LogDetailJSON" src="https://github.com/user-attachments/assets/49eca087-fce8-43db-9b5c-7e036b5280d0" />
<img width="1590" height="839" alt="LogDetailJSON1" src="https://github.com/user-attachments/assets/8ee9fc7a-3135-45a1-bccc-aec9a1365b93" />
<img width="1599" height="300" alt="LogDetailJSON2" src="https://github.com/user-attachments/assets/49bc1b3a-25e4-4b56-9ee9-3b6dcbbe6c1b" />

