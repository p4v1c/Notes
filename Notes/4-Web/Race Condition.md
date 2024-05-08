**Basic Race Condition (Limit Overrun) **
- Send the requests to the repeater then create a group and send send it in parallel :

![[Race Condition.png]]

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

