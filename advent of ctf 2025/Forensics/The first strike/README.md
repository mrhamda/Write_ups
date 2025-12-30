# Write-up of the challenge "The first strike"

This challenge is part of the **"Forensics"** category and earns 50 points.

# Goal of the challenge

So the goal is that we have a **"pcap file"**. In that file there is login credentials, the task is to find when the bruteforce was a success and submit the flag as format csd{username_password}.


## Solution

The solution is to sort when the ftp login is a success by searching in wireshark **ftp.response.code == 230**, 230 means a success login in ftp. 
We get this in return:

```
220 Microsoft FTP Service

USER Elf67

331 Password required

PASS snowball

230 User logged in.

OPTS utf8 on

200 OPTS UTF8 command successful - UTF8 encoding now ON.

PWD

257 "/" is current directory.

TYPE I

200 Type set to I.

PASV

227 Entering Passive Mode (192,168,253,245,192,211).

NLST

125 Data connection already open; Transfer starting.
226 Transfer complete.

QUIT

221 Goodbye.


```

So the username was **Elf67** and the password was **snowball**.