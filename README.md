# LummaC2-Threat-Analysis-  
This is a threat report based on Rajneesh Gupta's "Detecting and Investigating Malware Traffic". A project that requires the analysis of a PCAP file containing malicious network behavior through deep packet inspections. The report details this inspection, and my application of Wiresharks tools and networking concepts to identify this risk in a simulated Local Area Network

<h1>Scenario Overview</h1>
The scenario is listed on https://www.malware-traffic-analysis.net/2026/01/31/index.html, but the scenario goes as follows:

As an analyst at a Security Operations Center (SOC), you check alerts for the past week and find a signature hit for ET MALWARE Lumma Stealer Victim Fingerprinting Activity that triggered on traffic from 153.92.1[.]49 over TCP port 80. The alert triggered on 2026-01-27 at 23:05 UTC.

Using the information, you retrieve a packet capture (pcap) of the traffic from the internal IP address that triggered the alert. Based on the pcap, you write up an incident report, so the incident responders can track down the computer and associated user.

The characteristics of my environment:

--LAN segment range:  10.1.21[.]0/24   (10.1.21[.]0 through 10.1.21[.]255)
--Domain:  win11office[.]com
--AD environment name:  WIN11OFFICE
--Active Directory (AD) domain controller:  10.1.21[.]2 - WIN-LU4L24X3UB7
--LAN segment gateway:  10.1.21[.]1
--LAN segment broadcast address:  10.1.21[.]25

<h1>Malware Identification</h1>
According to Microsoft's Security webpage A Lumma Stealer (also known as LummaC2) is a malware as a service (MaaS) offering, that is capable of stealing data from various browsers and applications such as cryptocurrency wallets and installing other malware.
A Lumma Stealer has several delivery technique whihch server to deceive authenticated users through Phising attempts and Maladvertising.

They can typically hide as background processes. Communicating back and forth with their C2 server via https tcp communication which can be sniffed. Through stealing information in temporary files, they are actively trying to begin escalating their priviledges within the networking. 
This spread can be seen through an increase in communication with the AD of a LAN segment in order to try out their new stolen passwords. A place that stores security groups based on authorizations of users, and logon information.

<h1>Methodology</h1>

<br>Common http requests to look for based on keylogger's behavior : http.request.method  == "GET"  and http.reques.method == "POST".<br>
<br>Shows off the process of information beting sent and receive from the C2 server.<br>
<br>ip.addr == (infected machine's IP)<br>
<br>Helps to pinpoint the ip that can allow me to follow the tcp conversation between this infected machine and the C2 server.<br>

<h1></h1>






