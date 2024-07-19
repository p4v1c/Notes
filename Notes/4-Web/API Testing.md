
**Web Security Academy alignment with the OWASP Top 10 API vulnerabilities**

The OWASP Foundation periodically publishes a list of critical API-specific security risks. Although some of these risks have a different name in the context of APIs, many of them align with our existing Web Security Academy topics.

The table below specifies which Web Security Academy topics are relevant to the OWASP Top 10 API vulnerabilities:

|                                                 |                                                                                                                                                                                                                                                                                                                                                                            |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Risk**                                        | **Relevant Web Security Academy topics**                                                                                                                                                                                                                                                                                                                                   |
| Broken object level authorization               | [Access control vulnerabilities and privilege escalation](https://portswigger.net/web-security/access-control)                                                                                                                                                                                                                                                             |
| Broken authentication                           | [Authentication vulnerabilities](https://portswigger.net/web-security/authentication)<br><br>[OAuth 2.0 authentication vulnerabilities](https://portswigger.net/web-security/oauth)<br><br>[JWT attacks](https://portswigger.net/web-security/jwt)                                                                                                                         |
| Broken object property level authorization      | [Mass assignment vulnerabilities](https://portswigger.net/web-security/api-testing#mass-assignment-vulnerabilities)                                                                                                                                                                                                                                                        |
| Unrestricted resource consumption               | [Race conditions](https://portswigger.net/web-security/race-conditions)<br><br>[File upload vulnerabilities](https://portswigger.net/web-security/file-upload)                                                                                                                                                                                                             |
| Broken function level authorization             | [Access control vulnerabilities and privilege escalation](https://portswigger.net/web-security/access-control)                                                                                                                                                                                                                                                             |
| Unrestricted access to sensitive business flows | [Business logic vulnerabilities](https://portswigger.net/web-security/logic-flaws)                                                                                                                                                                                                                                                                                         |
| Server side request forgery                     | [Server-side request forgery (SSRF)](https://portswigger.net/web-security/ssrf)                                                                                                                                                                                                                                                                                            |
| Security misconfiguration                       | [Cross-origin resource sharing (CORS)](https://portswigger.net/web-security/cors)<br><br>[Information disclosure vulnerabilities](https://portswigger.net/web-security/information-disclosure)<br><br>[HTTP Host header attacks](https://portswigger.net/web-security/host-header)<br><br>[HTTP request smuggling](https://portswigger.net/web-security/request-smuggling) |
| Improper inventory management                   | [API testing](https://portswigger.net/web-security/api-testing)                                                                                                                                                                                                                                                                                                            |
| Unsafe consumption of APIs                      | [API testing](https://portswigger.net/web-security/api-testing)                                                                                                                                                                                                                                                                                                            |

**Server-side parameter pollution**

Objective: To identify vulnerabilities in the server-side handling of query string parameters.
#### Truncating Query Strings

- **Method:** Using URL-encoded characters like # to truncate the server-side request.
- **Example:**
    - Modified Query String: `GET /userSearch?name=peter%23foo&back=/home`
    - Resulting Server-Side Request: `GET /users/search?name=peter#foo&publicProfile=true`
- **Observation:** Review response for indications of truncation or incorrect handling of parameters.

#### Injecting Invalid Parameters

- **Method:** Using URL-encoded characters like & to inject additional parameters.
- **Example:**
    - Modified Query String: `GET /userSearch?name=peter%26foo=xyz&back=/home`
    - Resulting Server-Side Request: `GET /users/search?name=peter&foo=xyz&publicProfile=true`
- **Observation:** Check if the injected parameter affects server-side processing.

#### Injecting Valid Parameters

- **Method:** Adding valid parameters to the query string.
- **Example:**
    - Modified Query String: `GET /userSearch?name=peter%26email=foo&back=/home`
    - Resulting Server-Side Request: `GET /users/search?name=peter&email=foo&publicProfile=true`
- **Observation:** Assess how the additional parameter is handled.

#### Overriding Existing Parameters

- **Method:** Attempt to override original parameters by injecting a second parameter with the same name.
- **Example:**
    - Modified Query String: `GET /userSearch?name=peter%26name=carlos&back=/home`
    - Resulting Server-Side Request: `GET /users/search?name=peter&name=carlos&publicProfile=true`
- **Observation:** Examine how the application processes conflicting parameters, considering different web technologies.

**Note:** Exploiting overridden parameters could potentially lead to unauthorized access or privilege escalation.

**Exploiting server-side parameter pollution in a REST URL**

Here is an example with a path traversal : 
## Study the behavior

1. In Burp's browser, trigger a password reset for the `administrator` user.
    
2. In **Proxy > HTTP history**, notice the `POST /forgot-password` request and the related `/static/js/forgotPassword.js` JavaScript file.
    
3. Right-click the `POST /forgot-password` request and select **Send to Repeater**.
    
4. In the **Repeater** tab, resend the request to confirm that the response is consistent.
    
5. Send a variety of requests with a modified username parameter value to determine whether the input is placed in the URL path of a server-side request without escaping:
    
    1. Submit URL-encoded `administrator#` as the value of the `username` parameter.
        
        Notice that this returns an `Invalid route` error message. This suggests that the server may have placed the input in the path of a server-side request, and that the fragment has truncated some trailing data. Observe that the message also refers to an API definition.
        
    2. Change the value of the username parameter from `administrator%23` to URL-encoded `administrator?`, then send the request.
        
        Notice that this also returns an `Invalid route` error message. This suggests that the input may be placed in a URL path, as the `?` character indicates the start of the query string and therefore truncates the URL path.
        
    3. Change the value of the `username` parameter from `administrator%3F` to `./administrator` then send the request.
        
        Notice that this returns the original response. This suggests that the request may have accessed the same URL path as the original request. This further indicates that the input may be placed in the URL path.
        
    4. Change the value of the username parameter from `./administrator` to `../administrator`, then send the request.
        
        Notice that this returns an `Invalid route` error message. This suggests that the request may have accessed an invalid URL path.
        

## Navigate to the API definition

1. Change the value of the username parameter from `../administrator` to `../%23`. Notice the `Invalid route` response.
    
2. Incrementally add further `../` sequences until you reach `../../../../%23` Notice that this returns a `Not found` response. This indicates that you've navigated outside the API root.
    
3. At this level, add some common API definition filenames to the URL path. For example, submit the following:
    
    `username=../../../../openapi.json%23`
    
    Notice that this returns an error message, which contains the following API endpoint for finding users:
    
    `/api/internal/v1/users/{username}/field/{field}`
    
    Notice that this endpoint indicates that the URL path includes a parameter called `field`.
    

## Exploit the vulnerability

1. Update the value of the `username` parameter, using the structure of the identified endpoint. Add an invalid value for the `field` parameter:
    
    `username=administrator/field/foo%23`
    
    Send the request. Notice that this returns an error message, because the API only supports the email field.
    
2. Add `email` as the value of the `field` parameter:
    
    `username=administrator/field/email%23`
    
    Send the request. Notice that this returns the original response. This may indicate that the server-side application recognizes the injected `field` parameter and that `email` is a valid field type.
    
3. In **Proxy > HTTP history**, review the `/static/js/forgotPassword.js` JavaScript file. Identify the password reset endpoint, which refers to the `passwordResetToken` parameter:
    
    `/forgot-password?passwordResetToken=${resetToken}`
    
4. In the **Repeater** tab, change the value of the `field` parameter from `email` to `passwordResetToken`:
    
    `username=administrator/field/passwordResetToken%23`
    
    Send the request. Notice that this returns an error message, because the `passwordResetToken` parameter is not supported by the version of the API that is set by the application.
    
5. Using the `/api/` endpoint that you identified earlier, change the version of the API in the value of the `username` parameter:
    
    `username=../../v1/users/administrator/field/passwordResetToken%23`
    
    Send the request. Notice that this returns a password reset token. Make a note of this.
    
6. In Burp's browser, enter the password reset endpoint in the address bar. Add your password reset token as the value of the `reset_token` parameter. For example:
    
    `/forgot-password?passwordResetToken=123456789`
    
7. Set a new password.
    
8. Log in as the `administrator` using your password.
    
9. Go to the **Admin panel** and delete `carlos` to solve the lab.
