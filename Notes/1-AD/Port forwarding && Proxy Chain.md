
##### Option `-L` (Local Port Forwarding)

- **Syntaxe** : `ssh -L [port_local]:[host_remote]:[port_remote] [utilisateur]@[hôte]`
    
- **Usage** : Redirige un port de votre machine locale vers un port sur la machine distante.
    
- **Exemple** : `ssh -L 4000:127.0.0.1:3000 johndoe@192.168.1.100`
    
    - **Port local (4000)** : Le port sur votre machine locale.
    - **Adresse distante (127.0.0.1)** : L'adresse de la machine distante.
    - **Port distant (3000)** : Le port sur la machine distante.
    
    **But** : Utilisé pour accéder à des services sur la machine distante via un port local sur votre machine.
    

##### Option `-R` (Remote Port Forwarding)

- **Syntaxe** : `ssh -R [port_remote]:[host_local]:[port_local] [utilisateur]@[hôte]`
    
- **Usage** : Redirige un port de la machine distante vers un port sur votre machine locale.
    
- **Exemple** : `ssh -R 4000:127.0.0.1:3000 johndoe@192.168.1.100`
    
    - **Port distant (4000)** : Le port sur la machine distante.
    - **Adresse locale (127.0.0.1)** : L'adresse de votre machine locale.
    - **Port local (3000)** : Le port sur votre machine locale.

## Chisel / Proxychain

- Get the binary : 
```sh
wget https://github.com/jpillora/chisel/releases/download/v1.9.1/chisel_1.9.1_linux_amd64.gz
```

Adds a line to `/etc/proxychains4.conf` to be able to use `proxychains`:

```sh
socks5 127.0.0.1 9050
```

- Download the binary on the remote host and then  : 

```sh
# On your local machine
./chisel server -p 8888 -reverse
```

```sh
# On the remote host
./chisel client 10.8.3.12:8888 R:9050:socks &
```

```sh
proxychains -q nmap -sT localhost
```

- The  Burp settings : 
![[Proxychain.png]]

