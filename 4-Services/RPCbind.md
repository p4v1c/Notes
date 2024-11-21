Get  some information : 
```sh
showmount -e
```

Mount the directories :
```sh
sudo mount -t nfs -o vers=4 10.10.121.175:/profiles /mntc
```

Clean : 
```sh
sudo umount /mnt
```