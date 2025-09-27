https://tryhackme.com/room/linprivesc
Hello, I tried very interesting challenge for Linux privilege escalation in TryHackMe. 
I had to login via ssh with the following credentials.
- Username: leonard
- Password: Penny123

We start as a user `leonard` and the first step was to identify what commands the user can execute as root.
`sudo -l` showed that the user cannot run sudo commands.

With the following command I found SUID for `base64`:
```bash
find / -type f -perm -04000 -ls 2>/dev/null
```

![[../Attachments/Pasted image 20250927092747.png]]

So if we can't `cat` the `/etc/passwd` file we can read it with `base64`. And with this method I found a user `missy`. After that I read the `/etc/shadow` file for a password for the user `missy`. 
```bash
base64 /etc/shadow | base64 -d
```

Then I saved the hashed password in a file and decoded it with `john the ripper`:
![[../Attachments/Pasted image 20250927093222.png]]

After the password for user `missy` was found I switched the user and submitted the flag1.txt
![[../Attachments/Pasted image 20250927093400.png]]

After I become the user `missy` I had to identify which commands the user may run as sudo with `sudo -l`:
![[../Attachments/Pasted image 20250927093515.png]]

The SUID files:

![[../Attachments/Pasted image 20250927093700.png]]

The interesting things may be crontab and base64. Nothing I can do with these though or that's what I thought.

But proceeded to make the path privesc:
![[../Attachments/Pasted image 20250927093834.png]]

Found that the `/tmp` is writable folder.
Added `/tmp` folder in PATH variable.
![[../Attachments/Pasted image 20250927093853.png]]

Created a test file with content: `/bin/bash` in the `/tmp` folder, so when we try to execute it it should check executable in the PATH directories. In the end this is not working.
![[../Attachments/Pasted image 20250927093929.png]]

And this whole thing for flag2.txt was useless because we can just use the base64 to see the root flag.
```bash
base64 /home/rootflag/flag2.txt | base64 -d
```

![[../Attachments/Pasted image 20250927094006.png]]
