- Upgrade 1:
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
echo $TERM
stty -a
#speed 38400 baud; rows 46; columns 174; line = 0;


export SHELL=bash
export TERM=xterm256-color
stty rows 46 columns 174
```