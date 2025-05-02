#  Phase 2: SIEM - Dashboard Analysis

## Objective

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

![Screenshot13](../screenshots/Screenshot13.png)
![Screenshot14](../screenshots/Screenshot14.png)
![Screenshot15](../screenshots/Screenshot15.png)
![Screenshot16](../screenshots/Screenshot16.png)

### Access the Web Interface

```
http://192.168.142.129:8000
```

Default credentials:

- **Username:** `admin`
- **Password:** `admin123`

---

### Enable Receiving on TCP Port 9997

1. Go to **Settings > Forwarding and Receiving**
2. Under **Receive data**, click **Add New**
3. Set port to **9997** and click **Save**

<img width="955" alt="Screenshot 2025-04-22 163351" src="https://github.com/user-attachments/assets/f1d308a3-ddc1-484f-8561-2875aeaf4c65" />
<img width="956" alt="Screenshot 2025-04-22 163425" src="https://github.com/user-attachments/assets/d65323f5-7de3-4b22-b498-c477cfb91389" />

---


##  Splunk Forwarder Setup (Metasploitable3 VM)

### Why Use a Forwarder?

The **Universal Forwarder** is a lightweight agent that forwards system logs securely to the Splunk server.

### Installation

```bash
wget -O splunkforwarder-9.4.1-e3bdab203ac8-linux-amd64.deb "https://download.splunk.com/products/universalforwarder/releases/9.4.1/linux/splunkforwarder-9.4.1-e3bdab203ac8-linux-amd64.deb"
sudo dpkg -i splunkforwarder-9.4.1-e3bdab203ac8-linux-amd64.deb
sudo apt --fix-broken install
sudo /opt/splunkforwarder/bin/splunk start --accept-license
```
### Connect to Splunk Server (Kali)

```bash
sudo /opt/splunkforwarder/bin/splunk add forward-server 192.168.142.129:9997

```

### Monitor Authentication Logs

```bash
sudo /opt/splunkforwarder/bin/splunk add monitor /var/log/auth.log
sudo /opt/splunkforwarder/bin/splunk restart
```
![Screenshot18](../screenshots/Screenshot18.png)

Installed Splunk Universal Forwarder on the victim machine.  
Accepted the license and started the forwarder service.  

---

![Screenshot21](../screenshots/Screenshot21.png)

---

Authenticated with Splunk forwarder CLI and added forwarding to Splunk server (port 9997).  
Monitored `/var/log/auth.log` and listed active/inactive forwarders.  
Successfully verified log forwarding setup from victim to Splunk.

---

##  Attacks Simulated

### 1. SSH Brute-force via Hydra

```bash
hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt ssh://192.168.142.131
```
### 2. Netcat Listener on Kali

```bash
nc -lvnp 4444
```
### 3. Reverse Shell via Python Script

```bash
run ssh_reverse_shell_1.2.py
```

---

## Visualization in Splunk

### Open the Search Dashboard

```
http://192.168.142.129:8000/en-US/app/search/search
```

### Example Queries

#### Failed SSH Login Attempts

```spl
index=main "Failed password"
```

#### Successful SSH Logins

```spl
index=main "Accepted password"
```

#### Reverse Shell Activity

```spl
index=main "session opened" OR "session closed"
```

#### All SSH Events from auth.log

```spl
index=* source="/var/log/auth.log"
```

![Screenshot22](../screenshots/Screenshot22.png)
![Screenshot23](../screenshots/Screenshot23.png)
![Screenshot24](../screenshots/Screenshot24.png)
![Screenshot25](../screenshots/Screenshot25.png)
![Screenshot26](../screenshots/Screenshot26.png)
![Screenshot27](../screenshots/Screenshot27.png)
![Screenshot28](../screenshots/Screenshot28.png)

---

### Create Bar Chart Visualization

```spl
index=* source="/var/log/auth.log"
| eval event_type=case(
  match(_raw, "Failed password"), "Brute-Force Attempt",
  match(_raw, "Accepted password"), "Successful Login",
  match(_raw, "session closed"), "Session Close (Reverse Shell)",
  true(), "Other"
)
| stats count by event_type
```

---

## Log Test (Verification)

```bash
sudo logger -p auth.info "PHASE2 SSH ONLY TEST: $(date)"
```

```spl
index=* source="/var/log/auth.log"
```

---

## Defense Setup: Fail2Ban

### Install Fail2Ban

```bash
sudo apt-get install fail2ban -y
```

### Configure Fail2Ban (Jail)

```ini
[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
maxretry = 3
bantime = 600
findtime = 300
```

### Check Jail Status

```bash
sudo fail2ban-client status ssh
```

![Screenshot29](../screenshots/Screenshot29.png)
![Screenshot30](../screenshots/Screenshot30.png)
![Screenshot31](../screenshots/Screenshot31.png)
![Screenshot32](../screenshots/Screenshot32.png)
![Screenshot33](../screenshots/Screenshot33.png)

---

## ✅ Conclusion

- Successfully configured log forwarding from Metasploitable3 to Splunk.
- Simulated brute-force and reverse shell attacks, tracked in real time.
- Built visual dashboards to show attacker progression.
- Hardened the system using Fail2Ban and validated attack prevention.
