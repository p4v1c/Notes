https://github.com/BloodHoundAD/SharpHound
- No foothold  : 
```sh
bloodhound-python -d 'LAB.TRUSTED.VL' -u 'rsmith' -p 'IHateEric2' -ns 10.10.245.166 -dc labdc.LAB.TRUSTED.VL -c all
```

- With a shell we collect json files whit SharpHound 
```sh
./SharpHound.exe --CollectionMethods All
Invoke-BloodHound -CollectionMethod All
```

- If you have some issue with python bloodhound : 
```sh
dnschef --fakeip 10.10.233.102 
  
bloodhound-python -d 'LAB.TRUSTED.VL' -u 'rsmith' -p 'IHateEric2' -ns 127.0.0.1 -dc labdc.LAB.TRUSTED.VL -c all
```

- Start Bloodhound GUI : 
```sh
neo4j start
bloodhound
```
- Extract users :
```sh
grep -o '"samaccountname": "[^"]*"' 20240731155912_users.json | sed 's/"samaccountname": "\(.*\)"/\1/'
```

- With nxc : 
```sh
nxc ldap 10.10.202.37 -u Rhys.Garner -p knh1gJ8Xmeq+uP --bloodhound --collection All -ns 10.10.202.37
```
