- SSH port open + credential   :
Check if your are able to upload files  ,  if you can create a .ssh folder and upload a authorized_keys file in it .

- Unauthenticated enumeration with nmap :

```sh
sudo nmap -sV -p21 -sC -A 10.10.10.10
```

- Anonymous login :
```sh
anonymous : anonymous
anonymous :
ftp : ftp
ftp ftp://anonymous:anonymous@$t/
```

- Download everything : 
```sh
wget -m ftp://anonymous:anonymous@$t
```

