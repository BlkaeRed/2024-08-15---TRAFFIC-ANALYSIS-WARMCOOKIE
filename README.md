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

At first I researched “WarmCookie” malware. I found any.run site and there I learned important IOCs related to the malware like ip addresses and domains and also how the malware works and the fact that it is two-stage backdoor malware. So I knew that I should be looking for initial infection, most likely by phishing, then signs of unauthorized remote access . I should also be looking for download of WarmCookie DLL form specified URL which was already present in alerts section. Next before looking at pcap file I analized aforementioned alerts section. Like I previously wrote DLL download could be seen there, but maybe more importantly I could see Ip of both host in our LAN (10.8.15.133, most likely victim) and Ip outside of the LAN (72.5.43.29, most likely attacker). There was also a lot more alerts related to traffic between both of them. So with all this preparation I started analysing pcap file. As I expected I saw a lot of suspicious traffic for and to ip 72.5.43.29 where I could see file being downloaded. By using “follow” option on TCP stream I could see “MZ” written in front which means that the file was either .exe of .dll and knowing how “WarmCookie” malware works it is probably the dll that the malware downloads. Before doing anything else I wanted to see how did the malware make it to the host. So I started searching for potential phishing attempt that happened before first instance of traffic to or from 72.5.43.29. I found one suspicious domain quote.checkfedexexp.com and by again using “follow” option I saw that this domain downloads “Invoice 876597035_003.zip” file which is potential phishing attempt. By analysing the file deeper, I found .js file instide of it, and by analysing that .js file I fould another weird domain that download malicious file business.checkfedexexp.com, but I was not able to get a hold of the file for further inspection. 

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



