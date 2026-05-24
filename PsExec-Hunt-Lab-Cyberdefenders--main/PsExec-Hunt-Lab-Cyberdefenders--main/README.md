# PsExec-Hunt-Lab-Cyberdefenders-
https://cyberdefenders.org/blueteam-ctf-challenges/psexec-hunt/

## Objective:

In this project, I analyze SMB traffic in a pcap to investigate a breach so i can spot lateral movement and the systems affected

## Skills Learned:

Execution - How psexec is executed in the /Admin share 

Discovery - PCAP Analyzation

Lateral Movement - Tracking attacker pivoting from intial compromise to other work station on domain

## Tools Used:

Wire Shark - To analyze lateral movement captured in a PCAP

MITRE ATT&CK Framework - A knowledge base used for mapping and identifying tactics, techniques, and procedures (TTPs) used in adversarial attacks.




## IOC Collected :



IPC$=how hosts communicate with each other used for ipc between computers


ADMIN$ = Your systems administrative share (C:)


PSEXESVC.exe = threat hunt in whatever Siem your using to find psexec


10.0.0.131 = MARKETING-PC


10.0.0.130 = HR_PC Username: ssales


10.0.0.133 = SALES-PC


10.0.0.130 = used psexec to 10.0.0.133 on packet 144



## MITRE ATT&CK Mapping:


This attack demonstrates several MITRE ATT&CK techniques working together:

•	T1021.002 - Remote Services: SMB/Windows Admin Shares (used ADMIN$ and IPC$ shares)

•	T1570 - Lateral Tool Transfer (transferred psexesvc.exe to target machines)

•	T1569.002 - System Services: Service Execution (created temporary Windows service for execution)

## Steps:
1. ## To effectively trace the attacker's activities within our network, can you identify the IP address of the machine from which the attacker initially gained access? I discovered the attacker gained initial access from the machine with IP address 10.0.0.130.

*In Wireshark, I navigated to Statistics > Conversations and sorted the IPv4 addresses by highest bits. I noticed 10.0.0.130 sent the most bits, which was concerning given the context of the lab. I set my filter to ip.addr == 10.0.0.131 or ip.addr == 10.0.0.130, which showed me the frames of the two IP addresses with the most bytes sent between each other. I found that the three-way handshake was initiated from 10.0.0.130 to 10.0.0.133.*![1](https://github.com/user-attachments/assets/d04f8feb-ca59-4cba-9153-2b21d202b4d3)


2. ## To fully understand the extent of the breach, can you determine the machine's hostname to which the attacker first pivoted?

*I set a filter of ip.src == 10.0.0.133 that only showed me the network activity of the selected IP. I clicked on frame 131, which showed the first setup response. Under the NTLM header, I found the target name is SALES-PC.
This means the attacker connected from 10.0.0.130 (HR-PC) to 10.0.0.133 (SALES-PC), which is the first pivot*![2](https://github.com/user-attachments/assets/a3c62c46-91f5-490a-a4af-32ce30974048)


3. ## Knowing the username of the account the attacker used for authentication will give us insights into the extent of the breach. What is the username utilized by the attacker for authentication?

*The attacker used NTLM services to authenticate. Knowing this, I set my filter to ntlmssp.auth.username, which only shows the usernames of accounts used to authenticate. Since we're examining the first machine that was pivoted to, the account used for authentication was ssales.*![3](https://github.com/user-attachments/assets/cfe4fef1-d5d0-4a88-a60d-2082025c5e81)


4. ## After figuring out how the attacker moved within our network, we need to know what they did on the target machine. What's the name of the service executable the attacker set up on the target?

*The service executable is psexesvc.exe. In the PCAP file, we see the create request multiple times in the capture.*![4](https://github.com/user-attachments/assets/a8c2dd1f-2e32-4211-900a-952b5bf00424)


5. ## We need to know how the attacker installed the service on the compromised machine to understand the attacker's lateral movement tactics. This can help identify other affected systems. Which network share was used by PsExec to install the service on the target machine?

*On frame 138, we see 10.0.0.130 performing a Tree Connect Request to host 10.0.0.133 for share \ADMIN$. A Tree Connect Request is a message in the Server Message Block (SMB) protocol where a client asks a server for access to a specific shared folder.*![5](https://github.com/user-attachments/assets/142176db-73aa-4848-84f1-4929a8cf4ed9)


6. ## We must identify the network share used to communicate between the two machines. Which network share did PsExec use for communication?

*To be honest, this question stumped me at first because when I looked at the PCAP, there was communication between more than two machines. On frame 134, there is a Tree Connect Request to \IPC$.
At first glance, I thought this was another connection to a share file. After doing my research, I learned IPC$ is a hidden, administrative network share in Windows used for temporary, secure communication between system processes, especially for remote management tasks like software deployment (e.g., PsExec).*![6](https://github.com/user-attachments/assets/e0f71222-0e50-4192-ac16-88de215351d7)


7. ## Now that we have a clearer picture of the attacker's activities on the compromised machine, it's important to identify any further lateral movement. What is the hostname of the second machine the attacker targeted to pivot within our network? The second hostname pivoted to is MARKETING-PC.

*I set my filter to smb and scrolled through the PCAP until I found the next setup request with an auth frame after it. I determined the target PC was MARKETING-PC, which was authenticated by user jdoe from the HR-PC (10.0.0.130).*![7](https://github.com/user-attachments/assets/31b3a477-919d-48ff-ac4e-2dfa1f6162ac)

## Lab Takeaway:

*The biggest thing I learned from this lab is that when hunting for threats, you have to have a zero-trust approach. What I mean by that is you should investigate everything.
The attacker gained access to the network and was able to pivot to two machines by authenticating with credentials from one workstation. We don't know how this attacker obtained these credentials—whether it was through some form of phishing or if it was an insider threat.
The bottom line is always investigate your environment, no matter how safe you think it is. One set of compromised credentials can turn into a disaster.*
