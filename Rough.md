#### 1. Cyber security (Importance of information protection and the evolution of information security)
#### 2.List NIST Methods and objectives ()
#### 3.Explain different types of threats and attack vectors
#### 4. Discuss the principles of secure socket layer(SSL) and its roles in online security
#### 5. Short note on Email-Worms, Trojan,  Zombies
#### 6. Short note on SQL Injection, Buffer Overflow, Arp Poisoning, Spoofing
#### 7.  Explain authentication and authorization also write a note on Single, Two and MultiFactor Authentication
#### 8. Explain COBIT and ISO 27000 Series security standards
#### 9. Explain importance of Security awareness program 
#### 10. With neat diagram explain Lollipop and Onion Model

#### 11. Write a note on security council 
- ##### HIPPA, GDPR , ISO

### Threats
- ##### Potential to harm Organization
##### Threats
  - ###### SQL Injection
  - ###### XSS
  - ###### MITM
  - ##### Bruteforce
  - ##### Phishing
  - ##### Dos and DDos 
### Types of attacks
#### Malicious Mobile Code
- Trojans - as Legitimate Attacks
- Worms
- Ransomware
- Browser Exploits
#### Apt (Advanced Persistent Threat)
#### Manual Attacks
- Social Engineering
- Credential Stuffing
- Brute-Force

##### COBIT (Control Objectives for Information and RElated Technologies)
#### ISO 27000 series
- ###### 27001: Requirements for an ISMS
- ###### 27002: Guidelines for security controls
- ###### 27005: Focuses on risk management
##### NIST (National Institute of Standards and Technology)
##### GDPR (General Data Protectioon Regulation)
##### HIPPA (Health Insurance Portability and Accountability Act)




#### Lollipop Model
###### Key Characteristics
- single layer
- strong perimeter but once attacker gets inside internal security is weak
- Risk of complete compromise

#### Onion Model
###### Key Characteristics
-  Multi Layered approach
- Security increases as you go deep
- Zero trust approach
#### Zones of trust
- Public: Website Home page
- DMZ: Payment Processing Server
- Internal: Customer account data
- Restricted: Credit card details

#### Best practices for Network Defense
- Secure Physical defense
- Harden OS 
- Keeping Patches updated
- Antivirus  
- Firewall
	-  Network based
	- Host based (on Individual devices)
- Secure Network Share Permission
- Use encryption
- Secure applications
- Backup System
    - 3-2-1 Backup
- Implement ARP poisoning Defenses
    - MITM 
    - Arp Poisoning is also known as arp spoofing
    - It is type of MITM attack where an attacker sends falsified arp messages over a local network to link mac address with ip address of another device
    -  This allows attacker to intercept, modify, and disturb network traffic
    - Working Procedure

| Normal ARP Process                                                                   | Spoofed ARP Process                                                                                       |
| ------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- |
| Devices on the network use ARP to resolve IP addresses to MAC addresses.             | Attacker sends fake ARP replies associating their MAC address with the IP address of a legitimate device. |
| When a device needs to communicate, it sends ARP requests asking, "Who has this IP?" | The victim unknowingly updates its ARP table with the attacker's MAC address.                             |
| The target device responds with its MAC address.                                     | The victim sends traffic meant for the legitimate device to the attacker instead.                         |

## CISO
- CISO is senior Executive responsible for establishing and maintaning Oraganizations Information security statergy
- CISO ensures data security, manage cyber risk and alligns security initiates with business goals 


