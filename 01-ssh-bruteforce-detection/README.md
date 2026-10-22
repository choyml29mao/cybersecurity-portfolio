# Project 1: SSH Brute-Force Detection in Splunk

## Objective
Simulate an SSH brute-force attack against a vulnerable target and detect the attack using Splunk's log analysis capabilities.

## Environment

| Component | Details |
|-----------|---------|
| Hypervisor | VirtualBox 7.x |
| Attacker | Kali Linux — `192.168.56.101` |
| Victim | Metasploitable2 — `192.168.56.102` |
| SIEM | Splunk Enterprise (installed on Kali) |
| Network | Isolated Host-Only Adapter (`vboxnet0`, 192.168.56.1/24) |

## Methodology

### 1. Lab Setup & Isolation Verification
Built an isolated Host-Only network in VirtualBox with Kali Linux (attacker) and Metasploitable2 (victim). Verified network isolation by confirming Kali could not reach the internet.

![Network Isolation Check]<img width="713" height="124" alt="Screenshot 2026-10-01 211734" src="https://github.com/user-attachments/assets/b6085fff-7993-47c4-b822-6ba06c8b3bed" />


### 2. Attack Simulation
Created a brute-force script from Kali to attempt multiple passwords against `msfadmin@192.168.56.102`. 

*(Note: Because Metasploitable2 uses an ancient version of OpenSSH, modern tools like Hydra failed. I had to use `sshpass` with legacy algorithm flags to force the connection).*

### 3. Log Ingestion
Transferred `/var/log/auth.log` from Metasploitable2 to Kali. Uploaded to Splunk via **Settings → Data Inputs → Files & Directories**. Splunk successfully parsed the logs using the `linux_secure` source type.

![Splunk Ingestion] <img width="1919" height="1036" alt="Screenshot 2026-10-02 201314" src="https://github.com/user-attachments/assets/0ab9d6f1-24f0-4742-b1f7-6c86fb6ee872" />


### 4. Detection Query
Ran the following SPL search in Splunk to identify the brute-force pattern:

```splunk
index=main source="/home/city6292/auth.log" "Failed password"
| rex "Failed password for (?<user>\S+) from (?<src_ip>\S+)"
| stats count by src_ip, user
| where count > 3
