# COMODO One Client Communication Chocolatey Package
## CHOCO INSTALL comodo-cocc

[https://chocolatey.org/packages/comodo-cocc/]

Chocolatey Package for the COMODO One Client Communications ITSM/RMM Agent
This will need the agent to be activated to the HOST and TOKEN of your Comodo One account.
I am working with Comodo to pass these arguments directly to the CHOCO installer so that this can be automatted more.
It is possible to do via a work around with an INI config file enrollment_config.ini which is documented on the WIKI:
[https://wiki.comodo.com/frontend/web/topic/how-to-deploy-comodo-clients-on-vdi-environment-or-enroll-cloned-devices-automatically]
It is a bit of a messy undocumented way and I don't feel confident in it working via a package but I have written powershell to automate the creation of this file and I am releasing it as a package seperate to this one but hopefully they will get back to me on a magic way to just pass these required arguments directly to the installer or service and avoid all this. Until then this will get you the main agent installed with a lovely GUI to activate the device with by putting the domain and and token from your Comodo One portal.

### Version 6.14.9529.17120 / December 23rd 2017
[https://c1forum.comodo.com/forum/products/21070-comodo-one-it-operating-platform-december-release-12-23-2017]
[http://dl.cmdm.comodo.com/download/COCC.msi]

### Release Notes
[https://c1forum.comodo.com/filedata/fetch?id=21083]

### Checksum Verification using the AU Powershell Module 
https://www.powershellgallery.com/packages/AU/2017.8.30
https://github.com/majkinetor/au/

```Import-Module AU
Get-RemoteChecksum -Algorithm sha256 -Url 'http://dl.cmdm.comodo.com/download/COCC.msi'
b35cad69840578a783c41e79e3a65d7db19ef7f0e50d66de8f95a6a885e91504```

## Automatic Enrollment

enrollment_config.ini file placed in %PROGRAMFILES%\COMODO\Comodo ITSM\ or %PROGRAMFILES(x86)\COMODO\Comodo ITSM\

```[general]
host = hostname-msp.cmdm.comodo.com
token = {token}
port = 443
remove_third_party = false
suite = 4```

Found on these instructions about depoloying in a VDI environment, this is the only way I have found to automate the enrollment and pass these parameters.
[https://wiki.comodo.com/frontend/web/topic/how-to-deploy-comodo-clients-on-vdi-environment-or-enroll-cloned-devices-automatically]

A forum post I've created about this is at: [https://c1forum.comodo.com/forum/products/rmm/21146-chocolatey-nuget-packages-host-and-token-installer-args]

### Powershell to create this simple INI file for you
```
$token = "55776ef2c85b6ca713c1ea0377b99e00"
$domain = "inventica-msp"
$COMODOpath = Join-Path -ChildPath "COMODO\COMODO ITSM" -Path ${env:ProgramFiles(x86)}
if (Test-Path -Path $COMODOpath -eq $false){
    $COMODOpath = Join-Path -ChildPath "COMODO\COMODO ITSM" -Path ${env:ProgramFiles}
}
$inipath = Join-Path -ChildPath "enrollment_config.ini" -Path $COMODOpath
$ini = """
[General]
token = $($token)
host = $($domain).cmdm.comodo.com
port = 443
suite = 4
remove_third_party = false
"""
Out-File -FilePath $inipath -Encoding utf8 -InputObject $ini
& . "$($COMODOpath)\ITSMService.exe -c 2"
```