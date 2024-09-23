---

# 🛡️ Reverse Shell Attack Prevention Using Snort 🕵️

## Overview 📽️

This project demonstrates how to prevent a reverse shell attack using Snort, focusing on traffic directed to Metasploit's default reverse shell port (TCP 4444). Reverse shells are a common technique used by attackers to gain unauthorized access to a system by initiating connections back to their system.

In this scenario, we will create a simple Snort rule that detects and drops reverse shell traffic on port 4444, effectively preventing the attack.

## Tools and Environment 🧰

- **Operating System:** Ubuntu
- **Intrusion Detection System (IDS):** Snort
- **Network Interface:** `eth0:eth1`
- **Protocol:** TCP
- **Port:** 4444 (Metasploit default reverse shell port)
  
## Setup Instructions

### 1. Install Snort 💻

Ensure Snort is installed on your system. If not, use the following commands to install it:

```bash
sudo apt-get update
sudo apt-get install snort
```

### 2. Create a Snort Rule 📐

We will define a custom rule to drop any TCP traffic attempting to use port 4444 for a reverse shell attack.

1. Navigate to the `rules` directory:

   ```bash
   cd /etc/snort/rules
   ```

2. Open or create the `local.rules` file:

   ```bash
   sudo nano local.rules
   ```

3. Add the following Snort rule:

   ```bash
   drop tcp 10.10.144.156 4444 <> 10.10.196.55 any (msg:"Prevent attack"; sid:10001; rev:1;)
   ```

   - `drop`: Specifies that matching traffic will be dropped.
   - `tcp 10.10.144.156 4444`: Specifies the source IP and port (Metasploit reverse shell default).
   - `<> 10.10.196.55 any`: Matches any destination IP from the attacker to the target.
   - `msg`: Adds a custom alert message "Prevent attack".
   - `sid`: A unique Snort rule ID (choose any unused ID).
   - `rev`: Revision number for the rule.

### 3. Configure Snort 🏗️

Ensure that your Snort configuration file (`/etc/snort/snort.conf`) is set to include the `local.rules` file. Add or uncomment the following line:

```bash
include /etc/snort/rules/local.rules
```

### 4. Running Snort 🚦

You can now run Snort to monitor and prevent the reverse shell attack. Use the following command to activate Snort in inline mode to drop traffic:

```bash
sudo snort -c /etc/snort/snort.conf -q -Q --daq afpacket -i eth0:eth1 -A full
```

- `-c /etc/snort/snort.conf`: Path to Snort configuration.
- `-q -Q`: Run in quiet mode with inline drop mode.
- `--daq afpacket`: Use the `afpacket` DAQ module for inline operation.
- `-i eth0:eth1`: Specifies the network interfaces to monitor.
- `-A full`: Full alert mode for more detailed output.

### 5. Test the Setup 🚦

You can test the setup by attempting to run a reverse shell on port 4444 using Metasploit or another penetration testing tool. Snort should detect and drop the traffic, effectively preventing the attack.

Check Snort logs or alerts to verify the rule is working as expected.

## Logs and Output

The rule will generate detailed logs about the dropped traffic, including timestamps, IP addresses, and TCP options.

Sample Snort log: 🚥

```
09/23-14:50:06.694466 10.10.196.55:54124 -> 10.10.144.156:4444
TCP TTL:64 TOS:0x0 ID:9999 IpLen:20 DgmLen:134 DF
***AP*** Seq: 0xB3F98B06  Ack: 0xF6F12907  Win: 0x1EB  TcpLen: 32
```

You can further analyze these logs to ensure the attack prevention is functioning correctly.


## Conclusion ♾️

This project highlights the effectiveness of Snort in preventing reverse shell attacks by creating custom rules. By using Snort's inline mode, you can drop malicious traffic and secure your system against unauthorized access.

This project is based on one of the Tryhackme.com 's SOC ANALYST rooms 
--- 
