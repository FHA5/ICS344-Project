# Phase 1: Setup and Compromise the Service

In this phase, we set up the attack and victim environments.  
The victim is a Metasploitable3 machine, which contains multiple vulnerable services.  
Using Metasploit and a custom script, we compromised a selected vulnerable service to gain access.

Below are the detailed steps and screenshots demonstrating the setup and successful exploitation:

---







# Phase 1: Setup and Compromise the Service

---

### Screenshot 1: Launching Metasploit

<img width="521" alt="Screenshot12" src="https://github.com/user-attachments/assets/fb0c3d27-9bf2-4662-9b23-71a9801728ed" />

Launched Metasploit Framework on Kali Linux.  
Initialized msfconsole to prepare for exploiting the victim.  
Ensured environment is ready for launching attacks.

---

### Screenshot 2: Searching for FTP Exploits

<img width="542" alt="Screenshot11" src="https://github.com/user-attachments/assets/ba17e66b-5274-46bc-a833-9ceb4fc84d96" />

Searched for available exploits targeting FTP services.  
Selected the appropriate exploit module for the attack.  
Ready to configure exploit settings.

---

### Screenshot 3: Setting Target and Payload

<img width="583" alt="Screenshot10" src="https://github.com/user-attachments/assets/b369f7b7-dfee-418b-ac9e-c80df53a94a6" />

Set the target host IP address (Metasploitable3).  
Configured the payload and other necessary options.  
Prepared to execute the exploit against the FTP service.

---

### Screenshot 4: Successful Exploitation

<img width="676" alt="Screenshot9" src="https://github.com/user-attachments/assets/7f0e2070-5b65-4e30-a2c3-94006feda3e4" />

Successfully launched the exploit using Metasploit.  
Achieved a remote session with the victim machine.  
Proof of concept: gained access to Metasploitable3.

---

### Screenshot 5: Writing a Custom Script

<img width="773" alt="Screenshot8" src="https://github.com/user-attachments/assets/12ecda00-ed6d-4317-8c67-a2e2e7046408" />

Started crafting a custom attack script manually.  
The script automates exploiting the FTP service.  
This satisfies the project’s requirement for custom exploits.

---

### Screenshot 6: Continuing the Script

<img width="720" alt="Screenshot7" src="https://github.com/user-attachments/assets/7e6beaab-531c-4f78-b170-db5d6c70481a" />

Continued developing the custom script for the attack.  
Added commands for automatic connection and payload delivery.  
Ensured the script matches proof-of-concept goals.

---

### Screenshot 7: Testing the Script

<img width="959" alt="Screenshot6" src="https://github.com/user-attachments/assets/68a68e94-a239-4e65-9224-2518d8d782f9" />

Tested the custom script against the victim environment.  
The script successfully initiated the attack automatically.  
Confirmed script functionality as part of deliverables.

---

### Screenshot 8: Verifying Successful Connection

<img width="776" alt="Screenshot5" src="https://github.com/user-attachments/assets/3590b77b-3925-4ffd-bc83-a630dee2d7ae" />

Verified the successful connection using the custom script.  
Gained unauthorized access to the FTP service without manual exploitation.  
Demonstrated automation of the attack process.

---

### Screenshot 9: Proof of Exploitation

<img width="527" alt="Screenshot4" src="https://github.com/user-attachments/assets/0ec4a285-92cc-424d-a12c-8873d5e5f72d" />

Displayed proof of successful exploitation through custom script.  
Collected evidence of control over the victim machine.  
Ready to move into SIEM data analysis phase.

---

### Screenshot 10: Preparing for SIEM Integration

<img width="695" alt="Screenshot3" src="https://github.com/user-attachments/assets/cba2654c-1720-4ac6-9567-ba25dc3eeef1" />

Prepared environment for SIEM integration after successful attacks.  
Ensured victim logs were collected for later Splunk analysis.  
Verified attack traces exist in system logs.

---

### Screenshot 11: Configuring Log Forwarding

<img width="419" alt="Screenshot2" src="https://github.com/user-attachments/assets/7d9cf94e-3eb7-4041-9e36-76f230d3a0ce" />

Started configuration to forward logs into SIEM platform.  
Organized data from victim and attacker sides.  
Made logs ready for visualization and study.

---

### Screenshot 12: Finalizing Setup for SIEM

<img width="873" alt="Screenshot1" src="https://github.com/user-attachments/assets/5ffa85b9-98a4-4a8b-b3bd-3ce55006568d" />

Finalized setup before beginning Phase 2 analysis.  
Verified that Splunk or other SIEM will receive proper log formats.  
Confirmed successful attack data collection.

---

