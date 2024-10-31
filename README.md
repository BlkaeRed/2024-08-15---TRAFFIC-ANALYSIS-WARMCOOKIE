"# 2024-08-15---TRAFFIC-ANALYSIS-WARMCOOKIE" 

SCENARIO

LAN segment details:


LAN segment range:  10.8.15[.]0/24 (10.8.15[.]0 through 10.8.15[.]255)

Domain:  lafontainebleu[.]org

Active Directory (AD) domain controller:  10.8.15[.]4 - WIN-JEGJIX7Q9RS

AD environment name:  LAFONTAINBLEU

LAN segment gateway:  10.8.15[.]1

LAN segment broadcast address:  10.8.15[.]255


TASK

Write an incident report based on malicious network activity from the pcap and from the alerts.

The incident report should contains 3 sections:

Executive Summary: State in simple, direct terms what happened (when, who, what).

Victim Details: Details of the victim (hostname, IP address, MAC address, Windows user account name).

Indicators of Compromise (IOCs): IP addresses, domains and URLs associated with the activity.  SHA256 hashes if any malware binaries can be extracted from the pcap.


Initially, I researched the "WarmCookie" malware. I found useful information on the any.run site, where I learned about important IOCs (Indicators of Compromise) related to the malware, such as IP addresses and domains, and how the malware operates as a two-stage backdoor. This led me to focus on looking for signs of the initial infection, likely through phishing, and unauthorized remote access. I also kept an eye out for the download of the WarmCookie DLL from a specified URL, as mentioned in the alerts section.


Next, before examining the pcap file, I analyzed the alerts section. As previously noted, the DLL download was visible there. More importantly, I identified the IP addresses of both the host in our LAN (10.8.15.133, likely the victim) and the IP outside the LAN (72.5.43.29, likely the attacker). There were also numerous alerts related to traffic between these two IPs. Armed with this information, I started analyzing the pcap file.


As expected, I saw a lot of suspicious traffic to and from IP address 72.5.43.29, where a file was being downloaded. Using the “follow” option on the TCP stream, I noticed the “MZ” header, indicating the file was either an .exe or .dll. Given the behavior of the WarmCookie malware, it was likely the DLL that the malware downloads.


My next step was to determine how the malware initially reached the host. I searched for potential phishing attempts that occurred before the first instance of traffic to or from 72.5.43.29. I found one suspicious domain, quote.checkfedexexp.com, and using the “follow” option, I saw that this domain downloaded an “Invoice 876597035_003.zip” file, which seemed like a phishing attempt. Upon deeper analysis, I found a .js file inside it, which led me to another suspicious domain that downloaded the malicious file: business.checkfedexexp.com. Unfortunately, I was unable to retrieve the file for further inspection.


Executive Summary: 

On 2024-08-15 at around 00:11 UTC user Pierce Lucero fell victim to phishing attack and his computer was infected with WarmCookie malware
_________________________________________________________________________________________________________________________________________
Victim Details:

Hostname: DESKTOP-H8ALZBV

Ip address: 10.8.15.133

Mac address: 00:1c:bf:03:54:82

Windows user account name: plucero
_________________________________________________________________________________________________________________________________________

Indicators of Compromise (IOCs):

Suspicious Ip address and URL: 

104.21.55.70/quote.checkfedexexp.com

72.5.43.29

File: “Invoice 876597035_003.zip” SHA256: 798563fcf7600f7ef1a35996291a9dfb5f9902733404dd499e2e736ea1dc6fc5

File: ” Invoice-876597035-003-8331775-8334138.js” SHA256: dab98819d1d7677a60f5d06be210d45b74ae5fd8cf0c24ec1b3766e25ce6dc2c

File: Downloaded DLL: SHA256: b7aec5f73d2a6bbd8cd920edb4760e2edadc98c3a45bf4fa994d47ca9cbd02f6



