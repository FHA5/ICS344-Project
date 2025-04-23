# 🛡️ Phase 3: Defensive Strategy

## 🎯 Objective
Implement a defensive mechanism on the victim (Metasploitable3) system to prevent or detect SSH-based attacks. This includes configuration changes and the deployment of monitoring tools, followed by validation testing.

---

## 🔐 Defensive Mechanism
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

---

## 🧪 Testing & Validation
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

## ✅ Outcome
- The SSH configuration hardening prevented the same brute-force and reverse shell attacks demonstrated earlier.
- Splunk SIEM confirms no successful SSH logins occurred after the defense.

---

## 📸 Deliverables
- Screenshot of the updated `/etc/ssh/sshd_config`.
- Screenshot of failed login attempts via Hydra and Metasploit.
- Splunk dashboard screenshot showing drop in successful SSH access.

---

Return to [Phase 2](./PHASE1/README.md) or [Phase 1](./PHASE1/README.md)

