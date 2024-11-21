### Bypass IP-Based Brute-Force Protection
```sh
X-Forwarded-For:$var$
```

### Bypass Stronger IP Block

- **Investigate login page**: 3 incorrect logins block your IP.
- **Reset counter**: Log into your own account before reaching the limit.
- **Invalid credentials**: Use an invalid username and password.
- **Send to Burp Intruder**: Send `POST /login` request.
- **Pitchfork attack**: Set payload positions in username and password fields.
- **Resource pool**: Add to a resource pool.
- **Concurrent requests**: Set to 1 to maintain order.
###  Username enumeration via subtly different responses

- **Submit invalid credentials**: Start with an invalid username and password.
- **Send to Burp Intruder**: Highlight the username parameter in the `POST /login` request and send it.
- **Payloads tab**: Ensure the username parameter is marked as a payload position. Select "Simple list" payload type and add candidate usernames.
- **Grep - Extract**:
    - Go to the Settings tab.
    - Click Add under Grep - Extract.
    - Highlight the error message "Invalid username or password." from the response.
    - Click OK and start the attack.
- **Review results**: After the attack, sort by the new column to find any subtly different error messages.

``You can also do an Username enumeration via account lock``
https://portswigger.net/web-security/authentication
