**Basic Race Condition (Limit Overrun) **
- Send the requests to the repeater then create a group and send send it in parallel :

![[Race Condition.png]]

**Methodology**

To detect and exploit hidden multi-step sequences, we recommend the following methodology, which is summarized from the whitepaper [Smashing the state machine: The true potential of web race conditions](https://portswigger.net/research/smashing-the-state-machine) by PortSwigger Research.


1-Predict potential collisions

Testing every endpoint is impractical. After mapping out the target site as normal, you can reduce the number of endpoints that you need to test by asking yourself the following questions:

- **Is this endpoint security critical?** Many endpoints don't touch critical functionality, so they're not worth testing.
- **Is there any collision potential?** For a successful collision, you typically need two or more requests that trigger operations on the same record. For example, consider the following variations of a password reset implementation:

With the first example, requesting parallel password resets for two different users is unlikely to cause a collision as it results in changes to two different records. However, the second implementation enables you to edit the same record with requests for two different users.

2 - Probe for clues

To recognize clues, you first need to benchmark how the endpoint behaves under normal conditions. You can do this in Burp Repeater by grouping all of your requests and using the **Send group in sequence (separate connections)** option. For more information, see [Sending requests in sequence](https://portswigger.net/burp/documentation/desktop/tools/repeater/send-group#sending-requests-in-sequence).

Next, send the same group of requests at once using the single-packet attack (or last-byte sync if HTTP/2 isn't supported) to minimize network jitter. You can do this in Burp Repeater by selecting the **Send group in parallel** option. For more information, see [Sending requests in parallel](https://portswigger.net/burp/documentation/desktop/tools/repeater/send-group#sending-requests-in-parallel). Alternatively, you can use the Turbo Intruder extension, which is available from the [BApp Store](https://portswigger.net/bappstore/9abaa233088242e8be252cd4ff534988).

Anything at all can be a clue. Just look for some form of deviation from what you observed during benchmarking. This includes a change in one or more responses, but don't forget second-order effects like different email contents or a visible change in the application's behavior afterward.

3 - Prove the concept

Try to understand what's happening, remove superfluous requests, and make sure you can still replicate the effects.

Advanced race conditions can cause unusual and unique primitives, so the path to maximum impact isn't always immediately obvious. It may help to think of each race condition as a structural weakness rather than an isolated vulnerability.


**Detecting and exploiting limit overrun race conditions with Turbo Intruder & Bypassing rate limits**

In addition to providing native support for the single-packet attack in Burp Repeater, we've also enhanced the Turbo Intruder extension to support this technique. You can download the latest version from the [BApp Store](https://portswigger.net/bappstore/9abaa233088242e8be252cd4ff534988).

Turbo Intruder requires some proficiency in Python, but is suited to more complex attacks, such as ones that require multiple retries, staggered request timing, or an extremely large number of requests.

To use the single-packet attack in Turbo Intruder:

1. Ensure that the target supports HTTP/2. The single-packet attack is incompatible with HTTP/1.
2. Set the `engine=Engine.BURP2` and `concurrentConnections=1` configuration options for the request engine.
3. When queuing your requests, group them by assigning them to a named gate using the `gate` argument for the `engine.queue()` method.
4. To send all of the requests in a given group, open the respective gate with the `engine.openGate()` method.

```python
def queueRequests(target, wordlists):  
	engine = RequestEngine(endpoint=target.endpoint,
							concurrentConnections=1, 
							engine=Engine.BURP2 
							)  
	passwords = wordlists.clipboard  
	for password in passwords: 
		engine.queue(target.req, password, gate='1') 
	
	engine.openGate('1') 
	def handleResponse(req, interesting): 
		table.add(req)
```

For more details, see the `race-single-packet-attack.py` template provided in Turbo Intruder's default examples directory.
![[Race Condition2.png]]
Resource: https://www.youtube.com/watch?v=iKCAzfrswVQ

 **Multi-endpoint race conditions** 
 
Example : 

- Predict a potential collision

1. Log in and purchase a gift card so you can study the purchasing flow.
2. Consider that the shopping cart mechanism and, in particular, the restrictions that determine what you are allowed to order, are worth trying to bypass.
3. From the proxy history, identify all endpoints that enable you to interact with the cart. For example, a `POST /cart` request adds items to the cart and a `POST /cart/checkout` request submits your order.
4. Add another gift card to your cart, then send the `GET /cart` request to Burp Repeater.
5. In Repeater, try sending the `GET /cart` request both with and without your session cookie. Confirm that without the session cookie, you can only access an empty cart. From this, you can infer that:
    
    - The state of the cart is stored server-side in your session.
    - Any operations on the cart are keyed on your session ID or the associated user ID.
    
    This indicates that there is potential for a collision.
    
6. Notice that submitting and receiving confirmation of a successful order takes place over a single request/response cycle.
7. Consider that there may be a race window between when your order is validated and when it is confirmed. This could enable you to add more items to the order after the server checks whether you have enough store credit.
    

- Benchmark the behavior

1. Send both the `POST /cart` and `POST /cart/checkout` request to Burp Repeater.
2. In Repeater, add the two tabs to a new group. For details on how to do this, see Creating a new tab group
3. Send the two requests in sequence over a single connection a few times. Notice from the response times that the first request consistently takes significantly longer than the second one. For details on how to do this, see Sending requests in sequence.
4. Add a `GET` request for the homepage to the start of your tab group.
5. Send all three requests in sequence over a single connection. Observe that the first request still takes longer, but by "warming" the connection in this way, the second and third requests are now completed within a much smaller window. 
6. Deduce that this delay is caused by the back-end network architecture rather than the respective processing time of the each endpoint. Therefore, it is not likely to interfere with your attack.
7. Remove the `GET` request for the homepage from your tab group.
8. Make sure you have a single gift card in your cart.
9. In Repeater, modify the `POST /cart` request in your tab group so that the `productId` parameter is set to `1`, that is, the ID of the **Lightweight L33t Leather Jacket**.
10. Send the requests in sequence again.
11. Observe that the order is rejected due to insufficient funds, as you would expect.
    
- Prove the concept

1. Remove the jacket from your cart and add another gift card.
2. In Repeater, try sending the requests again, but this time in parallel. For details on how to do this, see Sending requests in parallel
3. Look at the response to the `POST /cart/checkout` request:
    - If you received the same "insufficient funds" response, remove the jacket from your cart and repeat the attack. This may take several attempts.
    - If you received a 200 response, check whether you successfully purchased the leather jacket. If so, the lab is solved.

**Bonus  Bypass the per-session locking restriction**

Example : 
1. Notice that your session cookie suggests that the website uses a PHP back-end. This could mean that the server only processes one request at a time per session.
    
2. Send the `GET /forgot-password` request to Burp Repeater, remove the session cookie from the request, then send it.
    
3. From the response, copy the newly issued session cookie and [CSRF](https://portswigger.net/web-security/csrf) token and use them to replace the respective values in one of the two `POST /forgot-password` requests. You now have a pair of password reset requests from two different sessions.
    
4. Send the two `POST` requests in parallel a few times and observe that the processing times are now much more closely aligned, and sometimes identical.