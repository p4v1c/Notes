#ldap
Using this you will be able to see the **public information** (like the domain name)**:**
```sh
nmap -n -sV --script "ldap* and not brute" <IP> #Using anonymous credentials
```

Check null credentials or if your credentials are valid:
```sh
ldapsearch -x -H ldap://<IP> -D '' -w '' -b "DC=<1_SUBDOMAIN>,DC=<TLD>"

ldapsearch -x -H ldap://<IP> -D '<DOMAIN>\<username>' -w '<password>' -b "DC=<1_SUBDOMAIN>,DC=<TLD>"
```
