#Guide to solve the X server problem for Mac users

#(Not fully tested yet)#

Download the newest singularity container (have more pulsar searching software included, see README.md), and then run it on your Mac.

## 1. Download the singularity container:

```
> wget http://haoyangye.com/PSRSOFT_yhy.simg
```

## 2. Download the newest singularity desktop for Mac

(https://sylabs.io/singularity-desktop-macos/)

Once downloaded, double click to open, you will see an installer and an uninstaller. If you have installed a singularity desktop before, please do the uninstaller first and then install.

## 3. Check whether XQuarz on your computer is properly installed

use "ssh -X” to connect to a random ubuntu machine, and then type

```
> xclock
```

in a terminal to see whether you can see a clock jumping up. If yes, then this means the XQuarz is fully installed.

If you cannot see this clock, do check the installation of XQuartz on your Mac.

After that, go to XQuartz preferences, under the Security tap, check the box that says: Allow connections from network clients

Restart XQuartz

## 4. Run the following two commands

```
> export DISPLAY=:0
> /opt/X11/bin/xhost +
```

## 5. Test the container

```
> singularity shell PSRSOFT_yhy.simg
> cd /PSRSOFT/pgplot/
> ./pgdemo1
```

You should see a series of pop-up images now.

## Extra

If still find problems connecting to the X server, try this:

```
sudo vi /etc/ssh/sshd_config
Change the following two lines:

X11Forwarding yes
X11UseLocalhost yes
```


