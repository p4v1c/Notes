
```sh
#Enumeration
nc -vn target 873
```

```sh
# Listing a shared folder
rsync -av --list-only rsync://192.168.0.123/shared_name

# Copying files from a shared folder
rsync -av rsync://192.168.0.123:8730/shared_name ./rsyn_shared
```