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
access control disabled, clients can connect from any host
```
If you get an error instead, **try log out and log in again**, the problem should be solved -- yes, XQuartz is not very stable on Mac (don't know why)

The error message can be: 

```
xhost: unable to open display ":0"
```

or

```
No protocol specified
xhost: unable to open display ":0"
```

## 6. Test the container

```
> singularity shell PSRSOFT_yhy.simg
> (Set path parameters)
> cd /PSRSOFT/pgplot/
> ./pgdemo1
```

**You should see a series of pop-up images now.

The detailed path parameters are:

```
#!/bin/bash
export ASTROSOFT=/PSRSOFT

# OSTYPE
export OSTYPE=linux

# PGPLOT
export PGPLOT_DIR=$ASTROSOFT/pgplot
export PGPLOT_DEV=/xw

#TEMPO
export TEMPO=$ASTROSOFT/tempo

#TEMPO2
export TEMPO2=/usr/local/tempo2

# PRESTO
export PRESTO=$ASTROSOFT/presto

# PSRCAT
export PSRCAT_RUNDIR=$ASTROSOFT/psrcat_tar
export PSRCAT_FILE=$ASTROSOFT/psrcat_tar/psrcat.db

# DSPSR
export PATH=/sigproc/bin:$PATH

# LD_LIBRARY_PATH
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib:/usr/lib64:$PGPLOT_DIR:$ASTROSOFT/lib:$PRESTO/lib:$ASTROSOFT/fftw-3.3.8/lib:$ASTROSOFT/cfitsio-3.48/lib:
# PATH
# Some Presto executables match sigproc executables so keep separate -
# all other executables are found in $ASTROSOFT/bin
export PATH=$PATH:$ASTROSOFT/bin:$PRESTO/bin:$PGPLOT_DIR
```

