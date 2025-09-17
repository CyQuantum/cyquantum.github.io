---
title: "Blueprint"
summary: "Hack into this Windows machine and escalate your privileges to Administrator."
categories: ["Try Hack Me"]
tags: ["Windows"]
date: 2025-09-16
layout: "simple"
---

# Blueprint room

https://tryhackme.com/room/blueprint

First step was of course to run nmap on the ip to find open ports
<img width="1336" height="664" alt="image" src="https://github.com/user-attachments/assets/edc83be2-8db1-400f-bec9-6c57d1b6eadd" />

First, lets try running smbmap to find out more about which shares are there

```shell
┌──(ravin㉿MSI)-[/mnt/d]
└─$ smbmap -u anonymous -H 10.201.107.66
[/] Checking for open ports...
[*] Detected 1 hosts serving SMB
[-] Authenticating...
[*] Established 1 SMB connections(s) and 1 authenticated session(s)
[\] Enumerating shares...
[+] IP: 10.201.107.66:445       Name: 10.201.107.66             Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    NO ACCESS       Remote IPC
        Users                                                   READ ONLY
        Windows                                                 NO ACCESS
[-] Closing connections..
```

which we can then login with

```shell
┌──(ravin㉿MSI)-[/mnt/d]
└─$ smbclient //10.201.107.66/Users -U anonymous
```

but, this was to no avail as there was nothing useful.

I then pivoted to looking towards port 8080 as there seemed to be some interesting stuff there, catalog and docs

```
8080/tcp  open  http         Apache httpd 2.4.23 (OpenSSL/1.0.2h PHP/5.6.28)
|_http-server-header: Apache/2.4.23 (Win32) OpenSSL/1.0.2h PHP/5.6.28
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2019-04-11 22:52  oscommerce-2.3.4/
| -     2019-04-11 22:52  oscommerce-2.3.4/catalog/
| -     2019-04-11 22:52  oscommerce-2.3.4/docs/
|_
|_http-title: Index of /
| http-methods:
```

I used searchsploit to find out if there were any vulnerabilities, and turns out there were!
<img width="1433" height="328" alt="image" src="https://github.com/user-attachments/assets/56817df0-b648-462c-a1f8-e67a8d436ea1" />
or you can also use
https://github.com/nobodyatall648/osCommerce-2.3.4-Remote-Command-Execution
And now we have root shell, as we have user “authority\system” which is root privileges in Windows
<img width="1051" height="177" alt="image" src="https://github.com/user-attachments/assets/59ba5621-4551-41b4-9448-3dad1d06d1de" />
By digging around a bit more, we find that in `C:\Users\Administrator\Desktop` we find `root.txt.txt` which probably contains the root flag  
<img width="722" height="322" alt="image" src="https://github.com/user-attachments/assets/ebdbb8ec-cd06-4dd5-96c4-1e6075aecc1f" />  
And thus, we obtain the root flag, `THM{aea1e3ce6fe7f89e10cea833ae009bee}`
<img width="742" height="84" alt="image" src="https://github.com/user-attachments/assets/c892b7f4-bfd3-4104-8a24-82d3532caab5" />  
Now, there is only one task remaining, and that is to find the Lab user’s NTLM hash decrypted.  
<img width="644" height="252" alt="image" src="https://github.com/user-attachments/assets/1e22a8e8-3a6b-4928-9f1a-2694a81b5f2c" />  
We can download the files off `http://10.201.107.66:8080/oscommerce-2.3.4/catalog/install/includes` and use `samdump2` to dump the hashes  
<img width="1070" height="147" alt="image" src="https://github.com/user-attachments/assets/24875bbb-a8e6-47af-a7ad-33f028b5d125" />  
We then use crackstation to crack the hash and find the password `googleplus`  
<img width="1340" height="501" alt="image" src="https://github.com/user-attachments/assets/34707411-9aa8-4e17-bdea-32f672ef262e" />
