# Information Security Fundamentals
Information security is state where the possiblility of tampering, theft or distruption of information or services on a system is low or tolerable.

Elements of information security includes:
- Confidentiality: Assurance that the information is accessible only to those authorized to have access
- Integrity: The assurance that the information is trustworthy
- Availability: Assurance that the information is accessible when required by the authorized user
- Non-Repudiation: Assurance that the sender of a message cannot later deny having sent the message and the recipient cannot deny having received the message

The security(Restriction), Functionality(features) and Usability(GUI) triangle: The higher the security in a system, the lesser the functionality and usability.

Classification of Attacks
- Passive Attacks: Does not affect system resources nor alter data. No interference and usually undetectable e.g sniffing, footprinting, traffic analysis etc.
- Active Attacks: Tamper with data or disrupt services e.g DoS, MITM, session hijacking etc.
- Close-in Attacks: Performed when in close proximity to the target e.g eavesdropping, shoulder surfing etc.
- Insider Attacks: Using privileged info to cause a threat. e.g planting keyloggers as an employee.

Information Security Vectors
- Cloud computing Threats: Error in configuration can allow attacker gain access
- Advanced Persistent Threat: Long-term stealthy attack designed to stay inside a network for months or even years.
- Viruses and Worms: Worms are independent and can spread very fast without human intervention while virus is dependent on a file and requires human action like opening the infected file for it to replicate.
- Phishing
- Ransomware
- Botnet
- Web Application Threats
- IoT Threats

Information Security Laws and Regulations
- Payment Card Industry Data Security Standard(PCI DSS): Standard for organizations that handle cardholder information
- Health Insurance Portability and Accountability Act(HIPAA): A US law that Provides protections for the personal health information(PHI) held by covered entities(doctors, hospitals etc) and their business associates.
- Sarbanes Oxley Act(SOX): Protects investors and the public from corporate fraud by increasing the accuracy and reliability of corporate dsiclosures.
- General Data Protection Regulation(GDPR): Toughest privacy and security law in the world passed by the European Union(EU). it imposes obligations onto organizations anywhere, so long as they target or collect data related to people in the EU

If HIPAA is about "Health," and SOX is about "Finance," GDPR is about "The Individual.
- The Digital Millennium Copyright Act(DMCA)

# Ethical Hacking Fundamentals
Cyber Kill Chain: It is a model that illustrate how an attacker execute an attack against a system. It shows the various TTPs involved and could help organization understand the various possible threats at various stage of attack and how to mitigate.
The methodology involves seven different phases
- Reconnaissance: Gather info about target
- Weaponization: Create deliverable payload based on gathered info
- Delivery: Send payload to victim via email, USB etc
- Expoitation: Exploit a vulnearbility by running payload on target system in order to break-in(initial entry)
- Installation: Install a persistent backdoor e.g web shell or Trojan for subsequent entries
- Command and Control: Establish a two-way communication system to the target
- Actions on Objectives: Perform action to achieve intended goal e.g data exfiltration, ransomware etc.

Tactics, Techniques, and Procedures (TTPs): While most security tools look for "digital signatures" (like a fingerprint), TTPs focus on the "personality" and "methods" of the hacker. If a hacker changes their tools (like wearing gloves to hide fingerprints), their behavior (TTPs) usually stays the same. The Tactics is the Goal (The "Why"). What is the attacker trying to achieve?. The Techniques is the Method (The "How"). What general approach are they using?. The Procedures is the Steps (The "What exactly"). The specific, step-by-step instructions.

Indicators of Compromise (IoCs): Clues and signs that indicate potential breach in a network or system
- Network-Based IoCs: Malicious IP, unusual port actvities, unusual outbound traffic etc.
- Host-Based IoCs: File hashes, registry change, unexpected software etc.
- Behavioral IoCs: Failed login, doc executing Power shell script, privilege escalation etc.

