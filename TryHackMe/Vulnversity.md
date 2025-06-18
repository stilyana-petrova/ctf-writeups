First I have a machine to test.
I ran nmap on it:
```bash
sudo nmap -sV 10.10.180.100
```
![[Pasted image 20250618160407.png]]

We have 6 ports open. 
After that I ran dirbuster on the web server on port 3333:
```bash
gobuster dir -u http://10.10.180.100:3333 -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt
```

![[Pasted image 20250618160616.png]]

And when i try to open /internal there was a form to upload file.

I have my burpsuite running and intercepting traffic. Then i upload a file and send the request to intruder.