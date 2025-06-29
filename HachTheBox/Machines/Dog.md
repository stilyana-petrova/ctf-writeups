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


# Directory Enumeration
I started `dirb`.
```bash
dirb http://10.10.11.58/ /usr/share/wordlists/dirb/common.txt

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Sun Jun 29 12:54:54 2025
URL_BASE: http://10.10.11.58/
WORDLIST_FILES: /usr/share/wordlists/dirb/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://10.10.11.58/ ----
+ http://10.10.11.58/.git/HEAD (CODE:200|SIZE:23)                                                 
==> DIRECTORY: http://10.10.11.58/core/                                                           
==> DIRECTORY: http://10.10.11.58/files/                                                          
+ http://10.10.11.58/index.php (CODE:200|SIZE:13332)                                              
==> DIRECTORY: http://10.10.11.58/layouts/                                                        
==> DIRECTORY: http://10.10.11.58/modules/                                                        
+ http://10.10.11.58/robots.txt (CODE:200|SIZE:1198)                                              
+ http://10.10.11.58/server-status (CODE:403|SIZE:276)                                            
==> DIRECTORY: http://10.10.11.58/shell/                                                          
==> DIRECTORY: http://10.10.11.58/sites/                                                          
==> DIRECTORY: http://10.10.11.58/themes/                                                         
                                                                                                  
---- Entering directory: http://10.10.11.58/core/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                  
---- Entering directory: http://10.10.11.58/files/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                  
---- Entering directory: http://10.10.11.58/layouts/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                  
---- Entering directory: http://10.10.11.58/modules/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                  
---- Entering directory: http://10.10.11.58/shell/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                  
---- Entering directory: http://10.10.11.58/sites/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                  
---- Entering directory: http://10.10.11.58/themes/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
-----------------
END_TIME: Sun Jun 29 12:58:33 2025
DOWNLOADED: 4612 - FOUND: 4

```

When navigating through core directory I found the admin bar to be `Backdrop CMS v1.27.1`:

![](../../Attachments/Pasted%20image%2020250629200814.png)


But the most interesting endpoint is `/shell`:

![](../../Attachments/Pasted%20image%2020250629201352.png)

# Privilege Escalation 
So, our current user is www-data. And we have to escalate our privileges to a user and then to root.
If we type:
```bash
cat /etc/passwd
```
we can see that there are users named: `johncusack` and `jobert`

![](../../Attachments/Pasted%20image%2020250629213612.png)

In [exploitdb](https://www.exploit-db.com) I found exploit for the same version of Backdrop for Authenticated RCE:
![](../../Attachments/Pasted%20image%2020250629214721.png)

Before we could exploit the vulnerability we need a valid credentials. So I went back in the `/files` directory to search if I missed something.
There was a file `update.settings/json`:

![](../../Attachments/Pasted%20image%2020250629225225.png)

With that I have an email `tiffany@dog.htb` and it seems that I cannot do anything with this right now. Another thing to check was `/.git`:

![](../../Attachments/Pasted%20image%2020250629231119.png)

I went to `/.git/logs/HEAD` and found another email: `dog@dog.htb` which could be a potential username: `dog`.

![](../../Attachments/Pasted%20image%2020250629231349.png)

I researched a bit and found a tool for downloading a git repo from a website: [GitHack](https://github.com/lijiejie/GitHack). I opened the file `settings.php` and found credentials from the connection string: `root:BackDropJ2024DS2024`

![](../../Attachments/Pasted%20image%2020250630005145.png)

```php
$database = 'mysql://root:BackDropJ2024DS2024@127.0.0.1/backdrop';
```

Then a login with the following (already found) credentials was successful: `tiffany@dog.htb:BackDropJ2024DS2024` and I was able to access the admin panel.

![](../../Attachments/Pasted%20image%2020250630010809.png)

I clicked `Add new modules for more functionality` and I need to add a new module with revshell...



---

```bash
python3 52021.py -u http://10.10.11.57 -l admin -p password
```



---

***What I learned?***
- If we have an exposed git repo, it's good to download it.
- It's good to check the source code, robots.txt, /.git repo, and others
- 