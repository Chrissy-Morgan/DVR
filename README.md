# DVR Security Evolution

# Introduction
A review has been undertaken on devices which are available online for purchase at consumer sites such as Ebay and Amazon. 
This is to see if any improvements have been made in recent years as research and exploits have been made public within this area. Where possible conduct comparisons between models and firmwares. It is hoped to provide a methodology for researchers to test against or to learn from if new to DVR hacking. Results will be published by January 2021, if you have research and would like to contribute to the results listed please contact [Chrissy Morgan](https://twitter.com/5w0rdFish).

# Notable Mentions
Notable resources to mention of which this research would not have been possible is the work undertaken by Istvan Toth Github POC’s [Tothi, 2017](https://github.com/tothi/pwn-hisilicon-dvr) who undertook research upon many CCTV DVR's firmwares to discover multiple vulnerabilities. 
[Vlad](https://twitter.com/snawoot) who has uncovered further zero days within devices ([Habr, 2020](https://habr.com/en/post/486856/)) and [CyberGibbons](https://twitter.com/cybergibbons)  who has done extensive work over the years on DVR hacking and has produced great Hardware walkthroughs ([CyberGibbons.com, 2020](https://cybergibbons.com/hardware-hacking/identifying-security-relevant-components-in-a-dvr/))

# Overview
It has been observed that there are multiple vulnerabilities in CCTV DVR’s purchased by consumers and that these vulnerabilities are indeed exploited in the wild. These can either be directly accessed by viewers via websites such as Insecam ([Information Security Magazine](https://www.infosecurity-magazine.com/news/uk-security-cameras-risk-hacking/), 2020) or have been known to be actively exploited to be part of botnets ([threatpost](https://threatpost.com/hackers-exploited-0-day-cctv-camera/154051/), 2020).
Consumers may find it particularly hard to purchase a “secure device” currently for home or business use, as there is little to no indication without undertaking their own research if that device is insecure or not. 

Services such as Which ([Which](https://www.which.co.uk/news/2019/10/the-cheap-security-cameras-inviting-hackers-into-your-home/), 2019) release reports advising consumers, but there are literally thousands of different types, models and brands on the open market, sold online at places such as Amazon which can make it difficult for consumers. In addition to this, many of these will use generic boards, firmwares and chips which may be vulnerable and there is not much way of checking prior to purchasing the item. Initiatives exists to improve IoT security with projects such as the OWASP IOT, 
([Code of Practices for Consumer IoT ](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/773867/Code_of_Practice_for_Consumer_IoT_Security_October_2018.pdf)) and now more recently European standardisation which provides a baseline requirement for the Internet of things ([ETSI EN 303 645]( https://www.etsi.org/deliver/etsi_en/303600_303699/303645/02.01.01_60/en_303645v020101p.pdf))


One piece of information which can be seen when purchasing an IoT device online is its release date or  “Date First Available”. It is anticipated that newer devices would be sold with additional security features or updates included, but this is an assumption. It may take some time before manufacturers adopt the standards and best practice, and where international suppliers are concerned, these may not be adopted at all. 

This is currently a small sample study, however a collection of sample firmwares has been collected for future research and comparison.
As research continues, further products will be listed in the results tables and updated with findings. 
It is hoped that this research may give some pointers to others who are interested in IoT, as DVRs give a good surface area to explore. In addition to this there are walkthroughs documenting the steps taken to improve the researchers skills and provide repeatable steps. 

# Materials & Methods
The below lists the devices and the vulnerabilities which have been tested.  
The known exploits have been mapped to the [OWASP IOT top ten](https://owasp.org/www-project-internet-of-things/) as part of this research and provide a framework to test against.

| OWASP IOT Top Ten | Vulnerability | Tools | Tutorial | Method |
| --- | --- | --- | --- | --- |
| I7 Insecure Data Transfer and Storage | Insecure Transmission | Wireshark | Link | Network Testing |
| I1 Weak, Guessable, or Hardcoded Passwords | Insecure Guest Access URL | Browser | Link | Web App Manual Testing |
| I1 Weak, Guessable, or Hardcoded Passwords | Insecure Guest Access Login | Browser | Link | Web App Manual Testing |
| I3 Insecure Ecosystem Interfaces | Insecure blank password Access | Browser | Link | Web App Manual Testing |
| I5 Use of Insecure or Outdated Components | Directory Traversal | Burpsuite | ([Link](https://github.com/Chrissy-Morgan/DVR/blob/master/4.%20Tutorial/Directory-Traversal.pdf)) | Web App Manual Testing |
| I1 Weak, Guessable, or Hardcoded Passwords | Root credentials | HashCat | Link | Web App Manual Testing |
| I9 Insecure Default Settings | Root Telnet | Telnet | Link | Network Testing |
| I1 Weak, Guessable, or Hardcoded Passwords | RTSP Camera access with credentials | RTSP | Link | Network Testing |
| I2 Insecure Network Services | Remote Back Door | Custom Script | Link | Network Testing |
| I10 Lack of Physical Hardening | Firware Extraction via SPI | CH341a & SPI Clip | Link | Hardware Pentesting |
| I10 Lack of Physical Hardening | U-Boot access via UART | FTDI & Minicom | Link | Hardware Pentesting |

# Methodology
The below covers a high-level overview of the methodology undertaken to exploit the listed vulnerabilities. Further details on each vulnerability can be found within the Results and Observation section. 

## Reconnaissance & Investigation

Manufacturer manuals were search for online, these sometimes have default credentials or engineering details which are of use in terms of exploitation. Data Sheets were gathered for the model of the System On Chip and EEPROM. Schematics were obtained for the main DVR boards. From this it is possible to understand if there are any insecure default configurations and the hardware attack surface. Where the data sheets could not be obtained for that exact version, the nearest version was collected. It was possible to gather intelligence on the specific version by comparing and contrasting details between versions. Some documents of interest can be found within Resources/Schematics.

A review of existing research and CVE's was undertaken and from this a list of vulnerabilities was achieved. This is tabled in the next section. The links to research reviewed is within the [Notes](https://github.com/Chrissy-Morgan/DVR/tree/master/1.%20Notes) area. 

Notable resources to mention of which this research would not have been possible is the work undertaken by Istvan Toth Github POC’s ([Tothi, 2017)](https://github.com/tothi/pwn-hisilicon-dvr) who undertook research upon many CCTV DVR's firmwares to discover multiple vulnerabilities.

## Web App Manual Testing

A survey of the web application panel was undertaken. This can normally be found at [http://192.168.1.10](http://192.168.1.10). Login prompts were manually tested for the insecure guest, default and admin access as well as the directory traversal CVE, which are all known issues.
There is a wide range of default credentials listed online that could be used to brute force against a device. 

Burpsuite and various browsers were used to test for issues, as the web panel behaved differently in each. For example, Microsoft Edge was highlighted to be too modern to use and the user would need to use an older version browser. Also, Internet explorer was advised to be the browser of choice due to the Active X plugin required for the web app to work appropriately. This plugin would be downloaded from an external source in China.

Please see [Web Application Testing Walkthroughs](https://github.com/Chrissy-Morgan/DVR/tree/master/4.%20Tutorial/Web__Walk-through) for further details on how the web app manual testing was undertaken.

## Network Testing

Network Testing was achieved by  a combination of very basic methods. Running NMAP  to note all the TCP/UDP ports which were open on the device. From this we could ascertain any vulnerable services and possible connection routes.  From there on in, manual probing was undertaken, using Netcat and direct connections to the ports.

A custom script which had been made by Vladislav Yarmak was used against the device to see if it would enable the Telnet access.  This script targets a possible backdoor/ “administration” port which then enables port 23 to be open. Not all devices will have port 23 open on them as default, so this script would need to be used to enable remote access.

RTSP is used within CCTV camera systems to enable the viewing of the device remotely, a direct connection to RTSP could be made with default / hardcoded credentials, soa script using cvlc within a ubuntu virtual machine was used to test the credentials and pull up the camera feed.

Please see [Network Testing Walkthrough](https://github.com/Chrissy-Morgan/DVR/tree/master/4.%20Tutorial/Network_Walk-through) for further details on how network testing was undertaken.

## Hardware Penetration Testing

By using the CH341A programmer it may be possible to make a direct SPI connection to the device to extract firmware. This is achieved by connecting the SPI adapter to the serial flash memory and reading the firmware directly from the device. In some circumstances there can be difficulties with this, such as an unsupported  version or a clean read being obstructed by the MCU.

By using a FTDI USB to UART adapter it may be possible to interface with the U-Boot process and gain shell access. Many of the boards may place the UART connections in varying places, in addition to this the pins may be soldered so no connection is easily accessible. In this case de-soldering the chip from the board may be one method, or connecting jumper wires directly. 

The correct connections were identified by obtaining schematics which document the similar generic board layouts, some of which use the same naming convention for the pin outs. By process of elimination reviewing many of the different board schematics it was possible to know which were the correct connections. 

Please see [Hardware Testing Walkthrough](https://github.com/Chrissy-Morgan/DVR/tree/master/4.%20Tutorial/Hardware_Walk-through) for further details on how the hardware testing was undertaken.

* * *

# Results & Observations

* * *

**I1 Weak, Guessable, or Hardcoded Passwords**  
Use of easily brute forced, publicly available, or unchangeable credentials, including backdoors in firmware or client software that grants unauthorized access to deployed systems.

**Vulnerabilities:**

- **Insecure Guest Access URL**  
    Access to the guest account could be achieved by  entering 192.168.1.10\\DVR.htm  
    This would give an overview of the system (without camera view) but leaked sufficient information which could be used to gain a foothold. Information such as the firmware version via the web app console. This made it possible to search for firmware online. 
    
- **Insecure Guest Access Login**  
    Access to the Guest account could be gained with default credentials. This could be achieved by entering the username Default and blank password. This would once again give an overview of the system but without camera viewing.
    

**Results:**

| Device | Year | Firmware Version / Board | Insecure Guest Access URL | Insecure Guest Access Login |
| --- | --- | --- | --- | --- |
|SecuLink |02/2017  |Unknown / Board - MBD6804T-EL  | Yes   | Yes   |
| Kare | —   |  |  | —   |
| Floureon | —   | —   | —   | —   |
| AVSonic |     |     |     |     |

**Observations:**

* * *

**I2 Insecure Network Services**  
Unneeded or insecure network services running on the device itself, especially those exposed to the internet, that compromise the confidentiality, integrity/authenticity, or availability of information or allow unauthorized remote control.

**Vulnerabilities:**

**\- Remote Back Door**


**Results:**

| Device | Year | Firmware Version | Remote Back Door |     |
| --- | --- | --- | --- | --- |
|SecuLink |02/2017  |Unknown / Board - MBD6804T-EL  | Yes   | Yes   |
| Kare | —   | - |-  | —   |
| Floureon | —   | —   | —   | —   |
| AVSonic |     |     |     |     |

**Observations:**
* * *

**I3 Insecure Ecosystem Interfaces**  
Insecure web, backend API, cloud, or mobile interfaces in the ecosystem outside of the device that allows compromise of the device or its related components. Common issues include a lack of authentication/authorization, lacking or weak encryption, and a lack of input and output filtering.

**Vulnerabilities:**

**\- Insecure blank password Access**

**Results:**

| Device | Year | Firmware Version | Insecure Blank Password |     |
| --- | --- | --- | --- | --- |
|SecuLink |02/2017  |Unknown / Board - MBD6804T-EL  | Yes   | Yes   |
| Kare | —   |  |  | —   |
| Floureon | —   | —   | —   | —   |
| AVSonic |     |     |     |     |


**Observations:**

* * *

I4 Lack of Secure Update Mechanism - TBC  
Lack of ability to securely update the device. This includes lack of firmware validation on device, lack of secure delivery (un-encrypted in transit), lack of anti-rollback mechanisms, and lack of notifications of security changes due to updates.

**Vulnerabilities:**  
\*\*- \*\*

**Results:**

| Device | Year | Firmware Version |     |     |
| --- | --- | --- | --- | --- |
|SecuLink |02/2017  |Unknown / Board - MBD6804T-EL  | Yes   | Yes   |
| Kare | —   |  |  | —   |
| Floureon | —   | —   | —   | —   |
| AVSonic |     |     |     |     |


**Observations:**

* * *

**I5 Use of Insecure or Outdated Components**  
Use of deprecated or insecure software components/libraries that could allow the device to be compromised. This includes insecure customization of operating system platforms, and the use of third-party software or hardware components from a compromised supply chain

**Vulnerabilities:**

**\- Directory Traversal**

**Results:**

| Device | Year | Firmware Version | Directory Traversal |     |
| --- | --- | --- | --- | --- |
|SecuLink |02/2017  |Unknown / Board - MBD6804T-EL  | Yes   | Yes   |
| Kare | —   |  |  | —   |
| Floureon | —   | —   | —   | —   |
| AVSonic |     |     |     |     |


**Observations:**

* * *

I6 Insufficient Privacy Protection --TBC  
User’s personal information stored on the device or in the ecosystem that is used insecurely, improperly, or without permission.

**Vulnerabilities:**  
\*\*- \*\*

**Results:**

| Device | Year | Firmware Version |     |     |
| --- | --- | --- | --- | --- |
|SecuLink |02/2017  |Unknown / Board - MBD6804T-EL  | Yes   | Yes   |
| Kare | —   |  |  | —   |
| Floureon | —   | —   | —   | —   |
| AVSonic |     |     |     |     |


**Observations:**

* * *

**I7 Insecure Data Transfer and Storage**  
Lack of encryption or access control of sensitive data anywhere within the ecosystem, including at rest, in transit, or during processing

**Vulnerabilities:**

**\- Insecure Transmission**

Whilst testing of the web panel was undertaken, Wireshark would be running in the background to log information sent over the network, to and from the device. It can be seen that the protocol being used is HTTP, therefore details can be sent over plaintext upon the network.

**Results:**

| Device | Year | Firmware Version | Insecure Transmission |     |
| --- | --- | --- | --- | --- |
|SecuLink |02/2017  |Unknown / Board - MBD6804T-EL  | Yes   | Yes   |
| Kare | —   |  |  | —   |
| Floureon | —   | —   | —   | —   |
| AVSonic |     |     |     |     |


**Observations:**

* * *

I8 Lack of Device Management  --TBC

Lack of security support on devices deployed in production, including asset management, update management, secure decommissioning, systems monitoring, and response capabilities.

**Vulnerabilities:**  
\*\*- \*\*

**Results:**

| Device | Year | Firmware Version |     |     |
| --- | --- | --- | --- | --- |
|SecuLink |02/2017  |Unknown / Board - MBD6804T-EL  | Yes   | Yes   |
| Kare | —   |  |  | —   |
| Floureon | —   | —   | —   | —   |
| AVSonic |     |     |     |     |


**Observations:**

* * *

**I9 Insecure Default Settings**  
Devices or systems shipped with insecure default settings or lack the ability to make the system more secure by restricting operators from modifying configurations.

**Vulnerabilities:**

**\- Root Telnet**

**Results:**

| Device | Year | Firmware Version | Root Telnet |     |
| --- | --- | --- | --- | --- |
|SecuLink |02/2017  |Unknown / Board - MBD6804T-EL  | Yes   | Yes   |
| Kare | —   |  |  | —   |
| Floureon | —   | —   | —   | —   |
| AVSonic |     |     |     |     |


**Observations:**

* * *

**I10 Lack of Physical Hardening**  
Lack of physical hardening measures, allowing potential attackers to gain sensitive information that can help in a future remote attack or take local control of the device.

**Vulnerabilities:**

**\- U-Boot Shell access via UART**

**\- Firmware Extraction via SPI**

**\- Reverse Engineering of Embedded System.**

**Results:**


| Device | Year | Firmware Version | U-Boot Shell access via UART | Firmware Extraction via SPI | Reverse Engineering of Embedded System |
| --- | --- | --- | --- | --- | --- |
|SecuLink |02/2017  |Unknown / Board - MBD6804T-EL  | Yes   | Yes   |
| Kare | —   |  |  | —   |
| Floureon | —   | —   | —   | —   |
| AVSonic |     |     |     |     |

**Observations:**

* * *

References  
Spring, T
Hackers Actively Exploit 0-Day in CCTV Camera Hardware (2020). 
Available at: [https://threatpost.com/hackers-exploited-0-day-cctv-camera/154051/](https://threatpost.com/hackers-exploited-0-day-cctv-camera/154051/) 
(Accessed: 29 June 2020).

Coker, J. (2020) Over 100,000 UK Security Cameras Could Be at Risk of Hacking, Infosecurity Magazine. 
Available at: https://www.infosecurity-magazine.com/news/uk-security-cameras-risk-hacking/ 
(Accessed: 30 June 2020).
