
#  Phase 2: SIEM - Dashboard Analysis

##  Objective

This phase focuses on **Security Information and Event Management (SIEM)** using **Splunk**. The goal is to:
- Collect and forward logs from the **Metasploitable3 victim machine**.
- Ingest and index logs on **Splunk running on the Kali (attacker) VM**.
- Visualize attack events such as brute-force logins and reverse shell triggers.
- Build dashboards to observe timelines and behavior changes during exploitation.

---

##  Splunk Server Setup (Kali VM)

### Why Splunk?

Splunk provides real-time log analysis, making it an ideal SIEM solution for identifying anomalies and attack patterns. It supports:
- Log collection from multiple sources
- Indexing and searching large log volumes
- Graphical dashboard visualization

### Installation
```bash
wget -O splunk-9.3.2.deb https://download.splunk.com/products/splunk/releases/9.3.2/linux/splunk-9.3.2-d8bb32809498-linux-2.6-amd64.deb
sudo dpkg -i splunk-9.3.2.deb
sudo /opt/splunk/bin/splunk start --accept-license
sudo /opt/splunk/bin/splunk enable boot-start
```
- **Why this?** This installs Splunk Enterprise and configures it to start on system boot.

### Access Web Interface
Visit Splunk via browser:
```
http://192.168.142.129:8000
```

- **Default Credentials**:  
  - Username: `admin`  
  - Password: `admin123`

### Configure Receiving on TCP Port 9997
This port is used by the Splunk **Universal Forwarder** to send logs.

Steps:
1. Go to **Settings > Forwarding and Receiving**
2. Click **Add New** under “Receive data”
3. Enter **9997** and click Save

 This sets up the Splunk server to listen for logs on TCP port 9997.

---

##  Splunk Forwarder Setup (Metasploitable3 VM)

### Why Use a Forwarder?

The **Universal Forwarder** is a lightweight Splunk agent that securely forwards local logs to the central SIEM server.

### Installation
```bash
wget -O splunkforwarder.tgz https://download.splunk.com/products/universalforwarder/releases/9.4.1/linux/splunkforwarder-9.4.1-e3bdab203ac8-linux-amd64.tgz
tar -xvzf splunkforwarder.tgz
sudo mv splunkforwarder /opt/splunkforwarder
sudo /opt/splunkforwarder/bin/splunk start --accept-license
```

### Connect to Splunk Server (Kali)
```bash
sudo /opt/splunkforwarder/bin/splunk enable boot-start
sudo /opt/splunkforwarder/bin/splunk login -auth admin:admin123
sudo /opt/splunkforwarder/bin/splunk add forward-server 192.168.142.129:9997
```
 This links the victim’s logs to the Splunk instance on the attacker's VM.

### Monitor SSH Logs
```bash
sudo /opt/splunkforwarder/bin/splunk add monitor /var/log/auth.log
sudo /opt/splunkforwarder/bin/splunk restart
```

 **Why /var/log/auth.log?**  
This log records authentication events (successful and failed SSH logins), making it ideal for monitoring brute-force and session hijacks.

---

##  Attacks Simulated

### 1. SSH Brute-force via Hydra
```bash
hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt ssh://192.168.142.131 
```
- Simulates a real-world password attack using common credentials.

### 2. Reverse Shell via Python
```bash
run ssh_bruteforce_reverse.py
```
- Once connected, the victim's shell is controlled remotely via TCP.

### 3. Netcat Listener on Kali
```bash
nc -lvnp 4444
```
- Used by the attacker to capture the reverse shell connection.

---

## 📊 Visualization in Splunk

### Open the Search Dashboard
```
http://192.168.142.129:8000/en-US/app/search/search
```

### Sample Queries

#### Failed SSH Login Attempts
```spl
index=main "Failed password"
```
- Useful for identifying brute-force patterns.

#### Successful SSH Logins
```spl
index=main "Accepted password"
```
- Confirms whether the attacker successfully accessed the victim.

#### Reverse Shell Detection
```spl
index=main "session opened" OR "session closed"
```
- Helps track session lifecycle after compromise.

### Creating Visual Dashboards
Use Splunk’s **Visualization > Bar Chart**:
- X-Axis: `_time`
- Y-Axis: `count`
- Split By: `event_type` or `host`

 **Why Visualize?**
Charts reveal the timeline and intensity of attacks—key for incident response and forensic analysis.

---


##  Conclusion

- Successfully implemented log forwarding from a vulnerable system to a SIEM.
- Used Splunk to monitor and analyze SSH brute-force and reverse shell behaviors.
- Demonstrated how timelines can expose attacker progression from login to shell control.
