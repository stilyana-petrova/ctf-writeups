scope -> 10.10.10.245
First, I tried enumeration the open ports with nmap and were discovered 3 open ports:

```bash
sudo nmap -sC -sV -vv 10.10.10.245 -oN cap_scan.txt
```

output
```json
# Nmap 7.95 scan initiated Sat Jun 28 06:54:28 2025 as: /usr/lib/nmap/nmap -sC -sV -vv -oN cap_scan.txt 10.10.10.245
Nmap scan report for 10.10.10.245
Host is up, received echo-reply ttl 63 (0.093s latency).
Scanned at 2025-06-28 06:54:32 EDT for 19s
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 fa:80:a9:b2:ca:3b:88:69:a4:28:9e:39:0d:27:d5:75 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC2vrva1a+HtV5SnbxxtZSs+D8/EXPL2wiqOUG2ngq9zaPlF6cuLX3P2QYvGfh5bcAIVjIqNUmmc1eSHVxtbmNEQjyJdjZOP4i2IfX/RZUA18dWTfEWlNaoVDGBsc8zunvFk3nkyaynnXmlH7n3BLb1nRNyxtouW+q7VzhA6YK3ziOD6tXT7MMnDU7CfG1PfMqdU297OVP35BODg1gZawthjxMi5i5R1g3nyODudFoWaHu9GZ3D/dSQbMAxsly98L1Wr6YJ6M6xfqDurgOAl9i6TZ4zx93c/h1MO+mKH7EobPR/ZWrFGLeVFZbB6jYEflCty8W8Dwr7HOdF1gULr+Mj+BcykLlzPoEhD7YqjRBm8SHdicPP1huq+/3tN7Q/IOf68NNJDdeq6QuGKh1CKqloT/+QZzZcJRubxULUg8YLGsYUHd1umySv4cHHEXRl7vcZJst78eBqnYUtN3MweQr4ga1kQP4YZK5qUQCTPPmrKMa9NPh1sjHSdS8IwiH12V0=
|   256 96:d8:f8:e3:e8:f7:71:36:c5:49:d5:9d:b6:a4:c9:0c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDqG/RCH23t5Pr9sw6dCqvySMHEjxwCfMzBDypoNIMIa8iKYAe84s/X7vDbA9T/vtGDYzS+fw8I5MAGpX8deeKI=
|   256 3f:d0:ff:91:eb:3b:f6:e1:9f:2e:8d:de:b3:de:b2:18 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPbLTiQl+6W0EOi8vS+sByUiZdBsuz0v/7zITtSuaTFH
80/tcp open  http    syn-ack ttl 63 Gunicorn
| http-methods: 
|_  Supported Methods: HEAD GET OPTIONS
|_http-server-header: gunicorn
|_http-title: Security Dashboard
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Jun 28 06:54:51 2025 -- 1 IP address (1 host up) scanned in 23.07 seconds

```


On the web server I manage to do IDOR attack as another user and downloaded the 0.pcap file. And in the logs of that file there was a password.
![[Pasted image 20250628144249.png]]
![[Pasted image 20250628144256.png]]

![[Pasted image 20250628144311.png]]



```
40	5.424998	192.168.196.1	192.168.196.16	FTP	78	Request: PASS Buck3tH4TF0RM3!
```

With this password we were able to connect with ftp and get the user flag. 
![[Pasted image 20250628144329.png]]


The password was the same for ssh too. So I logged with ssh and started linpeas. It showed a files with capabilities to obtain root privileges: `/usr/bin/python3.8`

![[Pasted image 20250628150043.png]]


In the ssh session I run python code to execute the commands as root.

![[Pasted image 20250628145304.png]]

And after i become root I was able to see the flag.
