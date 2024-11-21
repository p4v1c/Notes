- Access to smb shared folder
```sh
smbclient //10.10.196.22/NETLOGON -U rsmith
```

- To use rpcclient, you generally need to specify the target machine and the credentials for authentication. Here’s a basic syntax:
```sh
rpcclient <target_IP> -U <username>
```
- Change password if you have the privilege :
```sh
rpcclient $> setuserinfo2 <username> <level> <new_password>
```
- Example : 
```sh
S-1-5-21-2241985869-2159962460-1278545866-1106
#21 is the level
```
