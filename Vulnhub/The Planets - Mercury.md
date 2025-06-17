
I have Kali running alongside with Mercury VM.


The first thing to try was
```bash
sudo netdiscover
```
to view the Mercury VM IP address.
Kali ip - 192.168.56.101
Mercury - 192.168.56.103

After that I ran nmap scan on the mercury vm
```bash
┌──(kali㉿kali)-[~]
└─$ nmap 192.168.56.103
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-17 06:57 EDT
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for 192.168.56.103
Host is up (0.0019s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
8080/tcp open  http-proxy
MAC Address: 08:00:27:73:19:07 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 0.45 seconds

```
![[Pasted image 20250617140135.png]]

