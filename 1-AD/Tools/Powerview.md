- Tool :
https://github.com/PowerShellMafia/PowerSploit/raw/master/Recon/PowerView.ps1

```sh
Import-Module ./PowerView.ps1
```


- Get a list of Computers on the domain
```powershell
Get-NetComputer -Properties samaccountname, samaccounttype, operatingsystem
```

- Get a list of all groups on a domain
```powershell
Get-NetGroup -Domain internal.msp.local | select name
```

- Get List of Kerbroastable Users
```powershell
Get-NetUser -Domain msp.local | Where-Object {$_.servicePrincipalName} | select name, samaccountname, serviceprincipalname | Export-CSV -NoTypeInformation kerberoastable.csv
```

- Find Local Admin Access
```powershell
//  Create Credential Object
$passwd = ConvertTo-SecureString "password" -AsPlainText -Force
$creds = New-Object System.Management.Automation.PSCredential ("fakedomain\user", $passwd)

// Gather List of all workstations on domain and store in variable
$comps = Get-NetComputer -Domain msp.local -Credential $creds

// Attempt to issue a command on each machine - any results indicate local admin on that machine
Invoke-Command -ScriptBlock{hostname} -Computer ($comps.dnshostName) -Credential $creds -ErrorAction SilentlyContinue
```

- List all members of a a given group
```powershell
Get-DomainGroupMember "Domain Admins" -Recurse
```