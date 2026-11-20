# Cybersecurity Virtual Lab — Text Network Topology

## Network connections and IP addresses

The topology below uses **addresses shown in the original Cyber Home Lab document**, including VM console output, VirtualBox configuration screenshots, the pfSense console, and Wazuh endpoint inventory. It groups systems by documented lab **exercise** rather than claiming all interfaces were connected simultaneously.

**Key:** `----ICMP ping---->` means communication was actually demonstrated; other branches indicate a documented VM, interface or shared IP range, **not** proven connectivity or routing.

```text
WINDOWS HOST PC  [Oracle VirtualBox / Vagrant]
|
+-- A. TARGET & RECONNAISSANCE LAB -- 172.30.1.0/24
|   |
|   +-- VirtualBox DHCP setup for "VulnLabs"
|   |     Server IP: 172.30.1.1
|   |     Pool:      172.30.1.20 - 172.30.1.55
|   |
|   +-- Kali Linux (testing VM)
|   |     eth1: 172.30.1.20/24
|   |     eth0: 192.168.100.207/24 (additional LAN adapter)
|   |
|   +-- Metasploitable 2 (vulnerable Linux VM)
|   |     eth0: 172.30.1.21/24
|   |     VM adapter screenshot: internal network "Vulnlab1"
|   |
|   +-- Metasploitable 3 (vulnerable Windows VM)
|   |     IPv4: 172.30.1.22/24
|   |     VM adapter screenshot: internal network "Vulnlab1"
|   |     Provisioned with Vagrant + VirtualBox
|   |
|   +-- Other observations (VM identity not established):
|   |     172.30.1.18  -- seen as active in an Nmap discovery scan
|   |     172.30.1.26  -- responded to Kali ping in a later test
|   |
|   `-- VERIFIED COMMUNICATION:
|         Kali 172.30.1.20 ----ICMP ping----> 172.30.1.21
|         Kali scanned 172.30.1.0/24 (three hosts observed in one scan)
|
+-- B. pfSense FIREWALL EXERCISE -- 172.30.2.0/24
|   |
|   +-- pfSense VM
|   |     WAN em0: 192.168.100.245/24 (DHCP)
|   |     LAN em1: 172.30.2.1/24
|   |     Internal adapter name shown: "pfVulnlab1"
|   |
|   `-- Kali Linux (later Wazuh network inventory)
|         eth1: 172.30.2.100/24
|         Same subnet as pfSense LAN; direct traffic path not proven
|
+-- C. OBSERVED EXTERNAL / BRIDGED-SIDE LAN -- 192.168.100.0/24
|   |
|   +-- Windows11HostPC (Wazuh inventory): Wi-Fi 192.168.100.70
|   +-- Kali Linux:                     eth0  192.168.100.207
|   +-- Ubuntu Server / SnortVM:        enp0s3 192.168.100.216
|   +-- Wazuh manager (Ubuntu):         enp0s3 192.168.100.214
|   +-- pfSense WAN:                    em0   192.168.100.245
|   `-- Additional monitored laptop:          192.168.100.215
|       (These addresses share a CIDR; their direct paths were not mapped.)
|
+-- D. SEPARATE VIRTUALBOX HOST-ONLY DHCP CONFIGURATION
|   `-- 192.168.56.100 = DHCP server
|       Lease pool: 192.168.56.101 - 192.168.56.254 /24
|       (No specific VM-to-this-network attachment was verified.)
|
`-- E. OTHER DOCUMENTED SYSTEMS / MANAGEMENT TOOLS
    +-- Windows Server 2022 / DC01: Active Directory + DNS
    |     IP address: not shown in the evidence
    +-- Snort IDS: tested in its Ubuntu/pfSense lab contexts
    +-- Nessus: vulnerability-assessment application
    `-- Action1: endpoint management application
          (Nessus and Action1 are tools, not necessarily separate VMs.)
```

**Important network-name discrepancy:** The screenshots show Kali configured on VirtualBox's internal network `VulnLabs`, while Metasploitable 2 and 3 are shown using `Vulnlab1`. These are *different VirtualBox network names*, even though the machines' console screenshots use the `172.30.1.0/24` address range. The successful Kali-to-Metasploitable 2 ping confirms reachability **at the time of that test**; it does not establish that the adapter-setting screenshots represent one unchanged configuration.

**Separate lab stages:** The `172.30.2.100` Kali address comes from a **later Wazuh endpoint inventory** and is not the same snapshot as Kali's earlier `172.30.1.20` address. The pfSense LAN IP is documented as `172.30.2.1`, but no direct Kali-to-pfSense ping or routing trace was provided. Similarly, the `192.168.100.x` addresses document interfaces on the same address range, not a complete switching or routing map.

### Network address evidence

| Device or service | Address / range | Evidence from the lab |
|---|---|---|
| VirtualBox `VulnLabs` DHCP server | `172.30.1.1`, pool `172.30.1.20–55` | [Windows host VBoxManage screenshot](screenshots/01-windows-host-network-discovery.png) |
| VirtualBox host-only DHCP service | `192.168.56.100`, pool `192.168.56.101–254` | [Windows host VBoxManage screenshot](screenshots/01-windows-host-network-discovery.png) |
| Kali Linux | `172.30.1.20` (`eth1`); `192.168.100.207` (`eth0`) | [Kali interface screenshot](screenshots/05-kali-network-interface-configuration.png) |
| Metasploitable 2 | `172.30.1.21` | [Linux console](screenshots/07-metasploitable2-network-console.png) |
| Metasploitable 3 | `172.30.1.22` | [Windows console](screenshots/16-metasploitable3-console.png) |
| Ubuntu Server / SnortVM | `192.168.100.216` | [Ubuntu terminal](screenshots/20-ubuntu-server-post-install-console.png) |
| pfSense | WAN `192.168.100.245`; LAN `172.30.2.1` | [pfSense console](screenshots/26-pfsense-vm-console.png) |
| Wazuh manager | `192.168.100.214` | [Wazuh Ubuntu IP output](screenshots/50-wazuh-server-ip-address.png) |
| Kali Linux (later inventory) | `172.30.2.100` (`eth1`) | [Wazuh endpoint network inventory](screenshots/51-wazuh-endpoint-network-addresses.png) |
| `Windows11HostPC` (inventory) | `192.168.100.70` (Wi-Fi) | [Wazuh endpoint network inventory](screenshots/51-wazuh-endpoint-network-addresses.png) |
| Additional Wazuh-monitored laptop | `192.168.100.215` | [Wazuh endpoint network inventory](screenshots/51-wazuh-endpoint-network-addresses.png) |
| Windows Server 2022 / DC01 | **Not shown** (DHCP indicated) | [Windows Server Manager](screenshots/32-windows-server-manager.png) |

**Interpretation:** CIDR membership alone does **not** confirm VirtualBox internal-network membership, reachability or firewall forwarding. IPs and adapter modes were captured at different points in the lab; do not treat the drawing as a production-grade verified routing diagram.


