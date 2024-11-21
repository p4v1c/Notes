Cheat sheet AD :
https://orange-cyberdefense.github.io/ocd-mindmaps/img/pentest_ad_dark_2023_02.svg

CVE-Website:
https://www.cvedetails.com/

Custom password :
https://github.com/Mebus/cupp

Custom Username Wordlist :
https://github.com/urbanadventurer/username-anarchy.git

Custom password :
https://github.com/digininja/CeWL

Setuid and setgid : 
https://linuxconfig.org/how-to-use-special-permissions-the-setuid-setgid-and-sticky-bits

SUID : 
https://gtfobins.github.io/

Dump the network : 
https://github.com/DanMcInerney/net-creds.git

Privesc tool : 
https://github.com/CISOfy/lynis

Seatbelt and SharpUp
https://github.com/r3motecontrol/Ghostpack-CompiledBinaries

LaZagne:
https://github.com/AlessandroZ/LaZagne/releases/

|Tool|Description|
|---|---|
|[Seatbelt](https://github.com/GhostPack/Seatbelt)|C# project for performing a wide variety of local privilege escalation checks|
|[winPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS)|WinPEAS is a script that searches for possible paths to escalate privileges on Windows hosts. All of the checks are explained [here](https://book.hacktricks.xyz/windows/checklist-windows-privilege-escalation)|
|[PowerUp](https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1)|PowerShell script for finding common Windows privilege escalation vectors that rely on misconfigurations. It can also be used to exploit some of the issues found|
|[SharpUp](https://github.com/GhostPack/SharpUp)|C# version of PowerUp|
|[JAWS](https://github.com/411Hall/JAWS)|PowerShell script for enumerating privilege escalation vectors written in PowerShell 2.0|
|[SessionGopher](https://github.com/Arvanaghi/SessionGopher)|SessionGopher is a PowerShell tool that finds and decrypts saved session information for remote access tools. It extracts PuTTY, WinSCP, SuperPuTTY, FileZilla, and RDP saved session information|
|[Watson](https://github.com/rasta-mouse/Watson)|Watson is a .NET tool designed to enumerate missing KBs and suggest exploits for Privilege Escalation vulnerabilities.|
|[LaZagne](https://github.com/AlessandroZ/LaZagne)|Tool used for retrieving passwords stored on a local machine from web browsers, chat tools, databases, Git, email, memory dumps, PHP, sysadmin tools, wireless network configurations, internal Windows password storage mechanisms, and more|
|[Windows Exploit Suggester - Next Generation](https://github.com/bitsadmin/wesng)|WES-NG is a tool based on the output of Windows' `systeminfo` utility which provides the list of vulnerabilities the OS is vulnerable to, including any exploits for these vulnerabilities. Every Windows OS between Windows XP and Windows 10, including their Windows Server counterparts, is supported|
https://www.leeholmes.com/adjusting-token-privileges-in-powershell/

https://www.powershellgallery.com/packages/PoshPrivilege/0.3.0.0/Content/Scripts%5CEnable-Privilege.ps1
https://raw.githubusercontent.com/fashionproof/EnableAllTokenPrivs/master/EnableAllTokenPrivs.ps1

https://itm4n.github.io/printspoofer-abusing-impersonate-privileges/

https://github.com/decoder-it/psgetsystem

https://github.com/daem0nc0re/PrivFu/tree/main/PrivilegedOperations/SeDebugPrivilegePoC

https://medium.com/@markmotig/enable-all-token-privileges-a7d21b1a4a77

Generate user wordlist : 
```sh
john -wordlist:users.txt -rule:KoreLogic -stdout
```

http://www.labofapenetrationtester.com/2017/05/abusing-dnsadmins-privilege-for-escalation-in-active-directory.html

https://msrc.microsoft.com/update-guide/vulnerability

https://cloud.binary.ninja/

https://beeceptor.com/mock-api/

https://blog.0daylabs.com/2016/04/03/unserialize-php-object-injection/

https://github.com/zrax/pycdc

https://book.hacktricks.xyz/pentesting-web/ssrf-server-side-request-forgery/url-format-bypass

https://beta.hackndo.com/

https://7rocky.github.io/en/ctf/htb-challenges/pwn/

https://github.com/jib1337/Rogue-MySQL-Server

- Precompiled Binary :
https://github.com/h4rithd/PrecompiledBinaries

https://www.offsec.com/labs/

https://github.com/Orange-Cyberdefense/GOAD

https://pwn.college/

https://www.thehacker.recipes/ad/

https://evasions.checkpoint.com/about/

https://swisskyrepo.github.io/InternalAllTheThings/active-directory/pwd-read-laps/

https://www.synacktiv.com/publications/ounedpy-exploiting-hidden-organizational-units-acl-attack-vectors-in-active-directory

https://www.youtube.com/watch?v=dShZR6FUV2w

InQL : https://github.com/doyensec/inql

pwnfox: https://github.com/yeswehack/PwnFox

https://github.com/spaze/hashes

https://www.thc.org/segfault/

https://nip.io/

dns rebinding : https://lock.cmpxchg8b.com/rebinder.html

https://csp-evaluator.withgoogle.com/

Caido

https://notes.secure77.de/?link=%2FOFFSEC%20Notes%2FWindows%20Related%2F_Windows%20Shells

https://github.com/kevin-mizu/domloggerpp