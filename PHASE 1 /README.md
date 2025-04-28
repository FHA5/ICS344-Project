# Phase 1: Setup and Compromise the Service

---

### Screenshot 1: Launching Metasploit

<img width="600" src="screenshots/Screenshot1.png" alt="Launching Metasploit" />

Launched Metasploit Framework on Kali Linux.  
Initialized msfconsole to prepare for exploiting the victim.  
Ensured environment is ready for launching attacks.

---

### Screenshot 2: Searching for FTP Exploits

<img width="600" src="screenshots/Screenshot2.png" alt="Searching for FTP Exploits" />

Searched for available exploits targeting FTP services.  
Selected the appropriate exploit module for the attack.  
Ready to configure exploit settings.

---

### Screenshot 3: Setting Target and Payload

<img width="600" src="screenshots/Screenshot3.png" alt="Setting Target and Payload" />

Set the target host IP address (Metasploitable3).  
Configured the payload and other necessary options.  
Prepared to execute the exploit against the FTP service.

---

### Screenshot 4: Successful Exploitation

<img width="600" src="screenshots/Screenshot4.png" alt="Successful Exploitation" />

Successfully launched the exploit using Metasploit.  
Achieved a remote session with the victim machine.  
Proof of concept: gained access to Metasploitable3.

---

### Screenshot 5: Writing a Custom Script

<img width="600" src="screenshots/Screenshot5.png" alt="Writing a Custom Script" />

Started crafting a custom attack script manually.  
The script automates exploiting the FTP service.  
This satisfies the project’s requirement for custom exploits.

---

### Screenshot 6: Continuing the Script

<img width="600" src="screenshots/Screenshot6.png" alt="Continuing the Script" />

Continued developing the custom script for the attack.  
Added commands for automatic connection and payload delivery.  
Ensured the script matches proof-of-concept goals.

---

### Screenshot 7: Testing the Script

<img width="600" src="screenshots/Screenshot7.png" alt="Testing the Script" />

Tested the custom script against the victim environment.  
The script successfully initiated the attack automatically.  
Confirmed script functionality as part of deliverables.

---

### Screenshot 8: Verifying Successful Connection

<img width="600" src="screenshots/Screenshot8.png" alt="Verifying Successful Connection" />

Verified the successful connection using the custom script.  
Gained unauthorized access to the FTP service without manual exploitation.  
Demonstrated automation of the attack process.

---

### Screenshot 9: Proof of Exploitation

<img width="600" src="screenshots/Screenshot9.png" alt="Proof of Exploitation" />

Displayed proof of successful exploitation through custom script.  
Collected evidence of control over the victim machine.  
Ready to move into SIEM data analysis phase.

---

### Screenshot 10: Preparing for SIEM Integration

<img width="600" src="screenshots/Screenshot10.png" alt="Preparing for SIEM Integration" />

Prepared environment for SIEM integration after successful attacks.  
Ensured victim logs were collected for later Splunk analysis.  
Verified attack traces exist in system logs.

---

### Screenshot 11: Configuring Log Forwarding

<img width="600" src="screenshots/Screenshot11.png" alt="Configuring Log Forwarding" />

Started configuration to forward logs into SIEM platform.  
Organized data from victim and attacker sides.  
Made logs ready for visualization and study.

---

### Screenshot 12: Finalizing Setup for SIEM

<img width="600" src="screenshots/Screenshot12.png" alt="Finalizing Setup for SIEM" />

Finalized setup before beginning Phase 2 analysis.  
Verified that Splunk or other SIEM will receive proper log formats.  
Confirmed successful attack data collection.

---
