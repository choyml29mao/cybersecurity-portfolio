# SSH Brute-Force Detection in Splunk

## Objective
Simulate an SSH brute-force attack against a vulnerable target and detect the attack using Splunk's log analysis capabilities.

## Environment

| Component | Details |
|-----------|---------|
| Hypervisor | VirtualBox 7.x |
| Attacker | Kali Linux — `192.168.56.101` |
| Victim | Metasploitable2 — `192.168.56.102` |
| SIEM | Splunk Enterprise 10.6.0.5 (installed on Kali) |
| Network | Isolated Host-Only Adapter (`vboxnet0`, 192.168.56.1/24) |
| Isolation verified | `ping 8.8.8.8` from Kali fails (no internet route) |

## Tools Used
- `sshpass` — scripted SSH authentication attempts
- OpenSSH client with legacy algorithm flags
- Splunk Enterprise — log ingestion and detection
- Metasploitable2 `auth.log` — victim log source

## Methodology

### 1. Lab Setup
Built an isolated Host-Only network in VirtualBox with Kali Linux (attacker) and Metasploitable2 (victim). Verified network isolation by confirming Kali could not reach the internet (`ping 8.8.8.8` failed).

### 2. Attack Simulation
Created a password list (`~/passwords.txt`) with common weak passwords and ran a scripted brute-force loop from Kali:

```bash
for i in 1 2 3 4 5; do
  sshpass -p "wrongpassword" ssh -o StrictHostKeyChecking=no \
    -o KexAlgorithms=+diffie-hellman-group1-sha1 \
    -o HostKeyAlgorithms=+ssh-rsa \
    -o MACs=+hmac-md5 \
    -o PreferredAuthentications=password \
    -o PubkeyAuthentication=no \
    msfadmin@192.168.56.102 "echo test" 2>/dev/null
  echo "Attempt $i done"
done
Passwords attempted: password, 123456, admin, msfadmin, root


3. Log Ingestion
Transferred /var/log/auth.log from Metasploitable2 to Kali. Uploaded to Splunk via Settings → Data Inputs → Files & Directories → Add new → Index Once.

Source: /home/city6292/auth.log

Source type: syslog

Index: main


4. Detection Query
Ran the following SPL search in Splunk:

splunk
index=main source="/home/city6292/auth.log" "Failed password"
| rex "Failed password for (?<user>\S+) from (?<src_ip>\S+)"
| stats count by src_ip, user
| where count > 3
