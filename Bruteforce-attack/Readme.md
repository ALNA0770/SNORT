# 🛡️ Snort Detection and Prevention 🕵️‍♂️ Scenario: Attack on Tech-Coffee shop ☕ 

## Project Overview

This project was part of a cybersecurity exercise where we simulated an attack on a new trending market. The scenario was designed to improve our understanding of intrusion detection and prevention systems using **Snort**. The exercise involved analyzing traffic, identifying suspicious activity, and creating Snort rules to mitigate the attack. The goal was to detect and prevent malicious behavior on the network, ultimately securing a flag as proof of successful defense.

## Objective

- Detect and analyze suspicious network traffic using Snort.
- Identify a potential attack targeting **SSH** (port 22).
- Write and implement a Snort rule to block the attack.
- Successfully prevent the attack and capture the flag.

## Tools and Technologies 🧰

- **Snort**: Network Intrusion Detection and Prevention System (NIDS/NIPS).
- **SSH**: Secure Shell protocol used for secure communications over a network.
- **Linux**: Operating system environment where Snort was deployed.

## Scenario Description 📘

In this scenario, we were tasked with securing a new trending market that was under a targeted cyber attack. The attack involved attempts to gain unauthorized access to the market's backend via SSH connections on **port 22**. Our job was to monitor network traffic, identify the source of the attack, and take appropriate action to prevent further damage.

## Steps Followed 👣

### 1. **Traffic Analysis with Snort** 🚦
I started by using **Snort** to inspect what was happening on the network. Snort logs revealed unusual activity and a suspicious number of connection attempts to **port 22**, which is commonly used for **SSH**.

Using the following command, I initiated Snort in network intrusion detection mode:

```bash
snort -A console -i eth0 -c /etc/snort/snort.conf -l /var/log/snort
```

This allowed me to see the incoming traffic and identify potential threats.

### 2. **Identifying Suspicious SSH Connections** 🛜
Upon further investigation, I noticed multiple attempts to access the server via SSH. Since SSH is a secure protocol but often targeted for brute-force attacks, I focused on these connections and considered them suspicious.

**Sample Snort Logs:**

```
WARNING: No preprocessors configured for policy 0.
09/23-12:55:03.255832 10.100.1.33:48522 -> 10.10.202.240:80
TCP TTL:64 TOS:0x0 ID:1124 IpLen:20 DgmLen:68 DF
***AP*** Seq: 0x3BB2F1B8  Ack: 0x5480C6EB  Win: 0x1292  TcpLen: 32
TCP Options (3) => NOP NOP TS: 3889439242 3134665395 
=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+

... [Additional log entries] ...

09/23-12:55:03.495435 10.100.1.33:48530 -> 10.10.202.240:80
TCP TTL:64 TOS:0x0 ID:62833 IpLen:20 DgmLen:68 DF
***AP*** Seq: 0xC03FB6AE  Ack: 0xCF1E531B  Win: 0x248B  TcpLen: 32
TCP Options (3) => NOP NOP TS: 3889439364 3134665553 
```

**Analysis of Logs:** 👨‍💻

- **Repeated Warnings**: `"WARNING: No preprocessors configured for policy 0."` indicated that Snort wasn't fully configured to handle certain types of traffic, potentially missing out on detecting more sophisticated attacks.
- **Suspicious Traffic**: Multiple TCP connections were being made to **port 22 (SSH)** from IP addresses like `10.10.140.29` targeting `10.10.245.36`. The frequent connection attempts suggested a brute-force or reconnaissance attack aiming to compromise SSH access.

### 3. **Writing a Snort Rule to Block the Attack**
Once I confirmed the nature of the attack, I wrote a simple Snort rule to drop any incoming SSH connections from the attacking IP address to our server. This rule was added to the **local.rules** file.

**Custom Snort Rule:** 📐

```snort
# ----------------
# LOCAL RULES
# ----------------
# This file intentionally does not come with signatures.  Put your local
# additions here.
drop tcp 10.10.140.29 22 <> 10.10.245.36 any (msg:"Dropped SSH Attack Traffic"; sid:10001; rev:1;)
```

**Explanation of the Rule:**

- **Action**: `drop` - Instructs Snort to block the traffic matching this rule.
- **Protocol**: `tcp` - Specifies that the rule applies to TCP traffic.
- **Source IP and Port**: `10.10.140.29 22` - Targets traffic from IP `10.10.140.29` on port `22`.
- **Direction**: `<>` - Applies the rule in both directions (bidirectional).
- **Destination IP and Port**: `10.10.245.36 any` - Targets traffic to IP `10.10.245.36` on any port.
- **Message**: `msg:"Dropped SSH Attack Traffic"` - Logs a message when this rule is triggered.
- **SID and Rev**: `sid:10001; rev:1;` - Unique identifiers for the rule.

