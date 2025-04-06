# SMB Exploits

Samba is a file sharing protocol and pretty prevalent in EJPT labs. 

## Enumeration

It runs on port 445 and can be identified with nmap.

```bash
nmap -p 445 --script smb-protocols <TARGET_IP>
```

Two important things normally asked are smb users and shares. Users aids us in bruteforcing passwords while shares are where flags most likely reside.

After veryfing samba you can do a enumeration of all the usefull info it provides with enum


```bash
enum4linux -a <target>
```

This can or not return information, if the server is robust you might need credentials for it.

## Login

To brute force login we can use hydra:

```bash
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt smb://<target>
```

## Metasploit

All this can be done with auxiliary modules from metasploit and is honestly pretty quicker and works well


```bash
use auxiliary/scanner/smb/smb_version
use auxiliary/scanner/smb/smb_enumusers
use auxiliary/scanner/smb/smb_enumshares
use auxiliary/scanner/smb/smb_login
```

## Connect to smb

To connect to smb use the smblcient command

```bash
smbclient -L //<target> -U <user>
```

After loggin in, you will be prompted with the shares you have access, to get more info out of its share and the permissions your user as run the crackmapexec command:

```bash
crackmapexec smb <target> -u <user> -p <password> --shares
```

It will return the shares permissions, normally ones with read and write are intressting, in last case search every share for the flag.


```bash
smbclient -L //<target>/<share> -U <user>
```