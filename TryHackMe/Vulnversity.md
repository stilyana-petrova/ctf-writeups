https://tryhackme.com/room/vulnversity
# Scope
The target machine was assigned the IP address `10.10.180.100`.
# Port Scanning

To identify open ports and running services, I conducted a service/version detection scan using `nmap`:
```bash
sudo nmap -sV 10.10.180.100
```

![](../Attachments/Pasted%20image%2020250618160407.png)

The scan revealed **six open ports**. The results indicated that a web server was accessible on **port 3333**, among other services.
# Web Directory Enumeration
Next, I performed directory brute-forcing on the web server hosted at port 3333 using `gobuster`
```bash
gobuster dir -u http://10.10.180.100:3333 -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt
```

![](../Attachments/Pasted%20image%2020250618160616.png)

This enumeration revealed the existence of an `/internal` directory.

When navigating to `http://10.10.180.100:3333/internal`, I discovered a **file upload form**.

# Interception and Manipulation
I launched **Burp Suite** and enabled intercept mode to monitor and manipulate HTTP requests.
Then I wanted to upload a file and send the request to `Intruder` to find which extension is allowed.

I downloaded the [php file](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) for reverse shell and changed the info, uploaded the file and `nc -lvnp 1234` to listen on that port and I successfully established reverse shell:
![](../Attachments/Pasted%20image%2020250619211733.png)

# Reverse Shell

![](../Attachments/Pasted%20image%2020250619211230.png)
I found the user and the user flag.


I run:
```bash
find / -perm -u=s -type f 2>/dev/null
```
or as in the hint:
```bash
find / -user root -perm -4000 -exec ls -ldb {} \;
```
to answer the following question: On the system, search for all SUID files. Which file stands out?

![](../Attachments/Pasted%20image%2020250620213900.png)

Answer: /bin/systemctl

I've done some research for this file in a site:
![](../Attachments/Pasted%20image%2020250620214426.png)

...

I first needed to create a service - root.service:
![](../Attachments/Pasted%20image%2020250622195334.png)

Then I started a web server and in the target's shell I wget the root.service

And when I start this service I get a reverse shell on my listener as a root, so I had to find the flag.

![](../Attachments/Pasted%20image%2020250622200244.png)

So with that, my first THM lab is solved 🥳