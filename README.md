# SSH-Brute-Force-Attack-using-Hydra-with-Monitoring-via-Wazuh-and-Wireshark
This project demonstrates a brute-force attack on an SSH service using Hydra and shows how to detect and monitor the attack using Wazuh and Wireshark. It highlights both the offensive (attacking) and defensive (monitoring &amp; mitigation) aspects of cybersecurity. 

# Tools & Environment

Victim Machine (Cyber Lab - Linux): Runs SSH service.

Kali Linux: Attacker machine running Hydra.

Wazuh: For log monitoring and intrusion detection.

Wireshark: For analyzing network traffic.

PuTTY: To log in using discovered credentials.

# Step-by-Step Process
1. Setting Up Environment

Start victim machine with SSH service enabled.

Deploy Wazuh for monitoring events.

Prepare Kali Linux for attack execution.

2. Install Hydra
git clone https://github.com/vanhauser-thc/thc-hydra

3. Prepare Credential Files

user.txt → List of possible usernames.

password.txt → List of possible passwords.

4. Run Hydra Attack
hydra -L user.txt -P password.txt 192.168.1.76 ssh


-L = username list

-P = password list

Target: 192.168.1.76 (victim IP)

Protocol: ssh

5. Access via PuTTY

Use the cracked credentials to log in to victim machine.

6. Detecting the Attack
Wazuh Alerts

Authentication Failed (ID 5760): Multiple failed login attempts logged.

Authentication Success (ID 5715): Hydra guessed correct credentials.

PAM Session Opened (ID 5501): Successful shell access obtained.

PAM Session Closed: User logged out.

Wireshark Analysis

Capture SSH traffic during the brute-force attack.

Identify repeated authentication attempts.

Observe successful login patterns.

# Mitigation (Blocking Attacker IP)

Example shell script (block_ip.sh):

#!/bin/bash
if [[ $EUID -ne 0 ]]; then
   echo "Run as root." 
   exit 1
fi

BLOCK_IP="$1"
if [ -z "$BLOCK_IP" ]; then
    echo "Usage: $0 <IP_ADDRESS>"
    exit 1
fi

iptables -A INPUT -s "$BLOCK_IP" -p tcp --dport 22 -j DROP
echo "Blocked SSH access from IP: $BLOCK_IP"

Usage
chmod 777 block_ip.sh
sudo ./block_ip.sh <IP_ADDRESS>


Check blocked IPs:

sudo iptables -L INPUT -v -n

# Summary

This project illustrates how brute-force SSH attacks are executed and detected.

Hydra → Used to perform brute-force attack.

Wazuh → Provides real-time alerts for suspicious login attempts.

Wireshark → Helps analyze traffic patterns.

iptables → Used to block malicious IP addresses.

By combining offensive and defensive techniques, this project highlights the importance of monitoring, detection, and mitigation in securing systems against brute-force attacks.
