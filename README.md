# LummaC2-Threat-Analysis-  
This is a threat report based on Rajneesh Gupta's "Detecting and Investigating Malware Traffic". A project that requires the analysis of a PCAP file containing malicious network behavior through deep packet inspections. The report details this inspection, and my application of Wiresharks tools and networking concepts to identify this risk in a simulated Local Area Network.

<h1>Scenario Overview</h1>
The scenario is listed on https://www.malware-traffic-analysis.net/2026/01/31/index.html, but the scenario goes as follows:
<br> <br>
As an analyst at a Security Operations Center (SOC), you check alerts for the past week and find a signature hit for ET MALWARE Lumma Stealer Victim Fingerprinting Activity that triggered on traffic from 153.92.1[.]49 over TCP port 80. The alert triggered on 2026-01-27 at 23:05 UTC.

Using the information, you retrieve a packet capture (pcap) of the traffic from the internal IP address that triggered the alert. Based on the pcap, you write up an incident report, so the incident responders can track down the computer and associated user.

The characteristics of LAN environment:
<br>--LAN segment range:  10.1.21[.]0/24   (10.1.21[.]0 through 10.1.21[.]255)<br>
<br>--Domain:  win11office[.]com<br>
<br>--AD environment name:  WIN11OFFICE<br>
<br>--Active Directory (AD) domain controller:  10.1.21[.]2 - WIN-LU4L24X3UB7<br>
<br>--LAN segment gateway:  10.1.21[.]1<br>
<br>--LAN segment broadcast address:  10.1.21[.]25<br>

<h1>Malware Identification</h1>
According to Microsoft's Security webpage A Lumma Stealer (also known as LummaC2) is a malware as a service (MaaS) offering, that is capable of stealing data from various browsers and applications such as cryptocurrency wallets and installing other malware.
A Lumma Stealer has several delivery techniques to deceive authenticated users through Phising attempts and Maladvertising. Allowing for the rapid exfiltration of user information like credentials, and browser cookies.

They can typically hide as background processes. Communicating back and forth with their C2 server via https tcp communication which can be sniffed. Through stealing information in temporary files, they are actively trying to escalate their priviledges within a victim's network. 
This spread can be seen through an increase in communication with the AD of a LAN segment in order to try out their new stolen passwords. A place that stores security groups based on authorizations of users, and logon information.

<h1>Methodology</h1>
Based on that description, the isolation of this malware's behavior can be done by looking out for these patterns and applying the relevant filters:
  
<br>Common http requests to look for based on keylogger's communication with its C2 servers : http.request.method  == "GET"  and http.reques.method == "POST".
(Showed off the process of information beting sent and receive from the C2 server.)<br>
 
<br>ip.addr == (infected machine's IP)
(Helped to pinpoint the ip that can allow me to follow the tcp conversation between this infected machine and the C2 server.)<br> 

<h1>Detailed Findings</h1>
In the scenario it was disclosed that the Activity triggered on traffic from 153.92.1[.]49, over TCP port 80.
<br>
<img src = "https://i.imgur.com/61yx5Db.png">
<br>
153.92.1.49 traces back to whitepeppr.su (.su being a country code used by post soviet union countries and is an active web domain).<br>
As shown this domain and communicates with LAN Device 10.1.21.58:
<br>
<img src = "https://i.imgur.com/YedBsC9.png">

<br> Located the infected computer's MAC address which confirmes if the physcial computer itself was compromised. It also helped with the pinpointing of the infected machine's movements across the network. As the infostealer actively communicated with the network's Domain Controller to perform user profiling and later on actual authentication so it could access Domain resources. 
<br>
<img src = "https://i.imgur.com/0s1Clef.png">
<br><br>
Infected host device name communicating with AD:<br>
<img src = "https://i.imgur.com/U87zeG7.png">
<br> In this photo the infostealer is clearly seen communicating with CLDAP protocols to ping the domain controller to find information about the environment it is in. Finding specifc information like the DomainGuid.<br>
<br><br>
<img src = "https://i.imgur.com/jsFQXM3.png"> 
<br>
Infected host authenticating its credentials (AS-REQ)  with the AD/DC. Client name (Cname) under gwyatt. It then makes a (TGS-REQ) to request a service ticket for a specific resource within the domain. (Its behaviors always tie back to credential harvesting through process injections.)
<br><br>
<img src = "https://i.imgur.com/LmWcnAG.png">













