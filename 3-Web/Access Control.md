- Check cookies 

- Ty to change URL value 

- Try to change POST body like :
```json
"roleid":1 --> "roleid":2
```
- You can add  `X-Original-URL`: `URL` or `X-Rewrite-URL` to override the URL in the original request .

- You can also change the request method for example GET to POST 

- Sometime you can have an information disclosure that will help you to bypass access control 

- And also you can exploit an IDOR like brute-force the key `url?value=key` 

- Sometimes you have a referer header where the server check which page initiated the request .And if the request can be fully controlled by an attacker you can easily bypass the access control  