This file explains how I installed singularity, then presto, and finally create a .simg using the sandbox

# 1.Install Singularity

*Followed the instructions on* (https://sylabs.io/guides/3.6/user-guide/quick_start.html#quick-installation-steps)

## 1.1 Install packages that Singularity relies on

Firstly, install the following packages that Singularity relies on:

(aptitude can be replaced by apt, or apt-get)

```
$ sudo aptitude update && sudo aptitude install -y \
    build-essential \
    uuid-dev \
    libgpgme-dev \
    squashfs-tools \
    libseccomp-dev \
    wget \
    pkg-config \
    git \
    cryptsetup-bin
    
```

## 1.2 Install Go Language

Next, install Go. 

This is how I installed it:

```
$ export VERSION=1.14.4 OS=linux ARCH=amd64 && \
    wget https://dl.google.com/go/go1.14.4.linux-amd64.tar.gz && \
    sudo tar -C /usr/local -xzvf go1.14.4.linux-amd64.tar.gz 
```

You can download the newest version of Go and change the commands above accordingly. 

Add /usr/local/go/bin to the PATH environment variable. 
You can do this by adding this line to your /etc/profile (for a system-wide installation) or $HOME/.profile:

```
$ export PATH=$PATH:/usr/local/go/bin
```

Then run the shell commands directly or execute them from the profile using a command such as source $HOME/.profile.

Before I move on with singularity, I want to test Go installation:
(https://golang.org/doc/install?download=go1.14.4.linux-amd64.tar.gz)
Test your installation

Check that Go is installed correctly by building a simple program, as follows.
Create a file named hello.go that looks like:

```
package main

import "fmt"

func main() {
        fmt.Printf("hello, world\n")
}
```

Then build it with the go tool:
```
$ go build hello.go
```

The command above will build an executable named hello in the current directory alongside your source code. Execute it to see the greeting:
```
$ ./hello
hello, world
```

If you see the "hello, world" message then your Go installation is working.

(*I have got errors to build the .go file, only because I used single quotation mark for fmt;
the error information is*
```
can't load package: package main: 
hello.go:3:8: illegal rune literal
see: https://socketloop.com/tutorials/illegal-rune-literal
```
)

## 1.3 Install Singularity:

Download Singularity from a GitHub release

You can download Singularity from one of the releases. To see a full list, visit the GitHub release page. 

```
$ export VERSION=3.5.2 && # adjust this as necessary \
    wget https://github.com/sylabs/singularity/releases/download/v3.5.3/singularity-3.5.3.tar.gz && \
    tar -xzf singularity-3.5.3.tar.gz && \
    cd singularity
```

Compile the Singularity source code

Now you are ready to build Singularity. Dependencies will be automatically downloaded. You can build Singularity using the following commands:

```
$ ./mconfig && \
    make -C builddir && \
    sudo make -C builddir install
```

**Singularity must be installed as root to function properly**.

## 1.4 Play with Singularity

You may want to have a play with the newly installed singularity!

Including using the existing images from the Singularity library

Following the instructions on (https://sylabs.io/guides/3.5/user-guide/quick_start.html) to have a play with lolcow.

Some fun stuff like this:

```
$ singularity run lolcow_latest.sif
 _____________________________________
/ You have been selected for a secret \
\ mission.                            /
 -------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||

$ ./lolcow_latest.sif
 ____________________________________
/ Q: What is orange and goes "click, \
\ click?" A: A ball point carrot.    /
 ------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||

```

# 2.Build your own container with sandbox -- a short intro

Singularity v3.0 and above produces immutable images. This ensures reproducible and verifiable images and allows for many extra benefits such as the ability to sign and verify your containers.
However, during testing and debugging you may want an image format that is writable. This way you can shell into the image and install software and dependencies until you are satisfied that your container will fulfill your needs. For these scenarios, Singularity also supports the sandbox format (which is really just a directory).
Sandbox Directories

To build into a sandbox (container in a directory) use the build --sandbox command and option:

```
$ sudo singularity -d build --sandbox ubuntu/ docker://ubuntu
```

This helps me to build a container on top of a fresh ubuntu

Run this container (or .simg) on top of the fresh ubuntu, I need to use the option '--writable':

```
$ sudo singularity shell --writable ubuntu/
```

Now try to install some packages to this container:

```
Singularity ubuntu:~> apt-get update
...

Singularity ubuntu:~> apt-get install vim
...
```

Then exit the container by 

```
Singularity ubuntu:~> exit
```

Use 'build' to create your own new .simg file

```
$ sudo singularity build ubuntu-vim.simg ubuntu/
```

Now you can have a test:

```
$ sudo singularity shell ubuntu-vim.simg

Singularity ubuntu-vim.simg:~> vim 
```

Vim should already been installed


# 3.Install Python3, pip3 and matplotlib (to make you familiar with your container)

If Ubuntu doesn't have python3 installed (very unlikely), you can follow this link to install Python3:
(https://phoenixnap.com/kb/how-to-install-python-3-ubuntu)

**Install pip3** (https://linuxize.com/post/how-to-install-pip-on-ubuntu-18.04/)

```
$ apt-get install python3-pip
```

And then use pip3 to install matplotlib

```
$ pip3 install --upgrade matplotlib
```

how to test: 
try 
```
$ python3
...
```

Under the python environment 

```
import matplotlib 
```

Of course, now you can build another container to test whether the newly installed packages are there.


# 4.Install PRESTO v3.0.1 with python3

*Followed the instructions on* (https://github.com/scottransom/presto/blob/master/INSTALL) 

*as well as* (http://www.ljtwebdevelopment.com/pulsarref/)


Enter Singularity by

```
$ sudo singularity shell --writable ubuntu/
```

## 4.1 Install some relavent packages

```
apt-get install ftp wget csh git autoconf
apt-get install gfortran libx11-dev
```

## 4.2 Install FFTW


Make a directory called PSRSOFT somewhere you feel comfortable with. 

**This directory will be copied to root directory anyway when we build the final container**


```
mkdir PSRSOFT
cd PSRSOFT
wget http://www.fftw.org/fftw-3.3.8.tar.gz
tar -xvf fftw-3.3.8.tar.gz
cd fftw-3.3.8
```

**single precision**

```
./configure --prefix=$ASTROSOFT --enable-float --enable-threads --enable-shared CFLAGS=-fPIC FFLAGS=-fPIC
make
make check
make install
make clean
```

SET ENVIRONMENT VARIABLES:

```
export ASTROSOFT=/path/to/your/PSRSOFT

# OSTYPE
export OSTYPE=linux

# PGPLOT
export PGPLOT_DIR=$ASTROSOFT/pgplot
export PGPLOT_DEV=/PS

#TEMPO
export TEMPO=$ASTROSOFT/tempo

# PRESTO
export PRESTO=$ASTROSOFT/presto

# LD_LIBRARY_PATH
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib:/usr/lib64:$PGPLOT_DIR:$ASTROSOFT/lib:$PRESTO/lib:$ASTROSOFT/fftw-3.3.8/lib:$ASTROSOFT/cfitsio-3.48/lib:
# PATH
# Some Presto executables match sigproc executables so keep separate -
# all other executables are found in $ASTROSOFT/bin
export PATH=$PATH:$ASTROSOFT/bin:$PRESTO/bin:$PGPLOT_DIR
# Python path
export PYTHONPATH=$PRESTO/lib/python
```

*You don't need to put it in your .bashrc or other bash files.*


## 4.3 Install PGPLOT

```
wget ftp://ftp.astro.caltech.edu/pub/pgplot/pgplot5.2.tar.gz
tar -zxvf pgplot5.2.tar.gz
mv pgplot pgplot_source
mkdir pgplot
cp pgplot_source/drivers.list pgplot/
cd pgplot
vim drivers.list
```

Open drivers.list and delete the '!' in front of the drivers of PS, Xwindow, VPS, CPS, VCPS

```
../pgplot_source/makemake ../pgplot_source linux g77_gcc_aout
```

Edit the makefile with the following:

Set the fortran compiler to gfortran, and append FFLAGC and CFLAGC with -fPIC e.g:

```
FCOMPL=gfortran
FFLAGC=-fPIC -Wall -O
CFLAGC=-fPIC -DPG_PPU -O2 -I.
```

Save the file and compile it:

```
make
make clean
make cpg
ld -shared -o libcpgplot.so --whole-archive libcpgplot.a
```

Try the PGPLOT demo:

```
./pgdemo1
```


Press enter at the prompt. 
If you choose /Xwindow, you should see a prompt window of plots.
If you choose /PS, you can find new .ps file in your directory.

**This step is very important, as Presto will output image files in .ps format.**

## 4.4 Install TEMPO

```
git clone git://git.code.sf.net/p/tempo/tempo
cd tempo
./prepare
./configure F77=gfortran --prefix=$ASTROSOFT CFLAGS=-fPIC FFLAGS=-fPIC
make
make install
```

## 4.5 Install GLIB

```
apt-get install libglib2.0-dev
```

## 4.6 Install CFITSIO

```
wget http://heasarc.gsfc.nasa.gov/FTP/software/fitsio/c/cfitsio-3.48.tar.gz
tar -xvzf cfitsio-3.48.tar.gz
cd cfitsio-3.48
./configure --prefix=$ASTROSOFT CFLAGS=-fPIC FFLAGS=-fPIC
make shared
make install
make clean
```

## 4.7 Install PRESTO

```
git clone https://github.com/scottransom/presto.git
cd PRESTO/src
```

Edit Makefile as follows:

a) Define paths to fftw and cfitsio includes and libs

```
FFTINC = -I$(ASTROSOFT)/include
FFTLINK = -L$(ASTROSOFT)/lib -lfftw3f
CFITSIOINC = -I$(ASTROSOFT)/include
CFITSIOLINK = -L$(ASTROSOFT)/lib -lcfitsio
```

b) Add '-lm' flag to CFLAGS

c) Add '-g -fPIC' to FFLAGS

Continue the build:

```
make makewisdom 
make prep
make
make clean
```

Now for the python:

```
cd $PRESTO
vim setup.py
```

Edit the setup.py:

```
extra_compile_args = ["-DUSEFFTW -fPIC"]
include_dirs = ["/root/PSRSOFT/include"]
ppgplot_libraries = ["cpgplot", "pgplot", "X11", "png", "m", "gfortran"]
ppgplot_library_dirs = ["/root/PSRSOFT/pgplot"]
presto_libraries = ["presto", "fftw3f", "m"]
presto_library_dirs = ["/root/PSRSOFT/lib","/root/PSRSOFT/presto/lib"]
```

After this:

```
python tests/test_presto_python.py
```

You will see a series of successes!!!
Yeah!!!

OK, you may want to test your presto now:

```
cd $PRESTO/bin
readfile
rfifind
```

At least the system should be able to find these commands.
If the system cannot find them, 
try 

```
$ echo $ASTROSOFT
```

It should points to /PSRSOFT, if not, set the environment variable again.

You can also try your own data with the readfile/rfifind commands

Now, just move the whole /PSRSOFT to /

```
mv PSRSOFT /
```

Done :+1:

# 5. Build PRESTO singularity box

```
sudo singularity build presto-yhy.simg ubuntu/
```

*This container is no longer provided.*

# 6. Add more Pulsar software to the singularity container

Well, PRESTO itself seems not enough for me to find pulsars/FRBs, so I am installing the following software:

- PSRCHIVE
- SIGPROC
- DSPSR
- PSRCAT

Download the source files first:

```
git clone git://git.code.sf.net/p/psrchive/code psrchive
git clone https://github.com/SixByNine/sigproc.git
git clone git://git.code.sf.net/p/dspsr/code dspsr
wget https://www.atnf.csiro.au/people/pulsar/psrcat/downloads/psrcat_pkg.tar.gz
```

## 6.1 Install PSRCAT

The easist one is PSRCAT. You can refer to instructions on (https://www.atnf.csiro.au/people/pulsar/psrcat/download.html)

```
gunzip psrcat_pkg.tar.gz
tar -xvf psrcat_pkg.tar
cd psrcat_tar
source makeit
cp psrcat $ASTROSOFT/bin
```

You then need to add the path for the .bashrc file

```
export PSRCAT_RUNDIR=$ASTROSOFT/psrcat_tar
export PSRCAT_FILE=$ASTROSOFT/psrcat_tar/psrcat.db
```

Test can be found at (https://www.atnf.csiro.au/people/pulsar/psrcat/download.html).

## 6.2 Install PSRCHIVE

Firtly, need GNU_Build System, see (http://psrchive.sourceforge.net/third/autotools/)

Download script for GNU_Build:

```
#!/bin/bash


export mirror=http://ftp.gnu.org/gnu


echo downloading from $mirror


echo downloading m4 ...
curl -O -f -L $mirror/m4/m4-1.4.6.tar.gz


echo downloading autoconf ...
curl -O -f -L $mirror/autoconf/autoconf-2.60.tar.gz


echo downloading automake ...
curl -O -f -L $mirror/automake/automake-1.9.6.tar.gz


echo downloading libtool ...
curl -O -f -L $mirror/libtool/libtool-1.5.8.tar.gz
```

Install script:

```
#! /bin/bash 
  
if ( $?ASTROSOFT ) then
  export prefix=$ASTROSOFT/packages/$LOGIN_ARCH
else
  export prefix=/usr/local
endif
alias rehash='hash -r'
alias MAKE="./configure --prefix=$prefix; make; make install; rehash; cd .."

echo installing in $prefix

export PATH=${prefix}:${PATH}

echo unpacking m4 ...
gunzip -c m4-1.4.6.tar.gz | tar xf -
cd m4-1.4.6
MAKE

echo unpacking autoconf ...
gunzip -c autoconf-2.60.tar.gz | tar xf -
cd autoconf-2.60
MAKE

gunzip -c automake-1.9.6.tar.gz | tar xf -
cd automake-1.9.6
MAKE

gunzip -c libtool-1.5.8.tar.gz | tar xf -
cd libtool-1.5.8
MAKE
```

Now, it's time to install PSRCHIVE!!

```
git clone git://git.code.sf.net/p/psrchive/code psrchive
cd psrchive
./bootstrap
./configure
make
make install
make clean
```

After "./configure”, it told me that I haven’t installed the TEMPO2, so I just installed it as directed — super easy!
It told me that TEMPO2 was succuessfully installed, then do "./configure” again

The 'make' and 'make install' took me a while. 


## 6.3 Install SIGPROC

The SourceForge distribution didn't work for me. So I had to turn to Mike Keith's.

```
cd sigproc
./bootstrap
./configure --prefix=$PSRSOFT_DIR/sigproc F77=gfortran CFLAGS=-fPIC FFLAGS=-fPIC
make
make install
make clean
```

When 'make', if there is an error saying it cannot find -l(xxxx), then try to locate the lib(xxxx).so file by

```
find /usr/ -iname lib(xxxx).so
```

and then copy it to /usr/lib/, the error should be solved.

However, after the installation with no errors reported, I still cannot run the famous "filterbank" command. I consider this an unsuccessful installation.

## 6.4 Install DSPSR

```
cd dspsr
vi backends.list

##insert
##apsr asp bcpm bpsr caspsr cpsr2 cpsr dummy fits gmrt guppi kat lbadr64 lbadr lump lwa mark4 mark5 maxim mwa pdev pmdaq s2 sigproc spda1k spigot vdif##

./configure --prefix=$PSRSOFT_DIR/sigproc F77=gfortran CFLAGS=-fPIC FFLAGS=-fPIC

make
make install
make clean

# DSPSR
export PATH=/sigproc/bin:$PATH
```

This DSPSR actually helped me to install sigproc successfully. I am happy.

You can test it by typing 

```
dspsr
```

or

```
filterbank
```


# 7. Build PRESTO singularity box

```
sudo singularity build PSRSOFT_yhy.simg ubuntu/
```

Now you can follow the README in this github to use the .simg
