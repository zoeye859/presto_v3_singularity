# Guide to solve the X server problem for Mac users


Download the newest singularity container (have more pulsar searching software included, see README.md), and then run it on your Mac.

## 1. Download the singularity container:

```
> wget http://haoyangye.com/PSRSOFT_yhy.simg
```

## 2. Download the newest singularity desktop for Mac

(https://sylabs.io/singularity-desktop-macos/)

Once downloaded, double click to open, you will see an installer and an uninstaller. If you have installed a singularity desktop before, please do the uninstaller first and then install.

## 3. Download XQuarz on your Mac

(https://www.xquartz.org/index.html)


## 4. Check whether XQuarz on your computer is properly installed

If this is your first time to install XQuartz, you need to log out and log in after your installation to update your DISPLAY environment.

Then type

```
> xeyes
```
in your terminal to see whether you can see a pair of eyes jumping up. If yes, then this means the XQuarz is fully installed.

If you cannot see the cute eyes (the tiny eye balls would move according to the movement of your mouse!), do check the installation of XQuartz on your Mac.

After that, **go to XQuartz preferences, under the Security tap, check the box that says: *Allow connections from network clients**

Restart XQuartz

## 5. Run the following two commands

```
> export DISPLAY=:0
> /opt/X11/bin/xhost +
```

## 6. Test the container

```
> singularity shell PSRSOFT_yhy.simg
> cd /PSRSOFT/pgplot/
> ./pgdemo1
```

You should see a series of pop-up images now.


