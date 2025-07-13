# Active Cyber Defense: A SIEM-Based Threat Detection & Response Lab

This project demonstrates a practical, end-to-end cybersecurity incident response workflow. It simulates a mini-Security Operations Center (SOC) environment where a live brute-force attack is launched, detected in real-time using Splunk, and subsequently neutralized with a host-based firewall rule.

**[Watch the Full Video Demonstration Here]( https://drive.google.com/file/d/1kJp8djP-pT-f3VMBKxpcAQdYe392_j35/view?usp=sharing )**  

## Project Overview

The core challenge this project addresses is the lack of real-time visibility into network threats. Without active monitoring, attacks like SSH brute-force attempts can go unnoticed, providing attackers ample time to compromise systems. This lab proves the effectiveness of using a Security Information and Event Management (SIEM) tool to dramatically shorten the time from attack to detection and response.

### The Attack-Detect-Respond Lifecycle

This project follows a classic incident response model:
1.  **Attack:** A malicious actor (Kali Linux) launches an automated SSH brute-force attack against a server.
2.  **Detect:** The SIEM (Splunk) ingests security logs from the server, identifies the pattern of repeated failed logins, and alerts the analyst to the threat.
3.  **Respond:** The analyst uses the intelligence from the SIEM to implement a firewall rule on the victim server, blocking the attacker's IP address and neutralizing the threat.
4.  **Verify:** The attacker attempts to re-launch the attack, which now fails, confirming the success of the defensive action.

---

## Lab Architecture and Tools

This project was built entirely within a virtualized environment, creating a safe and isolated sandbox for security testing.

*   **Virtualization:** Oracle VM VirtualBox
*   **Networking Mode:** VirtualBox NAT Network (Private Subnet: `10.0.2.0/24`)

|          Role         |         Machine / Tool      |     Operating System     |                          Purpose                         |
| :---------------------| :---------------------------| :------------------------| :--------------------------------------------------------|
| 🔴  **Attacker**      |       Kali Linux VM        |         Kali Linux        |     The "Red Team" machine, used to launch the attack.   |
| 🔵   **Victim**       |      Ubuntu Server VM      |     Ubuntu Server LTS     |    The "Blue Team" asset being monitored and defended.   |
| 👁️     **SIEM**       |     Splunk Enterprise      |      Windows (Host)       |      The central monitoring station for log analysis.    |
| 🚚  **Agent**         | Splunk Universal Forwarder | Ubuntu Server (on Victim) |    Collects and forwards logs from the victim to Splunk. |
| ⚔️ **Offensive Tool** |            Hydra           |         Kali Linux        |    Used to perform the automated SSH brute-force attack. |
| 🛡️ **Defensive Tool** |          iptables          |       Ubuntu Server       | The host-based firewall used to block the attacker's IP. |

---

## Key Configuration Commands

#### 1. Splunk Forwarder Configuration (on Ubuntu-Victim)
Splunk Forwarder Configuration (on Ubuntu-victim): After downloading the
Splunk forwarder on ubuntu:
```
▪ Unpack the file: tar -xvzf splunk.tgz
▪ Go into the bin directory: cd splunkforwarder/bin
▪ Start Splunk: ./splunk start --accept-license
▪ Tell it where my Windows PC's Splunk server is: ./splunk add
forward-server 10.0.2.2:9997
▪ Tell it to watch the login log file: ./splunk add monitor
/var/log/auth.log
▪ Restart the forwarder: ./splunk restart
```


#### 2. Splunk Search Query (on Splunk Enterprise)
This query was used to detect the flood of failed login attempts from a single source.
```splunk
index="test-index" sourcetype="ubuntu_log" "Failed password"
```

#### 3. Firewall Response Rule (on Ubuntu-Victim)
This command was used to block the attacker after their IP was identified in Splunk.
```bash
# -A INPUT: Append a rule to the incoming traffic chain
# -s <KALI_IP>: Specify the source IP to block
# -j DROP: Drop the packet and do not respond
sudo iptables -A INPUT -s <KALI_IP> -j DROP
```

---

## Learning & Challenges

This project was a powerful hands-on lesson in both offensive and defensive cybersecurity techniques. The most significant challenge was overcoming initial environment setup issues, particularly the incompatibility of older virtual machines with modern tools and the complexities of virtual networking on a secured college Wi-Fi. Successfully diagnosing and adapting the project plan—by switching from Metasploitable2 to Ubuntu Server and from a Bridged to a NAT Network—was a critical learning experience that mirrors real-world IT troubleshooting.



