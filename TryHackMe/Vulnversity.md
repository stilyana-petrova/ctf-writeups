
# Scope
The target machine was assigned the IP address `10.10.180.100`.
# Port Scanning

To identify open ports and running services, I conducted a service/version detection scan using `nmap`:
```bash
sudo nmap -sV 10.10.180.100
```
![[Pasted image 20250618160407.png]]

The scan revealed **six open ports**. The results indicated that a web server was accessible on **port 3333**, among other services.
# Web Directory Enumeration
Next, I performed directory brute-forcing on the web server hosted at port 3333 using `gobuster`
```bash
gobuster dir -u http://10.10.180.100:3333 -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt
```

![[Pasted image 20250618160616.png]]

This enumeration revealed the existence of an `/internal` directory.

When navigating to `http://10.10.180.100:3333/internal`, I discovered a **file upload form**.

# Interception and Manipulation
I launched **Burp Suite** and enabled intercept mode to monitor and manipulate HTTP requests.
Then I wanted to upload a file and send the request to `Intruder` to find which extension is allowed.

I downloaded the php file for reverse shell and changed the info, uploaded the file and `nc -lvnp 1234` to listen on that port and I successfully established reverse shell:
![[Pasted image 20250619211733.png]]


![[Pasted image 20250619211230.png]]
I find the user and the user flag.

I will come tomorrow and make a privilege escalation.