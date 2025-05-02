#  Phase 3: Defensive Strategy

## Objective
Implement a defensive mechanism on the victim (Metasploitable3) system to prevent or detect SSH-based attacks. This includes configuration changes and the deployment of monitoring tools, followed by validation testing.

---

##  Defensive Mechanism
### SSH Hardening
1. **Disable Root Login:**
   ```bash
   sudo nano /etc/ssh/sshd_config
   # Set the following:
   PermitRootLogin no
   ```
2. **Disable Password Authentication:**
   ```bash
   sudo nano /etc/ssh/sshd_config
   # Set the following:
   PasswordAuthentication no
   ```
3. **Restart SSH service:**
   Metasploitable3 uses SysVinit:
   ```bash
   sudo service ssh restart
   ```
<img width="709" alt="Screenshot 2025-04-23 025333" src="https://github.com/user-attachments/assets/bda342d1-004b-4a3c-997c-dfd0139270a6" />

---

## Testing & Validation
### Before Defense
- **SSH Brute-force via Hydra:**
   ```bash
   hydra -L usernames.txt -P rockyou.txt ssh://<victim-ip>
   ```
   - Attack successful: `vagrant:vagrant` credentials obtained.

- **Metasploit `ssh_login` Module:**
   ```bash
   use auxiliary/scanner/ssh/ssh_login
   set USERNAME vagrant
   set PASSWORD vagrant
   set RHOSTS <victim-ip>
   run
   ```
   - Successfully opened shell session.

### After Defense
- Rerun the above Hydra and Metasploit modules.
- Results:
  - Hydra: All attempts fail due to `Permission denied` (username/password rejected).
  - Metasploit: Attack fails due to SSH password authentication being disabled.

---
<img width="689" alt="Screenshot 2025-04-23 030038" src="https://github.com/user-attachments/assets/da20433b-2a24-431d-8a75-16505bc15971" />

<img width="644" alt="Screenshot 2025-04-23 025808" src="https://github.com/user-attachments/assets/cc837c63-c220-4c9f-8cbb-c3ea1c575c03" />


##  Outcome
- The SSH configuration hardening prevented the same brute-force and reverse shell attacks demonstrated earlier.
- Splunk SIEM confirms no successful SSH logins occurred after the defense.

---
