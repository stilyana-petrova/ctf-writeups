https://tryhackme.com/room/lightroom

We are given a username to start with: smokey

```bash
nc 10.10.228.50 1337
```

![](../Attachments/Pasted%20image%2020250622221624.png)

With this error when typing `'`, we are sure that there is sql injection possible.

![](../Attachments/Pasted%20image%2020250622221703.png)

With all small caps the union command is blocked, so I typed only the first letter big.
Possible SQLite db.
![](../Attachments/Pasted%20image%2020250622221733.png)
`‘ Union Select group_concat(sql) FROM sqlite_master ‘` - gives a list of tables in the db. 

With the following command I was able to find the admin username
![](../Attachments/Pasted%20image%2020250622221817.png)
![](../Attachments/Pasted%20image%2020250622221836.png)
For a password if gives me only the flag.

And to get the password I used the following command, which also gives the flag.
![](../Attachments/Pasted%20image%2020250622221943.png)


