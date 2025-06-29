https://app.hackthebox.com/machines/Dog

![](../../Attachments/Pasted%20image%2020250629185646.png)


# Scope 
Target IP: 10.10.11.58

# Port Enumeration
I started with nmap scan and the goal is to find user and root flag.
```bash
sudo nmap -sV -p- 10.10.11.58     
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-29 12:30 EDT
Nmap scan report for 10.10.11.58
Host is up (0.044s latency).
Not shown: 65482 closed tcp ports (reset), 51 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 52.00 second
```

From the scan we saw port 22 and 80 opened. When I opened the web application there was a dog blog:

![](../../Attachments/Pasted%20image%2020250629193913.png)

And just from looking at the first blog post we had a possible username: `dogBackDropSystem`
