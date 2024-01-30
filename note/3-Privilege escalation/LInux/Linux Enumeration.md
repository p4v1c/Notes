
# Tools:

 Download the linpeas.sh script from the PEASS-ng GitHub repository :
 Link: https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh

Determine the operating system and version:
```sh
cat /etc/os-release
```

Display the system's PATH variable:
```sh
echo $PATH
```

Show the environment variables:
```sh
env
```

Display detailed system information:
```sh
uname -a
```

Gather information about the CPU type/version:
```sh
lscpu
```

Identify the available login shells on the server:
```sh
cat /etc/shells
bash --version
```

Enumerate block devices on the system:
```sh
lsblk
```

Check for credentials in /etc/fstab for mounted drives:
```sh
cat /etc/fstab | grep -E 'password|username|credential'
```

Display the routing table to view available networks and interfaces:
```sh
netstat -rn
```

Check /etc/resolv.conf for domain environments and internal DNS configuration:
```sh
cat /etc/resolv.conf
```

Enumerate users from /etc/passwd:
```sh
cat /etc/passwd | cut -f1 -d:
```

Display information about groups on the system:
```sh
cat /etc/group
```

List members of interesting groups, e.g., 'sudo':
```sh
getent group sudo
```

Display information about mounted file systems:
```sh
df -h
```

Display details of unmounted file systems from /etc/fstab:
```sh
cat /etc/fstab | grep -v "#" | column -t
```

List all hidden files (starting with dot) and show permissions:
```sh
find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep htb-student
```
List all hidden directories and show details:
```sh
find / -type d -name ".*" -ls 2>/dev/null
```

 Display information about temporary files and directories:
```sh
ls -l /tmp /var/tmp /dev/shm
```
 
 Enumerate sh file:
```sh
find / -type f -name "*.sh"
#or
sudo find / -type f -name "*.sh" -user www-data -readable
#or
find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
```

Last login on the machine:
```sh
lastlog 
```

Maybe find some credentials : 
```sh
history
```

Find history file : 
```sh
find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null
```
 
 It can be used to look up system information such as the state of running processes, kernel parameters, system memory, and devices: 
```sh
 find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"
```

See installed services : 
```sh
apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list
```

With the next oneliner, we can compare the existing binaries with the ones from GTFObins:
```sh
for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done
```

Find configuration file on the machine : 

```sh
find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null
#or 
find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null
```

We can use the diagnostic tool `strace` on Linux-based operating systems to track and analyze system calls and signal processing : 
```sh
strace ping -c1 10.129.112.20
```

Have look on the process running : 
```sh
ps aux | grep root
```

Credential Hunting : 
```sh
cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'
#or 
grep -ir "DB_USER\|DB_PASSWORD" /var/www/html
```