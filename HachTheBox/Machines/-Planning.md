# Machine Info
![[../../Attachments/image.png]]


# Scope 10.10.11.68

# Port Enumeration
First I scanned the target with the default nmap scan. The web application was running on port 80 and ssh on port 22.
```bash
sudo nmap 10.10.11.68
```
![[../../Attachments/image-1.png]]

The web application was for education courses. 
![[../../Attachments/image-2.png]]
To be able to access the application I added the ip address and the domain in the `/etc/hosts` file.

# Subdomain Enumeration
One of the questions was:
What subdomain of `planning.htb` hosts a data visualization monitoring platform?

To solve the question I tried `gobuster` for subdomain enumeration with different wordlists.
```bash
gobuster dns -d planning.htb -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

![[../../Attachments/image-3.png]]

After some time I gave up because it took so much time. Then I read the question again. And searched for different data visualization monitoring platform. I tried kibana, grafana and there was a match but cannot access `grafana.planning.htb` because I didn't added it in the `/etc/hosts` file. 
After I wrote the subdomain in the file, I opened the application:

![[../../Attachments/image-4.png]]

# Exploitation
I logged in with the provided credentials: `admin / 0D5oT70Fq13EvB5r`
![[../../Attachments/image-5.png]]

After I took a long time figuring how does this work.
The grafana version was 11.0.0 and found the `CVE-2024-9264` which is the DuckDB injection vulnerability + RCE + File read. Any authenticated user can execute arbitrary SQL queries. 
I found the cve on [github](https://github.com/nollium/CVE-2024-9264) and the instruction for using the exploit:

![[../../Attachments/image-6.png]]

I started with printing the PATH environmental variable using the exploit.

![[../../Attachments/image-7.png]]

After that I wanted to print all the env variables. 
I used `/proc/1/environ` which is the file that contains the environment variables of the process whose PID is 1. In a Linux container, PID 1 is the container's main process. 

![[../../Attachments/image-8.png]]

With that I found the password: `RioTecRANDEntANT!` for the user `enzo`. 
Then I logged in with ssh using the found credentials and was able to retrieve the user flag.

![[../../Attachments/image-9.png]]

![[../../Attachments/image-10.png]]


# Privilege Escalation and Lateral Movement





---
What I learned:
- To add the subdomains in the `/etc/hosts` also
- 