# cybersecurity-portfolio
Hands-on cybersecurity home lab projects, SOC detection engineering and incident response documentation
# Cyber Security Portfolio

Hands-on cybersecurity home lab projects focused on SOC analysis, detection engineering, and incident response.

## About Me
Computer Science (Cyber Security) student building practical skills through hands-on labs and documentation.

## Environment

### Home Lab Setup
- **Hypervisor:** VirtualBox 7.x
- **Attacker VM:** Kali Linux (192.168.56.101)
- **Victim VM:** Metasploitable2 (192.168.56.102)
- **Network:** Host-Only Adapter (`vboxnet0`, 192.168.56.1/24)
- **DHCP Server:** Enabled (pool 192.168.56.101-254)
- **Isolation verified:** `ping 8.8.8.8` from Kali fails (no internet route)

### Troubleshooting Log
1. **Issue:** Host-Only Adapter option was missing from VM network settings.
   **Fix:** Switched VirtualBox Preferences from "Basic" to "Expert" mode to expose Tools → Network.

2. **Issue:** Metasploitable2 was on Bridged Adapter, leaking onto the real home network (192.168.100.x).
   **Fix:** Changed adapter to Host-Only. Ran `sudo dhclient eth0` to obtain a new IP on 192.168.56.x.

3. **Issue:** Multiple Host-Only networks existed; DHCP was only enabled on the wrong one.
   **Fix:** Enabled DHCP on the `192.168.56.x` adapter and confirmed both VMs were attached to the same network.

### Safety Notes
- Never use Bridged mode for vulnerable VMs.
- Always verify isolation (`ping 8.8.8.8` should fail) before running any offensive tooling.

## Projects

| # | Project | Status | Tools |
|---|---------|--------|-------|
| 1 | SSH Brute-Force Detection in Splunk | 🚧 In Progress | Kali, Hydra, Splunk |
| 2 | Port Scan Detection Engineering Lab | ⏳ Planned | Kali, Nmap, Splunk |
| 3 | Reverse Shell Network Detection Study | ⏳ Planned | Kali, Metasploit, Wireshark |
| 4 | End-to-End SOC Investigation Simulation | ⏳ Planned | Kali, Splunk, Wireshark |
| 5 | Custom Log-Based Intrusion Detection Script | ⏳ Planned | Python, Splunk |
| 6 | Beaconing Traffic Detection Lab | ⏳ Planned | Kali, Wireshark, Splunk |
| 7 | Exploitation Visibility Analysis | ⏳ Planned | Kali, Metasploit, Splunk |
| 8 | Web Attack Detection in SIEM | ⏳ Planned | Kali, DVWA, Splunk |

## Contact
- GitHub: [@choyml29mao](https://github.com/choyml29mao)
- LinkedIn: [Your LinkedIn URL]
