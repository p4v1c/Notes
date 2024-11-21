```sh
evil-winrm  -i $t -u Caroline.Robinson -p 'Pentest_123456789'
```

- Connect with a hash:
```sh
evil-winrm -i <IP_ADDRESS> -u <USERNAME> -H <NTLM_HASH>
```

- Connect with a certificate:
```sh
evil-winrm -i <IP_ADDRESS> -S -c <CERTIFICATE_FILE> -k <KEY_FILE>
```

+ Connect with Kerberos authentication:
```sh
evil-winrm -i <IP_ADDRESS> -u <USERNAME> -r <DOMAIN> -k
```

- Upload a file:
```sh
upload <LOCAL_FILE_PATH> <REMOTE_FILE_PATH>
```

- Download a file:
```sh
download <REMOTE_FILE_PATH> <LOCAL_FILE_PATH>
```

- Connect with a certificate : 
```sh
evil-winrm -S -i 10.10.10.103 -u amanda -p Ashare1972 -c certnew.cer -k private.key
```
- Interactive shell : 
https://github.com/antonioCoco/ConPtyShell
