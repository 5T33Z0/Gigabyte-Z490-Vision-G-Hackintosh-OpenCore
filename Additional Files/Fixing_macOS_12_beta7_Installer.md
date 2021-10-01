#### 1. Fixing the Installer (beta 7 only)
Something in the installer of beta 7 is broken, so that the content of `SharedSupport.dmg` is not copied to the Installer App (size is only 34 MB. If you use aTo fix it you need to do the following:

#### Fixing the macOS Monterey Installer (beta 7 only)

- Download macOS Monterey build `21A5522h` via ANYmacOS
- After the download is completed, it will ask for your password to install the macOS Monterey Installer App in Applicatios
- After this process is finished, **DON'T** delete the downloaded files
- Go to `Desktop\ANYmacOS\Downloads` 
- Move `InstallAssistant.pkg` to the Desktop
- Next, enter in Terninal:
	- `cd ~/Desktop`
	- `xar -xf InstallAssistant.pkg SharedSupport.dmg` (mounts it)
	- `sudo mkdir /Applications/Install\ macOS\ Monterey\ beta.app/Contents/SharedSupport`
	- Next, right-click on the macOS Monterey Installer App and select "Show Package Contents"
	- Next, double-click the `SharedSupport.dmg` on the desktop
	-  Copy its contents to `/Contents/SharedSupport` inside of the macOS Monterey Installer App

If done correctly, the folder structure should look like this:

![](https://raw.githubusercontent.com/5T33Z0/Lenovo-T530-Hackinosh-OpenCore/main/Pics/SharedSupport.png)

