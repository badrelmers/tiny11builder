https://github.com/ntdevlabs/tiny11builder/pull/44

> Delete strange oscdimg.exe, too.

You can automatically download a signed Oscdimg binary directly from Microsoft without having to install Windows ADK [#43 (comment)](https://github.com/ntdevlabs/tiny11builder/pull/43#issuecomment-1501074660).

This would be more seamless than having to install Windows ADK just for the Oscdimg binary.
____________________________________________________________
https://github.com/ntdevlabs/tiny11builder/pull/43
When I first toyed with this project I noticed that the Oscdimg executable had an invalid signature, being the cautious type, I installed Windows ADK and compared both of the Oscdimg executables, and to my surprise the Oscdimg binary published with Windows ADK also had an invalid signature and had the same hash.

I verified that the binary distributed with this repository was the same one distributed with Windows ADK, however recently I came across a YouTube video which brought into question the validity of the Oscdimg binary distributed with this project as it wasn't signed. After installing Windows ADK in a fresh Windows 11 virtual machine, I found that Windows ADK has updated their Oscdimg binary and the signature is now valid.

This new binary is grabbed from `C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools\amd64\Oscdimg\oscdimg.exe`, it has a valid signature and it has the SHA-256 hash of `ab9e161049d293b544961bfdf2d61244ade79376d6423df4f60bf9b147d3c78d`.

I hope now that the binary has a valid SHA-256 signature, it can put to rest any unease surrounding this project.

_________

After doing some traffic analysis on the Windows ADK installer, I have managed to automate the download of the signed Oscdimg binary directly from Microsoft using a batch script. Depending on whether you still want to provide the binary directly or not, this script could replace the binary and be ran once as a first step, which would ensure that the binary is provided directly from Microsoft. This PR only updates the binary to the newest signed version however.

Batch script
```batch
pushd %~dp0

curl.exe https://download.microsoft.com/download/6/7/4/674ec7db-7c89-4f2b-8363-689055c2b430/adk/Installers/52be7e8e9164388a9e6c24d01f6f1625.cab --remote-name
curl.exe https://download.microsoft.com/download/6/7/4/674ec7db-7c89-4f2b-8363-689055c2b430/adk/Installers/5d984200acbde182fd99cbfbe9bad133.cab --remote-name
curl.exe https://download.microsoft.com/download/6/7/4/674ec7db-7c89-4f2b-8363-689055c2b430/adk/Installers/9d2b092478d6cca70d5ac957368c00ba.cab --remote-name
curl.exe https://download.microsoft.com/download/6/7/4/674ec7db-7c89-4f2b-8363-689055c2b430/adk/Installers/bbf55224a0290f00676ddc410f004498.cab --remote-name
curl.exe https://download.microsoft.com/download/6/7/4/674ec7db-7c89-4f2b-8363-689055c2b430/adk/Installers/Oscdimg%%20(DesktopEditions)-x86_en-us.msi --output "Oscdimg (DesktopEditions)-x86_en-us.msi"

mkdir temp
msiexec.exe /a "Oscdimg (DesktopEditions)-x86_en-us.msi" /qn TARGETDIR=%cd%\temp
copy "temp\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools\amd64\Oscdimg\oscdimg.exe" "oscdimg.exe"
rmdir /s /q temp

del 52be7e8e9164388a9e6c24d01f6f1625.cab
del 5d984200acbde182fd99cbfbe9bad133.cab
del 9d2b092478d6cca70d5ac957368c00ba.cab
del bbf55224a0290f00676ddc410f004498.cab
del "Oscdimg (DesktopEditions)-x86_en-us.msi"

popd

```
___________________

https://github.com/ianis58/tiny11builder/issues/3
https://github.com/WinJuvenate/WinJuvenate
https://github.com/ianis58/tiny11builder
https://github.com/ntdevlabs/tiny11builder/issues/47
