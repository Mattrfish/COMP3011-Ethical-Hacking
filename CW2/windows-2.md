Windows Machine 2 192.168.20.24 (at the time of the first nmap scan)

Phase 1

A directed scan to this ip just gave me that there is Microsfot Terminal Services running on port 3389. Ill use this to run a nessus scan next.

Phase 2

I had already done a lookup on this service (RDP) whihc says that there is a MS12-020 exploit. 

When running a nessus scan i had to turn ping off as it was not showing on ping and only via arp. However i did not seem to get anything with it at all.
