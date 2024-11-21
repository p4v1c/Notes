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

- Tool :
https://beeceptor.com/mock-api/

- Generate a new RSA key

![[jwt6.png]]

- Copy public key as JWK : into your exploit server : 

```json
{ 
"keys":[ 
	
	] 
}
```

![[jwt7.png]]

- In the header of the JWT, replace the current value of the `kid` parameter with the `kid` of the JWK that you uploaded to the exploit server.

- Add a new `jku` parameter to the header of the JWT. Set its value to the URL of your JWK Set on the exploit server.

- In the payload, change the value of the `sub` claim to `administrator`.

- At the bottom of the tab, click **Sign**, then select the RSA key that you generated in the previous section.

- Make sure that the **Don't modify header** option is selected, then click **OK**. The modified token is now signed with the correct signature.


**JWT authentication bypass via kid header path traversal**

- Create a New Symmetric key Null bytes encoded in base64 for k parameter : 
![[jwt8.png]]

- Then change the kid value like this :
![[jwt9.png]]

``Make sure to try every possible lfi for the kid like double encoding etc . And don't forget to sign the key ``

**JWT authentication bypass via algorithm confusion**

- Looks for `/jwks.json` or `/.well-known/jwks.json` file .
![[jwt10.png]]

- Copy the key from the json file to the new RSA key , let the JWK parameter. Click ok  and right click on the key then copy the key as PEM  .

![[jwt13.png]]

- Convert the key in base64 with the decoder : 
![[jwt11.png]]

- Create a new symmetric key and replace the k value this base64 of the key :
![[jwt12.png]]

- Change the alg RS256 to HS256 and the sub to administrator and sign it .


**JWT authentication bypass via algorithm confusion with no exposed key**

- Copy two valid JWT somewhere then use this command : 
```sh
#Install before docker
docker run --rm -it portswigger/sig2n <token1> <token2>
```

![[jwt14.png]]
- Try to send the two Tampered JWT the good one has 200 response not the 301 and 302 

- Take the base64 encoded key of the good one  then generate a new symmetric key and replace the k value with the encoded key . 

- Then use the 200 response JWT , sign it and modify the sub .

`In the header of the JWT, make sure that the alg parameter is set to HS256`