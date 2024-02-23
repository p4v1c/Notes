Be patient it take time to scan your target with UDP

Enumeration with nmap :
```sh
sudo nmap -sU --script "snmp* and not snmp-brute" -T5 10.10.11.248
```

It checks if your are able to write and at the same that enumerate snmp:
```sh
snmp-check -p 161 -c <community-string> -w <ip> -d
```
By default community string is public and the default is port 161.

Enumerate smnp:
```sh
snmpwalk -v <version> -c public <ip>
```

Brute force snmp string community : 

```sh
onesixtyone -c dict.txt 10.129.42.254
```