**Applying the Rule:**

After adding the rule to the `local.rules` file, I restarted Snort to apply the changes:

```bash
sudo systemctl restart snort
```

### 4. **Capturing the Flag**
With the custom rule in place, Snort began dropping the malicious SSH traffic from `10.10.140.29` to `10.10.245.36`. This effectively prevented the attack from succeeding. As a result of successfully blocking the attack, the system secured the environment and provided a flag, confirming the defense was successful.

### 5. **Addressing Snort Configuration Warnings**
The repeated warnings `"No preprocessors configured for policy 0."` indicated that Snort's preprocessors were not set up for the current policy. Preprocessors are essential for parsing and normalizing network traffic, enabling Snort to detect more complex attacks.

**Solution:**

- **Review Snort Configuration**: Ensure that the `snort.conf` file has the necessary preprocessors enabled for the policies in use.
- **Enable Relevant Preprocessors**: Depending on the attack vectors anticipated, enable preprocessors like `http_inspect`, `stream`, `frag3`, etc.

**Example Configuration Snippet:**

```snort
# Example of enabling HTTP preprocessor
preprocessor http_inspect: global \
    iis_unicode_map unicode.map 1252

preprocessor http_inspect_server: server default \
    profile all \
    ports { 80 443 } \
    oversize_dir_length 500
```

By configuring preprocessors appropriately, Snort can enhance its detection capabilities and reduce the likelihood of missing sophisticated attacks.

## Key Learnings

- **Snort as an IDS/IPS**: This exercise demonstrated the effectiveness of Snort in identifying and blocking suspicious activity on a network.
- **Rule Writing**: I learned how to write Snort rules tailored to specific threats, in this case, SSH brute-force attempts.
- **Traffic Monitoring**: Gained experience in analyzing network traffic and identifying patterns of malicious behavior.
- **Configuration Management**: Understood the importance of configuring Snort preprocessors to maximize detection capabilities.

## How to Run the Project ✴️

1. **Install Snort**:
   Follow the instructions to install Snort on your Linux machine.

   ```bash
   sudo apt-get update
   sudo apt-get install snort
   ```

2. **Configure Snort**:
   Ensure that Snort is properly configured by editing the `snort.conf` file located in `/etc/snort/`.

3. **Add Custom Rules**:
   Open the `local.rules` file and add your custom rules.

   ```bash
   sudo nano /etc/snort/rules/local.rules
   ```

   Add the following rule:

   ```snort
   drop tcp 10.10.140.29 22 <> 10.10.245.36 any (msg:"Dropped SSH Attack Traffic"; sid:10001; rev:1;)
   ```

4. **Configure Preprocessors**:
   Edit the `snort.conf` file to enable necessary preprocessors.

   ```bash
   sudo nano /etc/snort/snort.conf
   ```

   Ensure preprocessors like `http_inspect` are enabled as needed.

5. **Run Snort in IPS Mode**:
   Run Snort to monitor and block malicious traffic.

   ```bash
   snort -A console -Q -c /etc/snort/snort.conf -i eth0 -l /var/log/snort
   ```

   - **-A console**: Output alerts to the console.
   - **-Q**: Enable inline mode (IPS).
   - **-c**: Specify the configuration file.
   - **-i**: Specify the network interface.
   - **-l**: Specify the log directory.

6. **Monitor and Verify**:
   - **Check Logs**: Monitor `/var/log/snort/alert` and other log files to verify that the rule is being triggered.
   - **Test the Rule**: Attempt to initiate an SSH connection from `10.10.140.29` to `10.10.245.36` and observe that the connection is dropped.

7. **Flag Capture**:
   Once the attack is blocked, the system will indicate that the flag has been captured, confirming a successful defense.

## Conclusion

This project demonstrated how effective **Snort** can be in protecting a network from attacks. By monitoring network traffic, analyzing logs, and creating custom Snort rules, we were able to prevent an SSH brute-force attack and secure the environment. Additionally, addressing Snort configuration issues, such as enabling preprocessors, enhanced our detection capabilities. This scenario highlights the importance of proactive monitoring, accurate threat identification, and timely response in cybersecurity.

Tryhackme.com
---
