Network Overview

    My device is 192.168.0.61 (ifconfig), target net is 192.168.20.0/24.

    With nmap -sS 192.168.20.0/24 , I got back .1 (gateway which is excluded), .29 and .48.

Windows Machine 1 (192.168.20.48)

Phase 1

    With 48 there were 12 ports:

        21 (ftp) Microsoft ftp

        23 (telnet) Microsoft Windows XP telnetd

        135 (msrpc) Microsoft Windows RPC

        139 (netbios-ssn) MWS netbios-ssn

        445 (Microsoft-ds) MWS Server 2008 R2 – 2012 microsoft-ds

        49152 - 53, 55, 56, 57 and 59 all unknown MWS RPC.

    This information tells me that it’s a highly outdated windows machine. Firstly, telnet sends data in cleartext which is a big misconfiguration. The FTP is similiar to the linux box. The 139 and 445 are SMB ports running windows server 2008 which I already known contains the EternalBlue vulnerability (MS17-010). If its not patched I may be able to get admin privileges within a single exploit.

Phase 2

    Next ive loaded up Nessus to start a vulnerability scan to gather more information about the ports and services. I will run a basic network scan.

    For the .48 device there was the ftp vulnerability of cleartext transmitted data and DCE services enumeration. After the Nessus scan the state changed and moved onto .50 and to .93 a few hours later.

    No other vulnerabilities were found from this, however the windows machine is very outdated so im going to have to figure them out myself or run some more nmap scans.

    As this wasn’t very useful and didn’t give me anything I already hadn’t found, I wanted to use nmap to its fullest and run the nmap-vulner scan on the open ports to list all the vulnerabilities that they have, however I know that it could be quite noisy so my best bet was to try and exploit the vulnerabilities that I have already found.

    Before this though, I will use Metasploit to do some more enumeration to scan the windows device and see if it does contain the eternal blue vulnerability.

    I ran the scanner for eternal blue and it came back positive that it is likely vulnerable to it.

    Next one I checked was the SMB users which will attempt to pull a list of all user accounts on the machine over the network. Another tool for this is enum4linux but im “Living off the land” and sticking with Metasploit. I didn’t get anything back from this. Next ill try the smb shares to list the file shares available on the network.. I got an access denied for this one.

    Next is to check the telnet port to see what legacy version its running. I got a “TELNET Welcome to Microsoft telnet service login:” appear.

Phase 3

    Starting with the most dangerous exploit, eternal blue, I will try and get full control over the windows machine. I ran the exploit and got full access with the highest system privilege (NT AUTHORITY/SYSTEM). I checked the pid and migrated the shell inside of safer, more permanent system process.

    Next I wanted to steal all of the hashes for the user passwords from the Windows SAM registry using hasdump. I gathered the hashes for admin, 3 employees and 1 guest account. I copied them into a file and tried to crack them with john the ripper . I was able to get employee2 and employee15s passwords (house and shark) .


Phase 4

    I already got the root admin privilege, gathered the hashes and cracked 2 passwords. Now I want to check I can access these accounts via the smbclient tool. I used the password the display the list of shares that employee2 can access and then accessed the users share and the employee2 folder, with full access to all their documents.

    As ive got full access over the machine, I thought id use a local_exploit_suggester to check the internal patch level against known vulnerabilities. I found 12 different exploits from this scan for privilege escalation.  As ive already got root already I thought id just list a few: Some High / Critical ones that were found include CVE-2020-1054, CVE 2021-40449, and CVE_2019_1458 (Win32k Elevation of Privilege Vulnerabiltiy), CVE-2020-0787 (Windows Background Intelligent Transfer Service Elevation of Privilege Vulnerability).

    Next I tried to access the same account via telnet. I was able to login directly to the account.