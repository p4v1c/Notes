- Tools: 
https://jwt.io/

**Exploiting flawed JWT signature verification** :

```sh
{ "username": "carlos", "isAdmin": false }
```
to
```sh
{ "username": "administrator", "isAdmin": true }
```

![[JWT.png]]

**JWT authentication bypass via flawed signature verification** : 

- Change the alg value to none :  
![[Assets/JWT2.png]]

`You can use the options attack none in the jwt editor extension` 

**Brute-forcing secret keys**

```sh
hashcat -a 0 -m 16500 <jwt> <wordlist>
```

`It's the whole jwt `

**Injecting self-signed JWTs via the jwk parameter**

- We automatically generate a new RSA key pair :
![[JWT3.png]]

![[JWT4.png]]
- **Attack**, then select **Embedded JWK**. When prompted, select your newly generated RSA key and click **OK**.
![[JWT5.png]]

`Don't forget to modify the desire parameter`

**JWT authentication bypass via jku header injection**