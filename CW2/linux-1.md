Linux Machine 1 (192.168.20.29)

Phase 1

    Starting with .29 there were 3 ports open, with -sV scan I got some details on the ports:

        21 (ftp) vsftpd 3.0.5

        22 (ssh) OpenSSH 10.0p2 Debian 7 (protocol 2.0)

        80 (http) Apache httpd 2.4.66 ((Debian))

    This information tells me that this machine is a linux web server as its using Debian linux. The SSH version is very modern so I would need to find leaked credentials in order to access through this port. The version of FTP doesn’t have major known remote exploits but if anonymous login is enabled it could allow me to read or write files. The web server running on port 80 will definitely be the primary target as it could be vulnerable.

Phase 2

    For the .29 the ftp has 2 vulerabilities: anonymous login and cleartext, common platform enumeration, and web server directory enumeration.

    Next was to scan the linux box. First I checked the anonymous ftp login which Nessus saud was there. I got “Anonymous READ” for this confirming the vulnerability.

    Next was to scan the web server for any hidden folders. This is a potential place to also use lfi and rdfi in future. This gave back a few directories, but the main standout was .htpasswd and .htaccess. There was also program files and documents as well which is unusual for a linux machine and therefore could be useful. With the FTP anonymous access, it means I could potentially download the .htpasswd file.

    Finally was to check the http version to find basic misconfigurations. This confirmed it is Apache/2.4.66 (Debian).

Phase 3

    Now its time to move onto exploiting these vulnerabilities.

    Next I will exploit the linux machine and hopefully from here ill be able to laterally move to the other hidden machines within the network. I want to check the low hanging fruit and see if I can use the credentials found from the windows machine to access this one via SSH. I tried the employee2 credenitals but it did not work.

    Instead I typed the ip into the address bar and loaded up a default web page. As I got access to this the first thing that came ot mind was path traversal. I tried some techniques but got nothing. As I got nothing, I though it would be smarter to use gobuster to find the directories it holds and maybe be able to do it from there. I found ./htaccess and htpasswd as well which I had earlier found earlier from the vuln assessment. I tried to access ./htpasswd but I don’t have permission. I thought it would be good to search some vulnerabilities and exploits for this server version but didn’t get much as it’s a recent server.

    I think the best bet is to try ftp anonymous login. I had issues with the ftp command itself so I used netcat listening ro directly access the port. I used anonymous login and it was successful but didn’t get much.

    At this point I was stuck so thought about using Armitage to scan the target again but didn’t get anything else. I used wget to see if I could get the htpasswd file using ftp. I got access but no such file.

    I seemed to have no way into the linux machine currently and I did find some other ips which I will try to access.

    I took some time out and managed to get lftp to work and got access to the linux web file server. I realised that there was absolutely nothing in the server. However, I had the realisation that I could upload a reverse shell script and gain access via that. First, I needed to see if file uploads work and I need to make sure that its connected to the public server folder. I got a 550 permission denied when trying to upload a file.

    Phase 4


    Phase 5