
#firewall/ips

1. **SYN-Scan (Example 1):**

```sh
sudo nmap 91.194.100.174 -p 21,22,25 -sS -Pn -n --disable-arp-ping --packet-trace
```

2. **ACK-Scan (Example 2):**

```sh
sudo nmap  91.194.100.174 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace
```

3. **Decoy Scan (Example 3):**

```sh
sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5
```

4. **Testing Firewall Rule (Example 4):**

```sh
sudo nmap 91.194.100.174 -n -Pn -p 445 -O
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


