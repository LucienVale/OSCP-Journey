# OSCP-Journey
rooted-by-lucien

OSCP Journey – Chapter 2: Capturing Traffic with ARP Spoofing

Book: Ethical Hacking – A Hands-On Introduction to Breaking In by Daniel G. Graham
Author of Report: Luis  (aka Lucien Vale)
Environment: Kali Linux, VirtualBox, Host-Only Networking

⸻

About This Repository

My name is Luis, and I’m currently transitioning into cybersecurity with a focus on offensive security. I’m teaching myself everything from the ground up, and my goal is to earn the OSCP (Offensive Security Certified Professional) certification. This GitHub will serve as a living journal of my journey—documenting every lab, challenge, and lesson I encounter along the way. I’m not just studying commands—I’m learning to think like a hacker, break systems ethically, and eventually help secure them.

⸻

Chapter 2: Capturing Traffic with ARP Spoofing

This chapter introduced me to the concept and real-world execution of ARP spoofing to intercept traffic on a local network. I worked through tool installations, packet forwarding, host discovery, traffic interception, and scripting my own ARP monitor using Scapy. Every step was hands-on and foundational.

⸻

Key Concepts

What Is ARP Spoofing (My Own Words)

ARP spoofing is a technique where an attacker sends fake ARP messages to associate their MAC address with the IP address of another device (like the router or another host). This tricks both devices into routing traffic through the attacker, who can then observe or modify that data. It’s like pretending to be the mailman and rerouting someone else’s letters through your own address.

How Networks Communicate

Devices use IP addresses to communicate on a network, but to physically send data, they need MAC addresses. ARP (Address Resolution Protocol) helps devices map IPs to MACs. When you want to send something to a specific IP, your machine uses ARP to ask: “Who has this IP?” and waits for a MAC address. ARP spoofing manipulates that exchange.

⸻

Phase 1 – Tool Setup & Network Discovery

Installed dsniff – Contains the arpspoof utility used for the attack:

sudo apt-get install dsniff

Installed netdiscover – Used to identify live IP/MAC pairs on the subnet:

sudo apt-get install netdiscover

Discovered Live Hosts

netdiscover -i eth0

This allowed me to identify the victim and gateway IPs needed for the MITM attack.

⸻

Phase 2 – Enabling Packet Forwarding

echo 1 > /proc/sys/net/ipv4/ip_forward

This command tells the system to forward packets. Without this, intercepted traffic would stop at my machine and break the victim’s internet connection—making the attack obvious. With forwarding enabled, I become a silent router.

⸻

Phase 3 – Launching ARP Spoofing

ARP Spoof Attack:

arpspoof -i eth0 -t [victim_IP] [gateway_IP]
arpspoof -i eth0 -t [gateway_IP] [victim_IP]

This launched a double-sided ARP poisoning attack. I impersonated the router to the victim and the victim to the router.

What Fake ARP Replies Do:
They tell both devices to send traffic to my MAC address, making me the man in the middle. These replies are sent repeatedly to maintain control of the session.

⸻

Phase 4 – Monitoring With Python & Scapy

I wrote a Python script using the Scapy library to detect ARP anomalies. It sniffed packets in real-time and flagged any suspicious changes in IP-to-MAC associations.

sniff(count=0, filter="arp", store=0, prn=processPacket)

This line tells Scapy to capture all ARP packets continuously and process them using my processPacket function.

⸻

What I Learned Through Troubleshooting
	•	IndentationError: I encountered this when my else: statement was misaligned under a try/except block.	
 
	•	Fix: I corrected the indentation so else: aligned properly and ensured consistent 4-space tabs throughout the script.
 
	•	Scapy Installation: Initially, Scapy wouldn’t run due to missing dependencies.
 
	•	Fix: I resolved this by running:
pip3 install --pre scapy[basic]

	•	Network Configuration Across Virtual Machines: I ran into multiple network-related issues when trying to connect Kali Linux, Metasploitable2, and pfSense inside VirtualBox.
 
	•	Fix: I had to experiment with different network adapter settings—bridged, NAT, and host-only—to properly isolate traffic and enable inter-VM communication. Ultimately, I configured all machines to use host-only networking to simulate a real LAN environment while keeping external traffic isolated. pfSense acted as the firewall/router, allowing me to simulate real-world scenarios.
 
	•	I also verified IP assignments, confirmed gateway routes, and ensured DNS resolution worked across all machines. Troubleshooting this setup taught me how networking works at a practical level and gave me a clearer understanding of traffic flow, routing, and segmentation.
 
	•	Command Flow and Dependencies: At various points, I executed commands that failed silently or returned unclear errors.
 
	•	Fix: I learned to methodically trace back each command, check the order of execution, and verify prerequisite conditions (e.g., enabling packet forwarding before spoofing). This taught me the importance of thinking in steps—not just running tools blindly.

pip3 install --pre scapy[basic]


	•	Command Flow: I learned how each command builds on the previous one to create a complete attack chain.

⸻

What It Means to Capture Internet Traffic

Once the spoof was successful, I could monitor real-time traffic from the victim. This included DNS lookups, visited domains, and any unencrypted HTTP requests. It’s a serious demonstration of how easy it is to violate someone’s privacy if they’re on an insecure network.

⸻

Reflections

This wasn’t just a script-kiddie moment. I fully understood what each command did, why it mattered, and how each small failure taught me something deeper. I didn’t just copy-paste—I dissected. I troubleshot. I stayed up through the night pushing through errors. That’s what builds a hacker’s mind.

⸻

What’s Next

In Chapter 3, I’ll move into deeper traffic analysis using Wireshark and TCPDump, dissecting unencrypted packets and learning how to pull out meaningful data like passwords, cookies, and DNS queries from raw network streams. The journey continues.