Understanding The Different Phases of Hacking Cycle
- Reconnaissance(active & passive): target organization’s clients, employees,  operations, network, and systems. Usernames, passwords, credit card statements, bank statements, ATM receipts, Social Security numbers, private telephone numbers, checking account numbers.  Employees’ contact information, business partners, technologies currently in use, and other critical business knowledge. Company’s IP addresses, domain names, and contact information. Tools include: web data extractor(http://www.webextractor.com ), whois(https://whois.domaintools.com), ICMP Traceroute(e.g tracert 192.267.32.2 on windows), TCP Traceroute(e.g tcptraceroute www.google.com), UDP Traceroute(traceroute www.google.com)
- Scanning:  scans the network for specific information based on information gathered during reconnaissance. Tools include: port scanners(Nmap, MegaPing, unicornscan, zenmap), network mappers(zenmap), ping tools(ping, fping, hping3), and vulnerability scanners(Nessus, Burp suite). Extract information such as live machines, port, port status, OS details, device type, and system uptime. Enumeration tools(NetBIOS Enumerator, Nbtstat Utility(nbtstat -a 192.168.86.2 on windows)
- Gaining Access:  access at the operating system, application, or network levels. escalate privileges to obtain complete control of the system e.g password cracking, buffer overflows, DoS, and session hijacking
- Maintaining Access: Retain ownership of the system through backdoor, rootkit or trojan. Use system to launch further attack
- Clearring tracks: Remain unnoticed by clearing malicious acts traces and evidence by overwriting server, application and network logs

Ethical Hacking Concepts, Scope, and Limitations

- Concept: Ethical hacking is the practice of employing computer and network skills in order to assist organizations in testing their network security for possible loopholes and vulnerabilities with the permission of concerned authorities
- Scope: Ethical hacking is a crucial component of risk assessment, auditing, counter fraud, and information systems security best practices. It is used to identify risks and highlight remedial actions
- Limitation: Unless the business already know what they are looking for and why they are hiring an outside vendor to hack systems in the first place, chances are there would not be much to gain from the experience. An ethical hacker can only help the organization to better understand its security system; it is up to the organization to place the right safeguards on the network.

Skills of an Ethical Hacker

### Technical Skills
- Operating System Proficiency: Linux/Unix(CLI, shell scripting etc.) Windows & Active Directory(powershell)
- Networking Fundamentals: Protocols(HTTPs, OSI model, TCP/IP, DNS, SNMP etc), Packet Analysis(Wireshark, TCPDUMP), Intrastructure (router, switches firewall etc)
- Programming and Scripting: Python(for security automation and exploit development), JavaScript(for finding vulnerabilities in web browser and application), SQL(for testing and preventing SQL injection)
- Web Application Security
- Specialized Security Disciplines: cryptography, cloud security, vulnerability assessment, AI and machine learning etc

### Non-Technical Skills
- Ethics and Integrity
- Empathy & Collaboration
- Persistence and Adaptability
- Communication & Reporting
- Ability to learn and adopt new technologies quickly
- Analytical problem solving: When an exploit fails, can you logically deduce why? Is it a firewall, an EDR, or just a syntax error in your code?
- Critical Thinking & Creativity: Thinking Outside the Box: Attackers don't follow rules. You need the creativity to see a system not as it should work, but as it could work if pushed.

# Information Security Threat and Vulnerability Assessment
A threat is the danger waiting to happen.
- Vulnerability: A weakness or hole in your defense (e.g., an unlocked window).
- Threat: The actor or event that can take advantage of that weakness (e.g., a burglar).
- Risk: The probability that the threat will find the vulnerability and cause damage (e.g., the likelihood of a break-in)

### Different Ways for Malware to Enter a System(infection Vector)
- Email attachments & links
- Drive-by downloads
- Infected removable media
- Malicious downloads from untrusted sites: music, movie games, screensaver etc
- Social engineering & phishing
- Malvertising: Online ads with malicious code
- File sharing & P2P platforms
- Software vulnerabilities
- Trojanized Software: Modified legitimate software
- Watering Hole

### Types of Malware
- Viruses
- Worms
- Trojans
- Spyware: Secretly collects data
- Ransomware
- Botnet
- Adware: May not be destructive
- Keylogger
- Rootkit: Provides unauthorized access & control(backdoor). Dificult to detect and remove
- Wiper malware: Delete or destroy data without recovery.






### Vulnerability
In a network, there are generally two main causes for systems being vulnerable: (1) software or hardware misconfiguration and (2) poor programming practices

Common Reasons Behind the Existence of Vulnerability
- Hardware or software misconfiguration
- Insecure or poor design of the network and application
- Inherent technological weakness
- Careless approach of end users

Vulnerability Assessment: Identify weaknesses that could be exploited.
Vulnerability scans can also be performed on applicable compliance templates to assess the organization’s Infrastructure weaknesses against the respective compliance guidelines.
Vulnerabilities scanning tools includes: Nessus, Qualys, GFI LanGuard, and OpenVAS. 

The vulnerability management life cycle is an important process that helps identify and remediate security weaknesses before they can be exploited.
- Identify Assets and Create a Baseline: This phase identifies critical assets and prioritizes them to define the risk based on the criticality and value of each system
- Vulnerability Scan:  Vulnerability scan on the network to identify the known vulnerabilities in the organization’s infrastructure
- Risk Assessment: All serious uncertainties that are associated with the system are assessed and prioritized 
- Remediation: Remediation is the process of applying fixes on vulnerable systems in order to reduce the impact and severity of vulnerabilities
- Verification: The security team performs a re-scan of systems to assess if the required remediation is complete and whether the individual fixes have been applied to the impacted assets
