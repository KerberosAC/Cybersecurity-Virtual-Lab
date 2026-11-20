# Cybersecurity Virtual Lab

**A multi-VM security lab built with VirtualBox and Vagrant: Kali Linux, deliberately vulnerable targets, and an Ubuntu Server, used for network testing and defensive practice.**

[![VirtualBox](https://img.shields.io/badge/Oracle_VirtualBox-183A61?logo=virtualbox&logoColor=white)](https://www.virtualbox.org/)
[![Vagrant](https://img.shields.io/badge/Vagrant-1868F2?logo=vagrant&logoColor=white)](https://developer.hashicorp.com/vagrant)
[![Kali Linux](https://img.shields.io/badge/Kali_Linux-557C94?logo=kalilinux&logoColor=white)](https://www.kali.org/)
[![Ubuntu Server](https://img.shields.io/badge/Ubuntu_Server-E95420?logo=ubuntu&logoColor=white)](https://ubuntu.com/server)
[![Nmap](https://img.shields.io/badge/Nmap-4682B4)](https://nmap.org/)

> **Type:** Personal home lab (simulated, authorised environment) · **Author:** Alexander Cox

---

## Overview

This is the foundation of my security home lab. I built a set of virtual machines on a Windows host, configured isolated VirtualBox networks, and used Kali Linux to test connectivity and discover services on the lab targets. The same environment later hosted my firewall, IDS, SIEM, vulnerability-scanning and patch-management projects (linked [below](#where-the-lab-went-next)).

**What I practised**

- Creating and managing VMs, and choosing between **internal** and **bridged** VirtualBox networking
- Setting up a Kali Linux testing workstation
- Deploying **Metasploitable 2** and **Metasploitable 3** (provisioned with **Vagrant**) as practice targets
- Installing Ubuntu Server for later IDS work
- Verifying reachability with `ping` and mapping the network with **Nmap**

## Lab components

| VM / tool | Role in the lab |
|---|---|
| Windows host PC | Runs VirtualBox and Vagrant |
| Kali Linux | Testing workstation: ping, Nmap, network troubleshooting |
| Metasploitable 2 | Deliberately vulnerable Linux target |
| Metasploitable 3 | Vulnerable target provisioned with Vagrant |
| Ubuntu Server (`SnortVM`) | Linux server, later used for Snort IDS |

```mermaid
flowchart TB
  H["Windows host PC"] --> VB["Oracle VirtualBox + Vagrant"]
  VB --> K["Kali Linux<br/>testing workstation"]
  VB --> M2["Metasploitable 2<br/>vulnerable target"]
  VB --> M3["Metasploitable 3<br/>vulnerable target (Vagrant)"]
  VB --> U["Ubuntu Server (SnortVM)<br/>Linux server / IDS"]
```

> The screenshots come from several lab sessions, so they show different adapter settings (for example `VulnLabs` and `Vulnlab1` internal networks, and a bridged test). The diagram shows what was built, not a single always-on network.

---

## Build walkthrough

### 1. Prepare the host and VirtualBox networking

I checked the Windows host, opened VirtualBox, and attached Kali to an **internal network** (`VulnLabs`) so the lab stays isolated from my home network.

<table>
<tr>
<td width="50%"><img src="screenshots/01-windows-host-network-discovery.png" alt="Windows host command-line checks of the VirtualBox networking environment"><br><sub>Host-side networking checks</sub></td>
<td width="50%"><img src="screenshots/02-kali-internal-network-vulnlabs.png" alt="Kali Linux adapter set to the internal network VulnLabs"><br><sub>Kali adapter on the <code>VulnLabs</code> internal network</sub></td>
</tr>
</table>

### 2. Set up Kali Linux

Kali is the main workstation for scanning and diagnostics. I confirmed its interface and addressing before testing any targets.

<table>
<tr>
<td width="50%"><img src="screenshots/04-kali-linux-running.png" alt="Kali Linux virtual machine running"><br><sub>Kali VM running</sub></td>
<td width="50%"><img src="screenshots/05-kali-network-interface-configuration.png" alt="Kali network interface configuration output"><br><sub>Interface configuration</sub></td>
</tr>
</table>

### 3. Deploy Metasploitable 2

Metasploitable 2 sits on its own internal network (`Vulnlab1`) and is only used inside the lab.

<table>
<tr>
<td width="50%"><img src="screenshots/06-metasploitable2-internal-network-vulnlab1.png" alt="Metasploitable 2 adapter on the Vulnlab1 internal network"><br><sub>Adapter on <code>Vulnlab1</code></sub></td>
<td width="50%"><img src="screenshots/07-metasploitable2-network-console.png" alt="Metasploitable 2 console showing network interface information"><br><sub>Console with interface details</sub></td>
</tr>
</table>

### 4. Verify connectivity and discover hosts

From Kali I pinged lab hosts, then ran an Nmap ping sweep and a service scan.

```bash
ping 172.30.1.21
nmap -sn 172.30.1.1/24     # host discovery
```

<table>
<tr>
<td width="50%"><img src="screenshots/09-kali-ping-and-nmap-host-discovery.png" alt="Kali ping and Nmap host discovery on 172.30.1.0/24"><br><sub>Ping and Nmap host discovery</sub></td>
<td width="50%"><img src="screenshots/10-kali-nmap-service-inventory.png" alt="Nmap output showing open ports and services on a lab host"><br><sub>Open ports and services on a lab host</sub></td>
</tr>
</table>

**Result:** Nmap reported **three hosts up** on `172.30.1.0/24`, and a service scan listed the ports and services exposed by a lab host.

### 5. Provision Metasploitable 3 with Vagrant

I installed Vagrant on the Windows host and used the VirtualBox provider to build and start Metasploitable 3 from the command line, instead of installing it by hand.

<table>
<tr>
<td width="50%"><img src="screenshots/12-vagrant-plugin-setup.png" alt="Windows command prompt showing Vagrant plugin installation"><br><sub>Vagrant plugin setup</sub></td>
<td width="50%"><img src="screenshots/14-metasploitable3-vagrant-deployment.png" alt="Vagrant provisioning and startup output for Metasploitable 3"><br><sub>Vagrant provisioning output</sub></td>
</tr>
<tr>
<td width="50%"><img src="screenshots/15-metasploitable3-virtualbox-vm.png" alt="Metasploitable 3 virtual machine in VirtualBox"><br><sub>VM registered in VirtualBox</sub></td>
<td width="50%"><img src="screenshots/16-metasploitable3-console.png" alt="Running Metasploitable 3 console"><br><sub>Running Metasploitable 3 console</sub></td>
</tr>
</table>

### 6. Install Ubuntu Server (`SnortVM`)

I installed Ubuntu Server (24.04 LTS shown in the final console) as a VM named `SnortVM (IDS)`, then confirmed from Kali that it replied to ping at `172.30.1.26`.

<table>
<tr>
<td width="33%"><img src="screenshots/18-ubuntu-server-installation-snortvm.png" alt="Ubuntu Server installation in the SnortVM VM"><br><sub>Installer</sub></td>
<td width="33%"><img src="screenshots/20-ubuntu-server-post-install-console.png" alt="Ubuntu Server console after setup"><br><sub>Console after setup</sub></td>
<td width="33%"><img src="screenshots/21-kali-ubuntu-reachability-validation.png" alt="Successful ping replies from 172.30.1.26"><br><sub>Ping replies from Kali</sub></td>
</tr>
</table>

---

## Key results

| What I did | Evidence |
|---|---|
| Built a Kali + vulnerable-target lab in VirtualBox | Steps 1–3 |
| Used internal networks to keep the lab isolated | `VulnLabs` and `Vulnlab1` adapters |
| Found live hosts with Nmap | Three hosts up on `172.30.1.0/24` |
| Confirmed reachability between VMs | Ping replies from `172.30.1.21` and `172.30.1.26` |
| Provisioned a target with Vagrant | Metasploitable 3 built from the command line |
| Prepared a server for IDS work | Ubuntu Server `SnortVM` |

## Lessons learned

- **Network mode decides what can talk to what.** Internal, bridged and host-only adapters change both reachability and isolation, so I tested connectivity before every later exercise.
- **Failed tests are useful evidence.** My ping and bridged-adapter troubleshooting screenshots are in the [Evidence Gallery](EVIDENCE-GALLERY.md).
- **Automate provisioning.** Vagrant made the Metasploitable 3 build repeatable.
- **Keep vulnerable VMs isolated.** They should never be exposed to a home or production network.

## Where the lab went next

This environment is the base for my other projects. Each has its own repository with a full write-up:

- Snort Intrusion Detection
- pfSense Firewall Lab
- Windows Server and Active Directory
- Nessus Vulnerability Assessment
- Wazuh Security Monitoring
- Endpoint Patch Management (Action1)

See all of them on my [GitHub profile](https://github.com/KerberosAC).

## Scope and safety

This is a personal, simulated lab. The Metasploitable VMs are intentionally insecure and were used only on isolated virtual networks. Screenshots come from different sessions, so IP addresses and adapter names are historical lab data rather than one fixed topology. The repository contains no credentials, exploit payloads or production data.

**Author:** [Alexander Cox](https://www.linkedin.com/in/alexandercox25/) · [GitHub](https://github.com/KerberosAC)
