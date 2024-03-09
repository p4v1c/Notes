## Scan Network
#nmap 

```shell
nc -nv <ip> <open-port>
```

```shell
cme smb <ip_range> #enumerate smb host
```

```shell
nmap -sP -p <ip> #ping scan
```

```shell
nmap -Pn -sV --top-ports 50 --open <ip> #quick scan
```

```shell
nmap -Pn --script "smb-vuln*" -p139,445 <ip> #search smb vuln
```

```shell
nmap -Pn -sC -sV -oA <output> <ip> #classic
```

```shell
nmap -Pn -sC -sV -p- -oA <output> <ip> #full
```

```shell
sudo nmap -sU -sC -sV -oA <output> <ip> #udp scan
```

Find some vulnerability wit NSE : 

```shell
nmap <ip> -p <discovered-port> -script vuln
```
## Discovery DC IP
#discovery-DC

```shell
nmcli dev show eth0 #show domain name & dns
```

```shell
nslookup -type=SRV _ldap._tcp.dc._msdcs.<domain>
```

## Listing smb share
#smbenum

```shell
enum4linux -a -u "" -p "" <dc-ip> && enum4linux -a -u "guest" -p "" <dc-ip>
```

```shell
smbmap -a -u "" -p "" -P 445 -H <dc-ip> && smbmap -u "guest" -p "" -P 455 -H <dc-ip>
```

```shell
smbclient -U "%" -L//<dc-ip> && smbclient -U 'guest%' -L//<dc-ip>
```

```shell
cme smb <ip> -u '' -p '' #enumerate null session
```

```shell
cme smb <ip> -u 'a' -p '' #enumerate anonymous access
```

## Enumerate ldap
#ldapenum

```shell
nmap -n -sV --script "ldap* and not brute" -p 389 <dc-ip> 
```

```shell
ldapsearch -x -h <ip> -s base
```

## Find user 
#user

```shell
enum4linux -U <dc-ip> | grep 'user:'
```

```shell
cme smb <ip> --users
```

```shell
net rpc group member 'Domain Users' -W '<domain>' -I '<ip>'
 -U '%'
```

```shell
nmap -p 88 --script=krb5-enum-users --script-args="krb5-enum-users.realm='<domain>'.userdb=<users_list_file>"<ip>
```

## Firewall and IDS/IPS Evasion
#firewall/ips

1. **SYN-Scan (Example 1):**

```sh
sudo nmap 10.129.2.28 -p 21,22,25 -sS -Pn -n --disable-arp-ping --packet-trace
```

2. **ACK-Scan (Example 2):**

```sh
sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace
```

3. **Decoy Scan (Example 3):**

```sh
sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5
```

4. **Testing Firewall Rule (Example 4):**

```sh
sudo nmap 10.129.2.28 -n -Pn -p 445 -O
```

5. **Scan by Using Different Source IP (Example 5):**
```sh
sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0
```

6. **SYN-Scan of a Filtered Port (Example 6):**
```sh
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace
```

7. **SYN-Scan From DNS Port (Example 7):**
```sh
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53
```

8. **Connect To The Filtered Port (Example 8):**

```sh
ncat -nv --source-port 53 10.129.2.28 50000
```


