# PRESTO 3.0.1 SINGULARITY CONTAINER

## HOW TO DOWNLOAD THE CONTAINER

Download it from my personal website

```
wget http://haoyangye.com/presto_yhy.simg
```

I am also looking into **Push it to Singularity library**

You can issue a problem here or contact me *HAOYANG.YE AT CANTAB.NET*


## HOW TO USE THE CONTAINER

Download presto_yhy.simg 

Put it somewhere on your computer.

Create a presto_simg.sh as below:

```
#!/bin/bash
#ENVIRONMENT VARIABLES
export ASTROSOFT=/PSRSOFT

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

#YOUR COMMANDS FOR PRESTO HERE
readfile GBT_Lband_PSR.fil 
rfifind -time 2.0 -o Lband GBT_Lband_PSR.fil 
```

You may want to change the last two lines, to inform presto the location of the file GBT_Lband_PSR.fil

IF you want to obtain this file for test, download it by
```
wget http://www.cv.nrao.edu/~sransom/GBT_Lband_PSR.fil
```

Then exec the singularity image file as:

```
singularity exec presto_yhy.simg sh presto_simg.sh
```

You can add more commands to use presto by appending the .sh file. 

For more presto tutorial, tune in https://www.cv.nrao.edu/~sransom/PRESTO_search_tutorial.pdf


