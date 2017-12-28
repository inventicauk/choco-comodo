# Procedures for Comodo
## Procedure to install Chocolatey

The first procedure can be used to install CHOCO on computers. I have this as part of my standard windows profile so that it will be installed as soon as the device is activated.

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

So it installs Chocolatey using the Powershell Script from chocolatey.org and then it set's a few options so that we don't get any prompts, allowGlobalConfirmation, Ignore Checksums, and Don't Warn about Non Elevated.

https://chocolatey.org/install

I also install two packages the first time which is a GUI for the command line CHOCO to try and help drive adoption of the use of Choco to install ANY software because it makes the updating, reisntalling/moving computers all so much easier and possible to be automated. So my install for Chocolatey also puts on the package: chocolateygui and a Core Extension for Choco allowing packages to do some behind the scenes stuff easier. Also if your a lover of command-line you can use 'CINST package' and it's the form that BOXSTARTER scripts use but for readability I have the full choco install written out but save a few keypresses if you like and use cinst for any installs.

cinst chocolatey-core.extension chocolateygui

```
import os
import subprocess
import ctypes

pscmd="powershell -c \"Set-ExecutionPolicy Bypass; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))\""
cmd = 'choco feature enable -n=allowGlobalConfirmation -n=allowEmptyChecksums & choco feature disable -n=showNonElevatedWarnings & cinst chocolateygui chocolatey-core.extension --yes --force'
class disable_file_system_redirection:
	_disable = ctypes.windll.kernel32.Wow64DisableWow64FsRedirection
	_revert = ctypes.windll.kernel32.Wow64RevertWow64FsRedirection
	def __enter__(self):
		self.old_value = ctypes.c_long()
		self.success = self._disable(ctypes.byref(self.old_value))
	def __exit__(self, type, value, traceback):
		if self.success:
			self._revert(self.old_value)
			
#process=subprocess.Popen('powershell "%s"'%ps_command, shell=True, stdout=subprocess.PIPE) 
def runcommand(ps_command=None):
	result = -1
	if ps_command is not None:
		with disable_file_system_redirection():
			result = str(os.popen(ps_command).read()[:])
	return result.strip()

print(runcommand(pscmd))
print(runcommand(cmd))
```

## Procedure to upgrade/install Chocolatey Packages

Full list of packages can be found on below URL you will be surprised at how much software is here, It's rare to not find software that I need so being able to install it remotely as a procedure is amazing. 

http://chocolatey.org/packages

This procedure has a variable on the first line called PROGRAM and this is the program you want to either upgrade if you've already installed it via Choco or it will install it if it can't ugrade.

You can put multiple programs here and I think the best ones to get setup right away via Choco are Browsers, FlashPlayer Plugins for ActiveX/Other Browsers, Java, and Acrobat.
I also find 7z and Sublime Text are two that I can't live without on any machine I have to do IT support on. The below change to the procedure will install and upgrade all these machines and in the future a simple: choco upgrade all

PROGRAM='googlechrome firefox flashplayerplugin flashplayeractivex adobereader jre8 7zip.install sublimetext3'

Logs can be seen of any Chocolatey actions and live at: C:\ProgramData\chocolatey\logs\chocolatey.log

```
PROGRAM='googlechrome'
cmd='choco upgrade {0} --yes --force --ignore-checksums'.format(PROGRAM)
# cmdinstall='cinst {0} --yes --force --ignore-checksums'.format(PROGRAM)
import os
import subprocess
import ctypes

class disable_file_system_redirection:
	_disable = ctypes.windll.kernel32.Wow64DisableWow64FsRedirection
	_revert = ctypes.windll.kernel32.Wow64RevertWow64FsRedirection
	def __enter__(self):
		self.old_value = ctypes.c_long()
		self.success = self._disable(ctypes.byref(self.old_value))
	def __exit__(self, type, value, traceback):
		if self.success:
			self._revert(self.old_value)

with disable_file_system_redirection():
	out=str(os.popen(cmd).read()[:]).strip()

print(out)
```


## Upgrading

Comodo does a great job with it's Patch Management but there are over 25,000 packages managed with Chocolatey and Comodo shouldn't waste time trying to keep up with them you can individually update programs via choco using the choco upgrade but I prefer to just get them all done with choco upgrade all there are even choco packages to automatically do this at a scheduled time, or shutdown etc but that's up to you to check out. This is a simple COMODO Procedure to Upgrade All packages

choco upgrade all --yes --force

```
PROGRAM='all'
cmd='choco upgrade {0} --yes --force --ignore-checksums'.format(PROGRAM)
import os
import subprocess
import ctypes

class disable_file_system_redirection:
	_disable = ctypes.windll.kernel32.Wow64DisableWow64FsRedirection
	_revert = ctypes.windll.kernel32.Wow64RevertWow64FsRedirection
	def __enter__(self):
		self.old_value = ctypes.c_long()
		self.success = self._disable(ctypes.byref(self.old_value))
	def __exit__(self, type, value, traceback):
		if self.success:
			self._revert(self.old_value)

with disable_file_system_redirection():
	out=str(os.popen(cmd).read()[:]).strip()

print(out)
```

## Remove Software

Finally choco uninstall PROGRAM is the command to get rid of something and this is a COMODO procedure to remove a software installed via Chocolatey. Again just edit the first line with PROGRAM to specify which program you want to have removed.
```
PROGRAM='googlechrome'
cmd='choco uninstall {0} --yes --force'.format(PROGRAM)
import os
import subprocess
import ctypes

class disable_file_system_redirection:
	_disable = ctypes.windll.kernel32.Wow64DisableWow64FsRedirection
	_revert = ctypes.windll.kernel32.Wow64RevertWow64FsRedirection
	def __enter__(self):
		self.old_value = ctypes.c_long()
		self.success = self._disable(ctypes.byref(self.old_value))
	def __exit__(self, type, value, traceback):
		if self.success:
			self._revert(self.old_value)

with disable_file_system_redirection():
	out=str(os.popen(cmd).read()[:]).strip()

print(out)
```
